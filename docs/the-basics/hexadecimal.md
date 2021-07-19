# Hexadecimal

To program in 65c816 ASM, you will need to grasp the basics of hexadecimal. Hexadecimal, also known as "hex", is a counting system much like decimal, which is the everyday counting system people use. In hexadecimal, there are additional 6 digits per place value, which are denoted through the values A-F, as seen in the table below.

| Decimal | Hexadecimal |
| :--- | :--- |
| 0 | 0 |
| 1 | 1 |
| 2 | 2 |
| 3 | 3 |
| 4 | 4 |
| 5 | 5 |
| 6 | 6 |
| 7 | 7 |
| 8 | 8 |
| 9 | 9 |
| 10 | A |
| 11 | B |
| 12 | C |
| 13 | D |
| 14 | E |
| 15 | F |
| 16 | 10 |
| 17 | 11 |
| ... | ... |
| 255 | FF |

There are various ways to write hex numbers so readers cannot confuse them with actual decimal numbers. They are as follows:

* Prefix hexadecimal numbers with "0x" (e.g. 0x42)
* Prefix hexadecimal numbers with "$" (e.g. $42)
* Sufffix hexadecimal numbers with "H" (e.g. 42H)

In this tutorial, the convention is to prefix hexadecimal numbers with "$".

In assembly, a hexadecimal number with two digits is called a "byte". This means that values between $00-$FF are considered a byte.

## Signed and unsigned values

In the real world, numbers can be positive or negative. In assembly, depending on the code, values can be treated as "signed" or "unsigned". Signed values mean that they can also be negative: The value $80 and higher are considered to be negative numbers in decimal, starting from -128, and counting down as the hex number is counting up, as you can see in the table below.

| Decimal | Hexadecimal |
| :--- | :--- |
| 126 | $7E |
| 127 | $7F |
| -128 | $80 |
| -127 | $81 |
| ... | ... |
| -1 | $FF |

The presence of negative numbers depends on the game’s programming. For example, a player can have positive and negative speed (resulting in going forward or backward), but a player cannot have negative extra lives or points (because normally that doesn’t make sense). Needless to say, the value -0 does not exist.

## Four-digit hexadecimal values
Hexadecimal numbers can count well past two digits, as you can see below.

| Decimal | Hexadecimal |
| :--- | :--- |
| 254 | $FE |
| 255 | $FF |
| 256 | $0100 |
| 257 | $0101 |
| ... | ... |
| 65535 | $FFFF |

The format of such a hexadecimal number is as follows: $HHLL.

* HH is the "high byte" of the number
* LL is the "low byte" of the number
