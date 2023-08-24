1 byte = 8 bits (octet)  
unsigned integer (uint)  
When an integer is signed, the first bit will be used to store whether the integer is a positive or negative integer.

One char in ASCII - 1 byte, Unicode 1-4;
So how many bytes give access to what characters in these encodings?

UTF-8:

    1 byte: Standard ASCII
    2 bytes: Arabic, Hebrew, most European scripts (most notably excluding Georgian)
    3 bytes: BMP
    4 bytes: All Unicode characters

UTF-16:

    2 bytes: BMP
    4 bytes: All Unicode characters

Ranges for Different Types of Integers

```
Signed   8-bit   -128 through 127  2^7
Unsigned 8-bit   0 through 255  2^8
Signed   16-bit  -32,768 through 32,767 2^15
Unsigned 16-bit  0 through 65,535 2^16
Signed   32-bit  -2,147,483,648 through 2,147,483,647  2^31
Unsigned 32-bit  0 through 4,294,967,295 2^32
Signed   64-bit  -9,223,372,036,854,775,808 through 9,223,372,036,854,775,807 2^64
Unsigned 64-bit  0 through 18,446,744,073,709,551,615
```

binary to decimal representation

```
// No bits set => zero
00000000 = 0
// Increment least significant bit
00000001 = 1
// Least significant bit exceeds max, so we reset it and increment bit left of it
00000010 = 2
00000011 = 3
00101010 = 42 -> 1*2^5 + 0 + 1*2^3 + 0 + 1*2^1 + 0;
11111111 = 255
```

```php
<?php
$a = 1234; // decimal number
$a = 0123; // octal number (equivalent to 83 decimal)
$a = 0o123; // octal number (as of PHP 8.1.0)
$a = 0x1A; // hexadecimal number (equivalent to 26 decimal)
$a = 0b11111111; // binary number (equivalent to 255 decimal)
$a = 1_234_567; // decimal number (as of PHP 7.4.0)
?>
```