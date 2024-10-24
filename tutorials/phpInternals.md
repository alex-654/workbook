### php internals

- Переменные php представлены в движке C (zend engine) структурой zval.
- the type can change during the life of a zval
- variable types are only known at run-time and not at compile-time
- zval stores some value and the type this value has
- Simple values like integers, which are stored directly inside the zval, and complex values like strings, for which the
  zval only stores a pointer to a separate structure.

All complex values share a common header with the following structure:

```c
typedef struct _zend_refcounted_h {
  uint32_t refcount;
  union {
      uint32_t type_info;
  } u;
} zend_refcounted_h;
```

This header stores a reference count, which tracks in how many places this structure is used. If the structure is used
in a new zval, the refcount is incremented. If it stops being used, it is decremented. If the reference count reaches
zero, we know that the structure is no longer used and can be freed. This is the core mechanism of PHP's memory
management.

### Zval memory management

Zvals themselves are never individually heap-allocated. They are either allocated temporarily on the stack, or embedded
as part of a larger heap-allocated structure.

### copy-on-write concept

In PHP, all values follow by-value semantics by default.
You are only permitted to modify structures that you exclusively own, which means that they must have a refcount of one.
If a structure has a refcount greater than one, it needs to be separated first. Separation is just a fancy word for
duplicating the structure.

### Zend Memory Manager

Zend Memory Manager (ZendMM or ZMM) - php own layer over the traditional libc's dynamic memory allocator.  
While zvals themselves are generally not shared, it's possible to share the structures they point to using the
refcounting mechanism.

In CLI php create separate process for each command. On web one process can handle a 1000 of requests.
Opcache useful for web (php-fpm). It can store in memory static information from different requests.

There exists two kinds of dynamic memory allocations while programming PHP Core or extensions:

- Request-bound dynamic allocations.
- Permanent dynamic allocations.

In C, the library function malloc is used to allocate a block of memory on the heap.
Free store (informally called the "heap").

### Zvals in PHP

When a zval is used in a new place, previously (php 5) this meant copying a zval* and incrementing its refcount. Now it
means copying the contents of a zval (ignoring u2) instead and maybe incrementing the refcount of the value it points
to, if said value uses refcounting.

```
struct _zval_struct {
    zend_value value; // value
    union {
        struct {
            ZEND_ENDIAN_LOHI_4(
                zend_uchar type,
                zend_uchar type_flags,
                zend_uchar const_flags,
                zend_uchar reserved)
        } v;
        uint32_t type_info;
    } u1;
    union {
        uint32_t var_flags;
        uint32_t next;                 // hash collision chain
        uint32_t cache_slot;           // literal cache slot
        uint32_t lineno;               // line number (for ast nodes)
        uint32_t num_args;             // arguments number for EX(This)
        uint32_t fe_pos;               // foreach position
        uint32_t fe_iter_idx;          // foreach iterator index
    } u2;
};
```

```
typedef union _zend_value {
    zend_long         lval;
    double            dval; // directly
    zend_refcounted  *counted; // counter of value and some info for GC
    zend_string      *str;  // pointer
    zend_array       *arr;
    zend_object      *obj;
    zend_resource    *res;
    zend_reference   *ref;
    zend_ast_ref     *ast;

    // Ignore these for now, they are special
    zval             *zv;
    void             *ptr;
    zend_class_entry *ce;
    zend_function    *func;
    struct {
        ZEND_ENDIAN_LOHI(
            uint32_t w1,
            uint32_t w2)
    } ww;
} zend_value;
```

```
struct _zend_refcounted {
    uint32_t refcount;
    union {
        struct {
            ZEND_ENDIAN_LOHI_3(
                zend_uchar    type,
                zend_uchar    flags,
                uint16_t      gc_info)
        } v;
        uint32_t type_info;
    } u;
};
```

The gc_info is the equivalent of the buffered entry in the old zvals. However instead of storing a pointer into the root
buffer it now contains an index into it. Because the root buffer has a fixed size (10000 elements) it is enough to use a
16 bit number for this instead of a 64 bit pointer. The gc_info info also encodes the “color” of the node, which is used
to mark nodes during collection.

### Zval memory management

Zvals in PHP 5 are (with a few exceptions) allocated on the heap and PHP needs some way to keep track which zvals are
currently in use and which should be freed. For this purpose reference counting is employed:

A hashtable bucket will directly embed a zval instead of storing a pointer to a separate zval. The compiled variables
table of a function or the property table of an object will be zval arrays that are allocated in one chunk, instead of
storing pointers to separate zvals. As such zvals are now usually stored with one level of indirection less. What was
previously a zval* is now a zval.

As integers are no longer shared, both variables will use separate zvals.

```php
$a = 42;   // $a = zval_1(type=IS_LONG, value=42)

$b = $a;   // $a = zval_1(type=IS_LONG, value=42)
           // $b = zval_2(type=IS_LONG, value=42)

$a += 1;   // $a = zval_1(type=IS_LONG, value=43)
           // $b = zval_2(type=IS_LONG, value=42)

unset($a); // $a = zval_1(type=IS_UNDEF)
           // $b = zval_2(type=IS_LONG, value=42)

```

```php
$a = [];   // $a = zval_1(type=IS_ARRAY) -> zend_array_1(refcount=1, value=[])

$b = $a;   // $a = zval_1(type=IS_ARRAY) -> zend_array_1(refcount=2, value=[])
           // $b = zval_2(type=IS_ARRAY) ---^

// Zval separation occurs here
$a[] = 1   // $a = zval_1(type=IS_ARRAY) -> zend_array_2(refcount=1, value=[1])
           // $b = zval_2(type=IS_ARRAY) -> zend_array_1(refcount=1, value=[])

unset($a); // $a = zval_1(type=IS_UNDEF) and zend_array_2 is destroyed
           // $b = zval_2(type=IS_ARRAY) -> zend_array_1(refcount=1, value=[])

```

### Garbage collector

if variable is big we can do unset() to free memory and do not wait for garbage collector.

This means that garbage cycles can only be created when a refcount argument is decreased to a non-zero value.
And then this variable (possible roots zvals) goes to root buffer. fixed size of 10,000 roots.

### References (aka &)

Copy-on-write matches by-value passing semantics.
References disabled it.

Because zvals are no longer individually allocated, it is not possible to use the same approach that PHP 5 used. Instead
a new IS_REFERENCE type is added, which uses the zend_reference structure as its value:

```c
struct _zend_reference {
    zend_refcounted   gc;
    zval              val;
};
```

```php
$a = [];  // $a         -> zend_array_1(refcount=1, value=[])
$b = $a;  // $a, $b,    -> zend_array_1(refcount=2, value=[])
$c = $b   // $a, $b, $c -> zend_array_1(refcount=3, value=[])

$d =& $c; // $a, $b                                 -> zend_array_1(refcount=3, value=[])
          // $c, $d -> zend_reference_1(refcount=2) ---^
          // Note that all variables share the same zend_array, even though some are
          // PHP references and some aren't.

$d[] = 1; // $a, $b                                 -> zend_array_1(refcount=2, value=[])
          // $c, $d -> zend_reference_1(refcount=2) -> zend_array_2(refcount=1, value=[1])
          // Only at this point, once an assignment occurs, the zend_array is duplicated.
```

The important difference to PHP 5 is that all variables were able to share the same array, even though some were PHP
references and some weren't. Only once some kind of modification is performed the array will be separated. This means
that in PHP 7 it's safe to pass a large, referenced array to count(), it is not going to be duplicated. References will
still be slower than normal values, because they require allocation of the zend_reference structure (and indirection
through it) and are usually not handled in the fast-path of engine code.

### takeaway

the main difference (php 5 vs 7) was that zvals are no longer individually allocated and don't store a reference count
themselves.
Simple values like integers or floats can be stored directly in a zval, while complex values are represented using a
pointer to a separate structure.

### FastCGI Process Manager (FPM)

**FastCGI** is a binary protocol for interfacing interactive programs with a web server (Nginx, Apache etc.)
FPM (FastCGI Process Manager) is a primary PHP FastCGI implementation.

user -> nginx -> php-fpm -> php workers -> nginx -> user

### php Hashtable

The concept behind a hashtable is very simple: The string key is run through a hashing function, which returns an
integer. This integer is then used as an index into a “normal” array. The problem is that two different strings can
result in the same hash, as the number of possible strings is virtually infinite while the hash is limited by the
integer size. As such hashtables need to implement some kind of collision resolution mechanism.

**Collision resolution**: Open addressing, where elements will be stored at a different index if a collision occurs, and
chaining, where all elements hashing to the same index are stored in a linked list

The elements in the “collision resolution” chain are referred to as “buckets”.
A bucket is an entry in the hashtable

```c
typedef struct _HashTable {
	uint32_t          nTableSize;
	uint32_t          nTableMask;
	uint32_t          nNumUsed;
	uint32_t          nNumOfElements;
	zend_long         nNextFreeElement;
	Bucket           *arData; // traverse/iterating data
	uint32_t         *arHash; // lookup for key
	dtor_func_t       pDestructor;
	uint32_t          nInternalPointer;
	union {
		struct {
			ZEND_ENDIAN_LOHI_3(
				zend_uchar    flags,
				zend_uchar    nApplyCount,
				uint16_t      reserve)
		} v;
		uint32_t flags;
	} u;
} HashTable;
```

#### iterating

iterating an array looks roughly as follows

```c
uint32_t i;
for (i = 0; i < ht->nNumUsed; ++i) {
	Bucket *b = &ht->arData[i];
	if (Z_ISUNDEF(b->val)) continue;

	// do stuff with bucket
}
```

This corresponds to a linear scan of memory, which is much more cache-efficient than a linked list traversal (where you
go back and forth between relatively random memory addresses).

#### lookup

In code, the lookup mechanism looks like this:

```c
zend_ulong h = zend_string_hash_val(key);
uint32_t idx = ht->arHash[h & ht->nTableMask]; // h & ht->nTableMask -> hash % ht->nTableSize
while (idx != INVALID_IDX) {
	Bucket *b = &ht->arData[idx];
	if (b->h == h && zend_string_equals(b->key, key)) {
		return b;
	}
	idx = Z_NEXT(b->val); // b->val.u2.next (b->val = zval struct)
}
return NULL;
```

### Packed hashtables

continuous, integer-indexed arrays (i.e. real arrays)
The packed hashtable optimization only works if keys are in ascending order. There can be gaps in between them (the keys
don’t have to be continuous), but they need to always increase. 