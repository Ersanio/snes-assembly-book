# Bit shifting operations
'Bit shifting' is a bitwise operation in which you move bits left or right. As a result, practically speaking, you divide or multiply a number by two.

## ASL and LSR
There are two opcodes which shift bits to the left or right.

|Opcode|Full name|Explanation|
|-|-|-|
|**ASL**|A or memory shift left|Moves bits left once without carry, practically multiplying a value by 2|
|**LSR**|A or memory shift right|Moves bits right once without carry, practically dividing a value by 2|

ASL and LSR can affect either A or an address, just like INC and DEC. Here's an example of ASL in action:

```
LDA #$02 ; Load the value $02 into A
ASL A    ; Multiply A by 2
         ; A is now $04
```

This is what appears on the surface. When you look at the numbers in binary though, this is what it looks like:

```
LDA #$02 ; Load the value %00000010 into A
ASL A    ; Shift bits left once
         ; A is now %00000100
```
As you can see, the digit '1' has moved to the left once. That is what ASL does, moving bits left.

ASL can also move bits in an address without affecting A.

```
LDA #$02 ; Load the value $02 into A
STA $00  ; Store this into address $7E0000
ASL $00  ; Shift bits of $7E0000 left once
         ; A is still $02, while $7E0000 is now $04
```

You can also shift bits to the right by using LSR.

```
LDA #$02 ; Load the value $02 into A
LSR A    ; Divide A by 2
         ; A is now $01
```

And when you look at it on the level of binary, rather than hexadecimal:

```
LDA #$02 ; Load the value %00000010 into A
LSR A    ; Shift bits right once
         ; A is now %00000001
```
As you can see, the digit '1' has moved to the right once. That is what LSR does, moving bits right.

LSR can also move bits in an address without affecting A.

```
LDA #$02 ; Load the value $02 into A
STA $00  ; Store this into address $7E0000
LSR $00  ; Shift bits of $7E0000 right once
         ; A is still $02, while $7E0000 is now $01
```

### Edge-cases and carry flag
You might be wondering what happens if you bit shift `%1000 0001` to the right once, by using LSR. Bit 7 is currently set, but there's nothing to shift into bit 7. At the same time, bit 0 is also set, but it has nowhere to shift into. When this happens, bit 0 will move into the carry flag. At the same time, bit 7 will be set to 0.

The opposite is also true when you shift `%1000 0001` to the left once, by using ASL. Bit 7 will move into the carry flag, while bit 0 will be set to 0.

Here are some examples of a bit moving into the carry flag.
```
CLC         ; Ensuring carry (C) = 0
LDA #$80	; A = %1000 0000 | C = 0
ASL A		; A = %0000 0000 | C = 1
ASL A		; A = %0000 0000 | C = 0
```

```
CLC         ; C = 0
LDA #$01	; A = %0000 0001 | C = 0
LSR A		; A = %0000 0000 | C = 1
LSR A		; A = %0000 0000 | C = 0
```

### Chaining ASL and LSR
By chaining ASL and LSR, you can multiply or divide a value by 2 several times, therefore multiplying or dividing it by 2, 4, 8, 16, and so on. It's 2ⁿ, where n is the amount of ASL or LSR instructions you're chaining.

Here's an example of multiplying a value by 8.

```
LDA #$07    ; A is now $07 = %0000 0111
ASL A       ; A is now $0E = %0000 1110
ASL A       ; A is now $1C = %0001 1100
ASL A       ; A is now $38 = %0011 1000
            ; This is basically $07 * 2³
```

Here's an example of dividing a value by 4
```
LDA #$07    ; A is now $07 = %0000 0111
LSR A       ; A is now $03 = %0000 0011
LSR A       ; A is now $01 = %0000 0001
            ; This is basically $07 / 2²
```

## ROL and ROR
There are two opcodes which *rotate* bits to the left or right, rather than shifting.

|Opcode|Full name|Explanation|
|-|-|-|
|**ROL**|Rotate A or memory left|Moves bits left once with carry, wrapping the bits around|
|**ROR**|Rotate A or memory right|Moves bits right once with carry, wrapping the bits around|

They behave the same as LSR and ASL, except they are using the carry flag as an extra bit in order to make the bits 'wrap' around. This means that the value can't be 'stuck' at $00 eventually, like it happens in ASL and LSR. This is why they're called rotate, rather than shift.

Here's an example of ROL:
```
CLC         ; Ensuring carry (C) = 0
LDA #$80	; A = %1000 0000 | C = 0
ROL A		; A = %0000 0000 | C = 1
ROL A		; A = %0000 0001 | C = 0
            ; A is now $01
```
As you can see, bit 7 wrapped all the way around to bit 0, basically 'rotating' the bits without losing any information.

```
CLC         ; C = 0
LDA #$01	; A = %0000 0001 | C = 0
ROR A		; A = %0000 0000 | C = 1
ROR A		; A = %1000 0000 | C = 0
            ; A is now $80
```
As you can see, bit 0 wrapped all the way around to bit 7, basically 'rotating' the bits without losing any information.

```
SEC         ; C = 1
LDA #$00	; A = %0000 0000 | C = 1
ROL A		; A = %0000 0001 | C = 0
            ; A is now $01
```
As you can see, you can set the carry flag and rotate. This will result in A being modified anyway, even though A started out as $00.

Rotation can also affect addresses, just like ASL and LSR. Here's an example:

```
CLC      ; Clear carry
LDA #$02 ; Load the value $02 into A
STA $00  ; Store this into address $7E0000
ROR $00  ; Shift bits of $7E0000 right once
         ; A is still $02, while $7E0000 is now $01
         ; and carry is still cleared
```

## 16-bit mode bit shifting
All the previous explanations and behaviours apply to 16-bit bit shifting as well. It's just that you're working with 16 bits and the carry flag, not 8 bits.