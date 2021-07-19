# Addressing modes revisited
In the [basic addressing modes](../the-basics/addressing.md) chapter, we briefly looked at the most commonly used addressing modes: "Immediate 8-bit and 16-bit", "direct page", "absolute" and "long". In this chapter, we will look at the more advanced addressing modes: "indirect", "indexed" and "stack relative". These advanced addressing modes expand upon the earlier introduced addressing modes. This chapter also introduces the concept of pointers.

## Pointers
This is where "pointers" come into play. Pointers are values which pretty much "point" to a certain memory location. Imagine the following SNES memory:
```
         ; $7E0000: 12 80 00 55 55 55 ..
```
In this example: the RAM at address $7E0000 contains the values $12, $80 and $00. This is in little-endian, so reverse the values and you get $00, $80, $12. Treat this as a 24-bit "long" address and you have $008012. This means that RAM address $7E0000 has a 24-bit pointer to $008012. This is what "indirect" is; accessing address $7E0000 "indirectly" accesses address $008012.

You can access indirect pointers in two ways: 16-bits and 24-bits. They have a special assembler syntax:
|Syntax|Terminology|Pointer size|
|-|-|-|
|( )|Indirect|16-bit|
|[ ]|Indirect long|24-bit|

The bank byte of the 16-bit pointer depends on the type of instruction. When it comes to a `JSR`-opcode, which can only jump inside the current bank, the bank byte isn"t determined nor used. However, when it comes to a loading instruction, such as `LDA`, the bank byte is determined by the *data bank register*.

Pointers can point to both *code* and *data*. Depending on the type of instruction, the pointers are accessed differently. For example, a `JSR` which utilizes a 16-bit pointer accesses the pointed location as code, while an `LDA` accesses the pointed location as a bank.

## Indirect
Indirect addressing modes are basically accessing addresses in such a way, that you access the address they point to, rather than directly accessing the contents of the specified address.

### Direct, Indirect
As contradicting as it may sound, the naming actually makes sense. "Direct" stands for direct page addressing mode, while "indirect" means that we're accessing a pointer at the direct page address, rather than a value. Here's an example:

```
; Setup indirect pointer
REP #$20
LDA #$1FFF          ; $1FFF to RAM $7E0000
STA $00
SEP #$20
                    ; $7E0000: FF 1F .. .. .. .. ..

; Access indirect pointer
LDA ($00)   ; Load the value at address $1FFF into A
```
This accesses a 16-bit pointer at address $000000, due to the nature of direct page always accessing bank $00. Due to the effects of mirroring in the SNES mirroring, practically speaking, this accesses a 16-bit pointer at RAM $7E0000.

You might think that `LDA ($00)` loads the `value $1FFF` into A. However, it doesn't work that way. It loads the `value in address $1FFF` into A, because we use an indirect addressing mode. 

As we established earlier, parentheses denote 16-bit pointers. The bank of the indirect address, in the case of an LDA, depends on the data bank register. As a result, the `LDA ($00)` resolves into `LDA $1FFF`.

### Direct Indexed with X, Indirect
As is the case with the previous addressing mode, the naming may seem contradicting. "Direct" stands for direct page addressing mode. "Indexed with X" means that this direct page address is indexed with X. "Indirect" means that the previous elements are treated as an indirect address. Here's an example usage:

```
; Setup indirect pointers
REP #$20
LDA #$1FFF         ; $1FFF to RAM $7E0000
STA $00
LDA #$0FFF         ; $0FFF to RAM $7E0002
STA $02
SEP #$20
                   ; $7E0000: FF 1F FF 0F .. .. ..

; Access indirect pointer
LDX #$02           ; Set X to $02
LDA ($00,x)        ; Loads the value at address $0FFF into A
```
- The 16-bit value in address $7E0000 + $7E0001 is `$1FFF`.
- The 16-bit value in address $7E0002 + $7E0003 is `$0FFF`.

Thanks to using X as an indexer to the direct page address, `LDA ($00,x)` is resolved into `LDA ($02)`. This then resolves into `LDA $0FFF` because RAM $7E0002 points to `address $0FFF`.

### Direct, Indirect Indexed with Y
This is practically the same as `Direct, Indirect` but the pointer is then indexed with the Y register:
```
; Setup indirect pointer
REP #$20
LDA #$1FF0         ; $1FF0 to RAM $7E0000
STA $00
SEP #$20
                   ; $7E0000: F0 1F .. .. .. .. ..

; Access indirect pointer
LDY #$01           ; Set Y to $01
LDA ($00),y        ; Load value at address $1FF1 into A
```
As a result, `LDA ($00),y` resolves into `LDA $1FF0,y`, which then resolves into `LDA $1FF1` because Y contains the value $01.

### Absolute, Indirect
Exactly the same as `Direct, Indirect`, except the specified address is now 16-bit instead of 8-bit. This addressing mode is only used by jumping instructions. Example:
```
; Setup indirect pointer
REP #$20
LDA #$8000         ; $8000 to RAM $7E0000
STA $00
SEP #$20
                   ; $7E0000: 00 80 .. .. .. .. ..

; Access indirect pointer
JMP ($0000)        ; Jumps to $8000.
```
This has the same exact effect as the example in `Direct, Indirect`. As a result, the `JMP ($0000)` resolves into `JMP $8000`, thus jumps to `address $8000` in the current bank.

### Absolute Indexed with X, Indirect
Exactly the same as `Direct Indexed with X, Indirect`, except the specified address is now 16-bit instead of 8-bit. This addressing mode is only used by jumping instructions. Example:

```
REP #$20
LDA #$8000         ; $8000 to RAM $7E0000
STA $00
LDA #$9000         ; $9000 to RAM $7E0002
STA $02
SEP #$20
                   ; $7E0000: 00 80 00 90 .. .. ..

; Access indirect pointer
LDX #$02           ; Set X to $02
JMP ($0000,x)      ; Jumps to $9000
```
- The 16-bit value in address $7E0000 + $7E0001 is `$8000`. 
- The 16-bit value in address $7E0002 + $7E0003 is `$9000`.

Thanks to using X as an indexer to the direct page address, `JMP ($0000,x)` is resolved into `JMP ($0002)`. This then resolves into `JMP $9000` because RAM $7E0002 points to `address $9000`. This addressing mode is handy for jump tables.

### Direct, Indirect Long
Exactly the same as `Direct, Indirect`, except the pointer located at an address is now 24-bits instead of 16-bits, meaning the bank byte of a pointer is also specified. Example:
```
REP #$20
LDA #$1FFF         ; $1FFF to RAM $7E0000
STA $00
SEP #$20
LDX #$7F           ; $7F to RAM $7E0002
STX $02            ; $7E0000 now contains the 24-bit pointer $7F1FFF
                   ; $7E0000: FF 1F 7F .. .. .. ..
LDA [$00]          ; Load the value at address $7F1FFF into A
```
`LDA [$00]` resolves into `LDA $7F1FFF`.

### Direct, Indirect Indexed Long with Y
Exactly the same as `Direct, Indirect Indexed with Y`, except the pointer located at an address is now 24-bits instead of 16-bits, meaning the bank byte of a pointer is also specified. Example:
```
; Setup indirect pointer
REP #$20
LDA #$1FF0         ; $1FF0 to RAM $7E0000
STA $00
SEP #$20
LDX #$7F           ; $7F to RAM $7E0002
STX $02            ; $7E0000 now contains the 24-bit pointer $7F1FF0
                   ; $7E0000: F0 1F 7F .. .. .. ..

; Access indirect pointer
LDY #$01           ; Set Y to $01
LDA [$00],y        ; Load value at address $7F1FF1 into A
```
As a result, `LDA [$00],y` resolves into `LDA $7F1FF0,y` (practically speaking), which then resolves into `LDA $7F1FF1` because Y contains the value $01.

## Indexed
Indexed addressing mode was actually briefly touched upon in an [earlier chapter](../collections/indexing.md). This chapter will discuss all the possible indexed addressing modes.

### Direct, Indexed with X
This addressing mode indexes a direct page address with X. Example:
```
LDX #$02
LDA $00,x          ; Loads the value at address $7E0002 into A
```

### Direct, Indexed with Y
This addressing mode indexes a direct page address with Y. This addressing mode only exists on the `LDX` and `STX`-opcodes. Example:
```
LDY #$02
LDX $00,y          ; Loads the value at address $7E0002 into X
```

### Absolute, Indexed with X
This addressing mode indexes an absolute address with X. Example:
```
LDX #$02
LDA $0000,x        ; Loads the value at address $7E0002 into A
```

### Absolute, Indexed with Y
This addressing mode indexes an absolute address with Y. Example:
```
LDY #$02
LDA $0000,y        ; Loads the value at address $7E0002 into A
```

### Absolute, Long Indexed with X
This addressing mode indexes a long address with X. Example:
```
LDX #$02
LDA $7E0000,x      ; Loads the value at address $7E0002 into A
```

## Stack Relative
Stack relative is a special type of an indexing addressing mode. It uses the stack pointer register as a 16-bit index, rather than using the X or Y register. The index is **always** 16-bit, regardless of the register size of A, X and Y.

### Stack Relative
This loads a value from the RAM, relative to the stack pointer. The bank byte is always $00. Example:
```
; Stack pointer register: $1FF0
LDA $00,s          ; ($001FF0) Loads the value in the current free slot in the stack, into A.
LDA $01,s          ; ($001FF1) Loads the last pushed value into A.
LDA $02,s          ; ($001FF2) Loads the second last pushed value into A.
LDA $03,s          ; ($001FF3) ...
```
In 16-bit A mode, these instructions would read 16-bit values, rather than 8-bit values. If you don't use increments of 2, you'll start reading overlapping values.

### Stack Relative, Indirect Indexed with Y
This is pretty much the same as `Direct, Indirect Indexed with Y`, except the value is loaded from a stack relative address. Example:

```
; Stack pointer register = $01FD
REP #$20
LDA #$0100
PHA
SEP #$20
LDY #$03
LDA ($01,s),y      ; → LDA ($01FE),y → LDA $0100,y → LDA $0103
```
`$01,s` refers to the last pushed value into A, which is $0100 in the case of this example. The parentheses applies on this stack relative address, resolving the instruction to an `LDA $0100,y`. This finally resolves into `LDA $0103` because of the indexer.

This addressing mode is handy if you'd like to treat certain pushed values as an indexed memory address.
