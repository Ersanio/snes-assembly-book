# Contributing
This chapter concerns the writers of this tutorial. When writing, there are a few standards to follow in order to maximize consistency throughout the tutorial.

## Address explanations
This section concerns the way addresses are written, as to avoid confusion.

### Direct page
- For the sake of simplicity and explanation, all direct page addresses ("`$xx`") shall use address `$7E0000` as its base. This shall be emphasized in code block comments, as well as explanations.

Example:
```
LDA #$42
STA $08            ; Store the value $42 into address $7E0008
```

### Absolute addressing
- All absolute addresses ("$xxxx") use address `$7E0000` as its base, if the address is between $0000-$7FFF inclusive. For addresses between $8000-$FFFF, address `$000000` shall be used as its base instead. This shall be emphasized in code block comments, as well as explanations.

Example:
```
LDA $8002            ; Load the value at address $008002 into A
STA $1008            ; Store that value into address $7E1008
```

## Styling
This section concern the styling of the document.

### Inline code
- All opcodes and instructions shall be surrounded by the markdown backtick (`), regardless of the context.

Example: The opcode `LDA` is used to load values into the A register. Thus, `LDA #$49` loads the value $49 into A. This then can be increased to the value $4A by using `INC A`.

### Markdown tables
- Sentences and phrases within table cells generally shouldn't end with a period.
- Tables introducing opcodes should have the opcodes in **bold** and have at least the three columns in below example:

|Opcode|Full name|Explanation|
|-|-|-|
|**LDA**|Load into accumulator|Load a value into A|

## Terminology
This section concerns the usage of certain words in certain context.

|Rule|Example|
|-|-|
|When referring to the `Ricoh 5A22` processor, use `the SNES` instead|`The SNES` is capable of entering 8-bit or 16-bit mode|
|When referring to a specific area in the SNES memory, always prepend `address` to it, preferably with the memory area (i.e. `RAM`)|(The) `RAM address` $7E0000 contains [...]|
|When referring to the accumulator (A) or the index registers (X and Y), just use `A`, `X` or `Y`|`A` is now in 8-bit mode. `X` is used to index addresses|
|When referring to values, always prepend `value` to it|A contains the `value` $00|

## Example codes
- Code shall use indentation when there are labels, sublabels or plus/minus labels on the same line as an instruction. The amount of indentation equals the length of the longest aforementioned type of label in the code block, including colon ("`:`"), plus two additional spaces.
  - Code shall use whitespace for indentation, not tabs.
- Opcodes are written entirely in uppercase (e.g.: `LDA`).
- Labels are written in PascalCase (e.g.: `Label1:`).
- Sublabels are written entirely in lower, underscore-case and the name should semantically suit the parent label, without redundancy (e.g.: `.return`).
- Defines are written in PascalCase (e.g.: `!SomeDefine`).
- Direct data (`db`, `dw`, `dl`, `dd`) and opcode length specifiers (`.b`, `.w`, `.l`) are written entirely in lowercase.
- Comment indicators (i.e. `;`) shall start on column 20, and left-padded by whitespace, not tabs.
    - If there's no space for a comment on column 20, it will start on the same line.
- Comment indicators shall be followed by a whitespace, before the comment itself.
- There will be an extra new line after the opcodes `RTS`, `RTL`, `RTI`, `JMP`, `JML`, `BRA`, `BRL`.
- These are guidelines which are to be followed as strictly as possible, but there may be exceptional cases. Use your best judgment.

Examples:
```
SomeLabel:         ; This label is on its own line
LDA.b #$42
STA $00            ; This is a comment
RTS

.table:
db $01,$02,$03,$04 ; Another comment

.second_table:
db $01,$02,$03,$04,$01,$02,$03,$04 ; An exceptional comment
```
```
TestLabel:  LDA #$02 ; This label is on the same line as an instruction
            STA $01
            BNE +
            NOP
+           RTS      ; The code is indented according to "TestLabel", not "+"
```