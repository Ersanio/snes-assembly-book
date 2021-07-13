# Copying data
65c816 assembly has two opcodes dedicated to moving huge blocks of data from one memory location to another.

|Opcode|Full name|Explanation|
|-|-|-|
|**MVN**|Move block negative|Moves a block of data byte by byte, starting from the beginning and working towards the end|
|**MVP**|Move block positive|Moves a block of data byte by byte, starting from the end and working towards the beginning|

MVP and MVN practically do a mass amount of LDA and STA to some RAM addresses. You can’t move data to ROM, because ROM is read-only.

It's highly recommended to have the A, X and Y registers at 16-bit mode. It's also highly recommended to preserve the data bank using the stack, as the MVN opcode implicitly changes this. Here's an example of a proper block move setup:
```
PHB                ; Preserve data bank
REP #$30           ; 16-bit AXY
                   ; ← Move instructions are located here
SEP #$30           ; 8-bit AXY
PLB                ; Recover data bank
```

## MVN
When using MVN, the three main registers all have a special purpose.

|Register|Purpose|
|-|-|
|A|Specifies the amount of bytes to transfer, *minus 1*|
|X|Specifies the high and low bytes of the data source memory address|
|Y|Specifies the high and low bytes of the destination memory address|
The A register is 'minus 1'. This means that if you want to move 4 bytes of data, you load $0004-1, which is $0003, into A.

MVN can be written in two ways: 
```
MVN $xxyy
; or
MVN $xx, $yy
```
Where `xx` is the source bank, and `yy` is the destination bank.

{% hint style="info" %}
Note that the comma notation's parameters are reversed.
{% endhint %}

When executing the MVN opcode, the SNES loops at that same opcode for each byte transferred. When a byte is transferred, the following happens:
|Register|Event|
|-|-|
|A|Decreases by 1|
|X|Increases by 1|
|Y|Increases by 1|
|Data bank|Is set to the bank of the destination address|
Seeing that A decreases by 1, eventually it will reach the value $0000, then it'll wrap to $FFFF. Once that wrap happens, the block move finishes and the SNES proceeds to execute the opcodes that follow.

Here's an example of a block move:
```
PHB                ; Preserve data bank
REP #$30           ; 16-bit AXY
LDA #$0004         ; \
LDX #$8908         ;  |
LDY #$A000         ;  | Move 5 bytes of data from $1F8908 to $7FA000
MVN $1F, $7F       ; /
SEP #$30           ; 8-bit AXY
PLB                ; Recover data bank
```
This example will move 5 bytes of data from address $1F8098 to $7FA000.

## MVP
When using MVP, the three main registers all have a special purpose.

|Register|Purpose|
|-|-|
|A|Specifies the amount of bytes to transfer, *minus 1*|
|X|Specifies the high and low bytes of the data source memory address|
|Y|Specifies the high and low bytes of the destination memory address|
The A register is 'minus 1'. This means that if you want to move 4 bytes of data, you load $0004-1, which is $0003, into A.

MVP can be written in two ways: 
```
MVP $xxyy
;or
MVP $xx, $yy
```
Where `xx` is the source bank, and `yy` is the destination bank.

When executing the MVP opcode, the SNES loops at that same opcode for each byte transferred. From this point on, this is where MVP differs from MVN. When a byte is transferred, the following happens:
|Register|Event|
|-|-|
|A|Decreases by 1|
|X|Decreases by 1|
|Y|Decreases by 1|
|Data bank|Is set to the bank of the destination address|
Considering that X and Y decrease, rather than increase, this means that MVP moves blocks of data from the end towards the beginning.

Here's an example of a block move:
```
PHB                ; Preserve data bank
REP #$30           ; 16-bit AXY
LDA #$0004         ; \
LDX #$8908         ;  |
LDY #$A000         ;  | Move 5 bytes of data from ($1F8908-$0004) to ($7FA000-$0004)
MVP $1F, $7F       ; /
SEP #$30           ; 8-bit AXY
PLB                ; Recover data bank
```
This example will move 5 bytes of data from address $1F8904 to $7F9FFC. Although the transfer happens backwards, the transferred data isn't reversed. It still copies over as you'd expect.

## Edge cases
* When you set the A register to $0000, it means you will move 1 byte.
* When you set the A register to $FFFF, it means you will move 65536 bytes.
* When either the source or destination addresses cross a bank boundary, the high and low bytes reset to $0000, while the data bank remains unchanged.

## Easy notation
Asar supports labels as parameters for LDA, so you can write block moves without having to calculate the source table size or address locations. The following examples allow for tables of all sizes, and assume the destination to be memory address $7FA000.

An example for MVN:
```
PHB
REP #$30
LDA.w #SomeTable_end-SomeTable-$01
LDX.w #SomeTable
LDY #$A000
MVN SomeTable>>16, $7F
SEP #$30
PLB
RTS

SomeTable: db $00,$01,$02,$03,$04
.end
```
An example for MVP:
```
PHB
REP #$30
LDA.w #SomeTable_end-SomeTable-$01
LDX.w #SomeTable+SomeTable_end-SomeTable-$01
LDY.w #$A000+SomeTable_end-SomeTable-$01
MVP SomeTable>>16, $7F
SEP #$30
PLB
RTS

SomeTable: db $00,$01,$02,$03,$04
.end
```
As you can see, MVP is considerably more complicated to setup.
