# Addressing modes

There are different addressing modes in 65c816. Addressing modes are used to make opcodes access addresses and values differently, such as "indexed" or "direct indirect" (explained later in this tutorial). Using them wisely, you can access values and memory addresses in many ways. For example, you can immediately load a value into a register, such as A, or load a byte from the ROM into A. Keep in mind that not all the opcodes supports all the types of addressing modes. Here are some of the important addressing modes you’ll find yourself use very often.

## Immediate 8/16 bit
This addressing mode defines an absolute value, which is written as `#$XX` in 8-bit mode, or `#$XXXX` in 16-bit mode. The # means "immediate value", while the $ stands for hexadecimal. Using # alone makes the input decimal. For example, #10 is the same as #$0A. Think of an immediate value as a number you're directly defining.

## Direct page 
This addressing mode defines a direct page address, which is written as `$XX`.

The direct page is the last 2 hex digits of a long address. For example, address $7E0011 as direct page would be $11. When loading from a direct page address, the bank byte is ALWAYS treated as $00, no exceptions. If you write “LDA $11” for example, you would load the contents of $000011 into the accumulator, which is also mirrored at $7E0011 (remember the illustration of the SNES memory). Therefor, you load $7E0011’s contents into A.

## Absolute
This addressing mode defines an absolute address, which is written as `$XXXX`. 

An absolute address is the last 4 hex digits of a long address. Using the example from earlier, address $7E0011 as an absolute address would be $0011. The bank byte of the absolute address is determined by the data bank register.

## Long
This addressing mode defines a long address, which is written as `$XXXXXX`.

Long addresses deliver fewer complications when dealing with banks and mirroring. You also don’t have to worry about what the data bank register currently contains. With long addresses, you can access any address in the SNES memory.

## Other addressing modes

The SNES supports more addressing modes. The above addressing modes are the basics. There are also addressing modes, such as: indexed versions of direct page, absolute and long addresses, and much more. They will be explained near the end of this tutorial because you don’t need them at this point. It would make things only more confusing at the moment.