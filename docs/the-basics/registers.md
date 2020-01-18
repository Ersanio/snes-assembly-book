# The SNES registers

In SNES, there are several “registers” used for different purposes. They cannot be missed; they’re one of the reasons why the SNES can function properly. Basically, registers are “global variables” which can be used to hold values, or can be used for math and logic and all those fancy stuffs! These registers can be accessed anytime.

## Accumulator
The accumulator, also known as **A**, is used for general math, bit shifts, bitwise operations and loading indirect values. A can also hold general-purpose variables to store things to the memory and other registers. This register can hold either an 8-bit or 16-bit value 

In reality, this register is always 16-bits long. When A is in 8-bit mode, you access the low byte of this register. When A is in 16-bit mode, you access both the high and low bytes of this register. The high byte doesn't get cleared when A enters 8-bit mode. Also, certain instructions use both high and low bytes of the A register, regardless of whether A is in 8 or 16-bit mode.

## Indexers
The indexers are two registers, known as **X** and **Y**. Even though they are separate registers, they have exactly the same purposes and behave exactly the same. These registers are made for indexing, explained later in this tutorial. These registers can also be 8-bit or 16-bit. X and Y can also hold general-purpose variables to store things to the memory and other registers. 

When X and Y leave 16-bit mode, their high bytes get cleared to the number $00. X and Y are “paired” – they can be 8-bit or 16-bit mode only at the same time. One of them can’t be 8-bit while the other one is 16-bit.

## Direct Page 
The direct page register is a 16-bit register, used for the direct page addressing mode (explained later in this tutorial). When you access a memory address by its direct page notation, the value in the direct page is added to that address. You can generally ignore this register if you're just beginning with assembly.

## Stack Pointer
The stack pointer register is a 16-bit that holds the pointer to the stack in the RAM (explained later in this tutoorial), relative to memory address $000000. The register dynamically changes, as you push and pull values to the stack (explained later in the tutorial).

## Processor Status
The processor status register holds the current processor flags in 8-bit format. There are 8 processor flags, and they all occupy one bit. Changing this register would alter the SNES behaviour greatly. Processor flags are explained later in this tutorial.

## Data bank
The data bank register holds the current data bank address as a single byte. When you access an address using the "absolute address" notation, the SNES will use this register to determine the bank of the address.

## Program bank
The program bank register keeps track of the current bank of the currently executed instruction. So, if there is a code executed at address $018009, this register will hold the value $01.

## Program counter
This register keeps track of the high and low bytes of the address of the currently executed instruction. So, if there is an instruction executed at $018009, this register will hold the value $8009.