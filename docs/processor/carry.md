# Carry flag
The SNES supports math in the form of adding and subtracting numbers. It also supports bitwise operations such as bitshifting. Finally, the SNES supports logical operations such as a logical AND or XOR.

The “carry flag” is a processor flag used for most of these arithmetic and bitshifting operations. Additionally, the carry flag is also used for branching. The carry flag is the same concept as the "carry" you learn in elementary school. In a typical pencil-and-paper addition, you'd write it out like this:
```
  ¹
  27
+ 59
----
  86
 ```
7+9 equals 16, thus *carry* the 1 to the left.

Considering the carry is a "flag", when the carry flag is clear, the carry (C) will be 0. When it's set, the carry will be 1. You can safely assume that the carry flag is the “9th bit” of the A register when A is in 8-bit mode, and the “17th bit” when A is in 16-bit mode. Assuming A is in 8-bit mode, the carry flag will look like this:
```
BBBBBBBB C
```
Where C is the Carry Flag and B are the bits – in other words the A register's contents.

## SEC and CLC
There are two instructions to alter the carry flag directly without loading or storing or calculating anything:

|Opcode|Full name|Explanation|
|-|-|-|
|**SEC**|Set carry flag|Sets the carry flag, setting it to 1|
|**CLC**|Clear carry flag|Clears the carry flag, setting it to 0|

Depending on the carry flag, various mathematical instructions will behave different. The following chapters demonstrate this.