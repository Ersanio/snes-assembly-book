# Binary

Another important counting system is “binary”. Binary has only two possible digits for each place value: 0 and 1. A binary digit is also called a "bit". In assembly syntax, bits are prefixed by "%".

In assembly, a hexadecimal number with two digits is called a “byte”. This means that values between $00-$FF are considered a byte.

A byte is made of eight “bits”. Because a binary digit has two possible values, and a byte has 8 bits, this means there are 2⁸ possible values in a byte.

For example, a byte can consist of the following bits: `1001 0110` or `1001 0101`. The first bit from the left is called “bit 7” and the final bit is called “bit 0”. They are NOT called bits 0-7, nor bits 8-1. The table below shows a relatively easy way to memorize binary.

| Binary | Hexadecimal |
| :--- | :--- |
| %0000 0001 | $01 |
| %0000 0010 | $02 |
| %0000 0100 | $04 |
| %0000 1000 | $08 |
| %0001 0000 | $10 |
| %0010 0000 | $20 |
| %0100 0000 | $40 |
| %1000 0000 | $80 |

Note that there is a space inbetween 4 bits for easier readability, although assemblers generally don't accept this syntax. Groups of 4 bits are called "nibbles" and for the purposes of this chapter, they are there to make binary easier to read, because one nibble corresponds to one digit in hexadecimal.

The SNES is capable of working with both 8-bit and 16-bit numbers. While 8-bit numbers are called a byte, 16-bit numbers are called a "word". They would look like they have 16 bits in binary \(e.g. `10000101 11010101`, which is $85D5 in hexadecimal\). In the case of 16-bit numbers, the leftmost bit is called “bit 15” while the rightmost bit is called “bit 0”.

## Flags

Binary is useful when you’re giving a hex number multiple purposes, kind of like an on/off toggle for certain features. These bits are called "flags" and are generally used to save space in the working memory of games.

For example, you can divide a byte into 8 bits with each bit having a different meaning. Bit 7 could indicate that a level has rain or not. Bit 6 could indicate that a level layout is horizontal or vertical. Bit 5 could indicate that the level setting is during day or night, etc. It would look like this in binary:

```text
10100000
││└───── "Is daytime" flag
│└───── "Is horizontal level" flag
└───── "Is raining" flag
```

Finally, here's an overview of how to count up in decimal, hexadecimal and binary:

| Decimal | Hexadecimal | Binary |
| :--- | :--- | :--- |
| 00 | $00 | %0000 0000 |
| 01 | $01 | %0000 0001 |
| 02 | $02 | %0000 0010 |
| 03 | $03 | %0000 0011 |
| 04 | $04 | %0000 0100 |
| 05 | $05 | %0000 0101 |
| 06 | $06 | %0000 0110 |
| 07 | $07 | %0000 0111 |
| 08 | $08 | %0000 1000 |
| 09 | $09 | %0000 1001 |
| 10 | $0A | %0000 1010 |
| 11 | $0B | %0000 1011 |
| 12 | $0C | %0000 1100 |
| 13 | $0D | %0000 1101 |
| 14 | $0E | %0000 1110 |
| 15 | $0F | %0000 1111 |
| 16 | $10 | %0001 0000 |
| 17 | $11 | %0001 0001 |
| ... | ... | ... |
| 254 | $FE | %1111 1110 |
| 255 | $FF | %1111 1111 |

## Notation

Sometimes, bits could be written inconsistently, like `11` or `110 0000`. This makes the binary number harder to read, because the general convention is to write bits in groups of eight. In order to read them, you will need to add leading 0s to the digits until there are either 8 bits or 16 bits in total.

In 8-bit:

* `11` becomes `00000011`
* `1100000` becomes `01100000`

In 16-bit:

* `11` becomes `00000000 00000011`
* `1100000` becomes `00000000 01100000`

You can convert between decimal, hexadecimal and binary, by using Windows calculator’s "programming" mode. There are also many calculators online which can do this. Assembly syntax accepts decimal numbers also, so you usually don't need to convert between decimal and hexadecimal.

