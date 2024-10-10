
### php internals

- Переменные php представлены в движке C (send engine) структурой zval.
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
zero, we know that the structure is no longer used and can be freed. This is the core mechanism of PHP’s memory
management.

### Zval memory management

Zvals themselves are never individually heap-allocated. They are either allocated temporarily on the stack, or embedded
as part of a larger heap-allocated structure.

#### copy-on-write concept

In PHP, all values follow by-value semantics by default.
You are only permitted to modify structures that you exclusively own, which means that they must have a refcount of one.
If a structure has a refcount greater than one, it needs to be separated first. Separation is just a fancy word for
duplicating the structure.

### Zend Memory Manager

Zend Memory Manager (ZendMM or ZMM) php own layer over the traditional libc’s dynamic memory allocator