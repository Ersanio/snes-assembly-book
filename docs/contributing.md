# Contributing

This chapter concerns the writers of this tutorial. When writing, there are a few standards to follow in order to maximize consistency throughout the tutorial.

## Terminology
|Rule|Example|
|-|-|
|When referring to the `Ricoh 5A22` processor, use `the SNES` instead.|`The SNES` is capable of entering 8-bit or 16-bit mode.|
|When referring to a specific area in the SNES memory, always append `address` to it.|(The) `RAM address` $7E0000 contains [...].|
|When referring to the A, X and Y registers, just use `A`, `X` or `Y`.|`A` is now in 8-bit mode. `X` is used to index addresses.|
|When referring to values, always append `value` to it|A contains the `value` $00.|

## Example codes
- Code shall use whitespace for indentation, not tabs.
- Opcodes are written entirely in uppercase (e.g.: `LDA`).
- Labels are written in PascalCase (e.g.: `Label1:`).
- Sublabels are written entirely in lowercase and the name should semantically suit the parent label, without redundancy (e.g.: `.return`).
- Defines are written in PascalCase (e.g.: `!SomeDefine`).
- Direct data (`db`, `dw`, `dl`, `dd`) and opcode length specifiers (`.b`, `.w`, `l`) are written entirely in lowercase.
- Comment indicators (i.e. `;`) shall start on column 20, and left-padded by whitespace, not tabs.
    - If there's no space for a comment on column 20, it will start on the same line.
- Comment indicators shall be followed by a whitespace, before the comment itself.
- There will be an extra new line after the opcodes `RTS`, `RTL`, `RTI`, `JMP`, `JML`, `BRA`, `BRL`.

Example:
```
SomeLabel:
LDA.b #$42
STA $00            ; This is a comment
RTS

.table:
db $01,$02,$03,$04 ; Another comment
```