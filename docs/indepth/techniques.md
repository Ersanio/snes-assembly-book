# Techniques
This chapter focuses on general techniques you can use in SNES ASM.

## Byte counting
Each instruction assembles into one or more bytes. This happens as follows: First, the opcode is guaranteed assembled into a byte. Then the byte is followed by 0-3 bytes which serves as the parameter of the opcode. Every 2 hex digits equals 1 byte. This means that the maximum amount of bytes an instruction in SNES can use, is four bytes: opcode + a 24-bit parameter. Here is an example on how LDA would look like, when it's assembled with different addressing modes:

```
LDA #$00           ; A9 00
LDA #$0011         ; A9 11 00
LDA $00            ; A5 00
LDA $0011          ; AD 11 00
LDA $001122        ; AF 22 11 00
```

An instruction without a hexadecimal parameter is only 1 byte, like `INC A` or `TAX`. An instruction with an 8-bit parameter is 2 bytes, like `LDA #$00`. An instruction with a 16-bit parameter is 3 bytes, like `LDA $0000`. An instruction with a 24-bit parameter is 4 bytes, like `LDA $000000`. It doesn’t matter if the addressing mode is indexed, direct indirect or something else. It all depends on the length of the `$`-value.
