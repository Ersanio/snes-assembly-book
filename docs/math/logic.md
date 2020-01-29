# Bitwise operations
Bitwise operations are fundamental when it comes to assembly. The 65c816 supports several bitwise opcodes, which are explained in this chapter. Bitwise operators mainly work on bits rather than bytes, so in this chapter, vague terms such as `x` and `y` refer to bits rather than bytes.

In this chapter, we will sometimes refer to binary value 1 as `true` and binary value 0 as `false`.

## AND
AND is an opcode which affects the accumulator by applying a logical AND on all bits.

|Opcode|Full name|Explanation|
|-|-|-|
|**AND**|Logical AND|Logical AND, also known as a "`&`" in some other programming languages|

The result of `x AND y` is `true` if both `x` and `y` evaluate to `true`. Otherwise, the result is `false`.

Here's an example of a logical AND.
```
LDA #$F0    ; A = 1111 0000
AND #$98    ; AND 1001 1000
            ; A = 1001 0000 = $90
```
To understand this code, you'll have to read the comments from top to bottom. As you can see, the first line contains the accumulator's binary value, while the second line contains the AND operator's parameter binary value. When two 1 come together, the result is a 1 as well. This is what it means when both x and y evaluate to true. If x = 1 and y = 1, then the result is 1 also.

This rule can be written in a "truth table".
|Compared bit|AND operation|Result|
|-|-|-|
|1|1|1|
|0|1|0|
|1|0|0|
|0|0|0|

In short, whenever there's a 0 in A or in the AND value's bit, the resulting bit is also 0.

## ORA
ORA is an opcode which affects the accumulator by applying a logical ORA on all bits.

|Opcode|Full name|Explanation|
|-|-|-|
|**ORA**|Logical OR|Logical OR, also known as a "`|`" in some other programming languages|

The result of `x OR y` is true if either `x` or `y` evaluates to `true`. Otherwise, the result is `false`.

Here's an example of an ORA:
```
LDA #$F0    ; A = 1111 0000
ORA #$87    ; ORA 1000 0111
            ; A = 1111 0111 = $F7
```
If one of the bits has 1, the resulting bit will be 1. After ORA, the result will be stored in A. Here's the truth table for ORA:
|Compared bit|OR operation|Result|
|-|-|-|
|1|1|1|
|0|1|1|
|1|0|1|
|0|0|0|
So basically, whenever A or the ORA value’s bit is 1, the resulting bit is also 1.

## EOR
EOR is an opcode which affects the accumulator by applying a logical exclusive OR (XOR) on all bits.

|Opcode|Full name|Explanation|
|-|-|-|
|**EOR**|Logical XOR|Logical XOR, also known as a "`^`" in some other programming languages|

The result of `x XOR y` is `true` if `x` evaluates to `true` and `y` evaluates to `false`, or `x` evaluates to `false` and `y` evaluates to `true`. Otherwise, the result is `false`.

Here's an example of an XOR:
```
LDA #$99    ; A = 1001 1001
EOR #$F0    ; EOR 1111 0000
            ; A = 0110 1001 = $69
```
Basically, when the accumulator and the given value's bits are both 1, the result is 0. When they're both 0, the result is 0. When they're not equal, then the result is 1 also. Here's the truth table for EOR:
|Compared bit|XOR operation|Result|
|-|-|-|
|1|1|0|
|0|1|1|
|1|0|1|
|0|0|0|

## BIT
BIT is an opcode which essentially does a logical AND, but the result is NOT stored in the accumulator. Instead, it only affects the processor flags.

|Opcode|Full name|Explanation|
|-|-|-|
|**BIT**|Bit test|Tests certain bits of the accumulator or memory|

Here's an example of BIT in usage:
```
LDA #$04	; A = 0000 0100 = $04
BIT #$00	; AND 0000 0000. None of the bits are set
            ; so normally, A should be $00, but it's still $04
            ; However, the zero flag has been set because
            ; the outcome *should* be $00.
```

BIT has a feature which distinguishes it from a regular AND, involving processor flags other than the zero flag. When you use BIT on an address, rather than the accumulator, it can also affect the 'negative' and 'overflow' processor flags. If bit 7 of the address' value is set, the negative flag would be set as a result. If bit 6 of the address' value is set, the overflow flag would be set as a result. Here's an example of using BIT on an address:

```
BIT $04     ; Bit test $7E0004's value
```

If $7E0004’s value was $80 (1000 0000), the negative flag would be set and the overflow flag would be clear. It’s useful to check really fast if the value of an address is negative.

If $7E0004’s value was $40 (0100 0000), the negative flag would be clear and the overflow flag would be set. Useful to check if specifically bit 6 is set.

If $7E0004’s value was $C0 (1100 0000), both the negative and overflow flags would be set.

Coincidentally enough, the bits for negative (bit 7) and overflow (bit 6) correspond to the bits in the processor flag register: `nvmxdizc`.

|Set bits|BIT result
|-|-|-|
|Bit 7|Negative flag set
|Bit 6|Overflow flag set
|Bits 7, 6|Negative & overflow flags set

When you are performing a BIT operation on a RAM address, the N and V flags will be set or cleared, regardless of the value in the accumulator. The zero flag depends on the accumulator’s value and the RAM address’ value. So, BIT with a RAM address does both AND, and an inevitable check of bits 7 and 6 of the RAM address.