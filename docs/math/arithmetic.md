# Arithmetic operations

At some point, you would probably want to *increase* RAM address $7E000F by $01, but a simple LDA and STA won’t work, because this simply changes the RAM Address’ contents to $01 – not increase it by one. Maybe you'd like to increase it by 2 instead, or even multiply by 2.

The SNES has a few instructions capable of doing basic math operations.

## INC and DEC
These are opcodes to increase or decrease a value by 1.

|Opcode|Full name|Explanation|
|-|-|-|
|**INC**|Increase|Increases accumulator or memory by 1|
|**DEC**|Decrease|Decreases accumulator or memory by 1|

INC and DEC support both the accumulator as well as addresses. Here's an example of increasing the accumulator by 1:
```
LDA #$02 ; Load the value $02 into the accumulator
INC A    ; Increase the accumulator by 1. It is now $03
```
Here's an example to decrease the accumulator by 1:
```
LDA #$02 ; Load the value $02 into the accumulator
DEC A    ; Decrease the accumulator by 1. It is now $01
```

Here's an example to increase the value of an address by 1, without affecting the accumulator at all:
```
INC $0F	; Increase the value in $7E000F by one
RTS		; Return. A remains unchanged
```
Here's also an example to decrease a value by one
```
DEC $0F	; Decrease the value in $7E000F by one
RTS		; Return. A remains unchanged
```

When you use INC when the value being modified is currently $FF, it would result in a $00 and the zero flag being set. Conversely, when you use DEC and the value being modified is currently $00, it would result in a $FF.

{% hint style="info" %}
INC and DEC don’t work with long addressing modes. They only work with absolute or direct page addressing modes. Therefore, instructions like `INC $7E000F` do not exist. Instead, you should use `INC $000F` or `INC $0F`.

Why isn’t there a long addressing mode? The processor is simply made that way, so you'll have to deal with it, one way or another.
{% endhint %}

## INX, DEX, INY and DEY
There are also instructions to increase or decrease the value inside the index registers:

|Opcode|Full name|Explanation|
|-|-|-|
|**INX**|Increase X|Increases X by 1|
|**DEX**|Decrease X|Decreases X by 1|
|**INY**|Increase Y|Increases Y by 1|
|**DEY**|Decrease Y|Decreases Y by 1|

You cannot use the above 4 opcodes to manipulate an address. They are solely used for the X and the Y registers.

## ADC and SBC
If you wanted to increase or decrease a value by, say, 95, you wouldn't want to write INC or DEC 95 times. Thankfully, the SNES has instructions for such situations as well.

|Opcode|Full name|Explanation|
|-|-|-|
|**ADC**|Add with carry|Adds a given value to A|
|**SBC**|Subtract with carry|Subtracts a given value from A|

{% hint style="warning" %}
Just for emphasis: ADC adds a value to the accumulator, not a RAM address. SBC subtracts a value from the accumulator.
{% endhint %}

With ADC and SBC, you can do add and subtract operations on addresses. Here's an example which adds 4 to the value of an address:
```
LDA $0F  ; Load the address value in A
CLC      ; Clear carry flag
ADC #$04 ; What you will add to A. It is $04 in this case.
STA $0F  ; Store A in $0F
```
Because ADC modifies A rather than addresses, you must first load the address value into A, then do the addition, then store the result back into the address. 

If the carry flag wasn't cleared, the addition would've been $05 instead of $04, depending on the state of the carry flag at that moment. Therefore, it's important to be extra sure about the carry flag and just clear it with a `CLC`, before the ADC.

The opposite is also true for subtracting:

```
LDA $0F  ; Load the current value in A
SEC      ; Set Carry Flag
SBC #$04 ; How many you will subtract from A. It is $04 in this case.
STA $0F  ; Store A in $0F
```
This will subtract 4 from the RAM address’ content (`$0F-#$04`). You'll notice that for subtracting, we set the carry flag rather than clear it. If you didn’t set the carry flag, it would subtract $05 instead of $04. This might seem backwards, but it's just how the processor works.

{% hint style="info" %}
In short: when adding, use `CLC`. When subtracting, use `SEC`.
{% endhint %}

### Carry flag
When you use ADC, and the value in A wraps from $FF to $00, the carry flag will be set. This is also true for 16-bit A, when the value wraps from $FFFF to $0000.

When you use SBC, and the value in A wraps from $00 to $FF, the carry flag will be cleared. This is also true for 16-bit A, when the value wraps from $0000 to $FFFF.

With this, you can use the carry flag to check if some sort of value wrapping has occured.

### Overflow flag
The opcodes ADC and SBC are unique in the sense that they're two of the three opcodes which can affect the *signed overflow* processor flag as a result of a calculation.

The overflow flag is especially relevant when you decide to treat values as signed values. Remember the hexadecimal chapter with the signed and unsigned values? Values $00-$7F are considered positive, and values $80-$FF are considered negative. 

The overflow flag is set when the result of an operation doesn't make sense in the math world:
* Adding a negative value to a negative value, and getting a positive value as a result, when it should be negative
* Adding a positive value to a positive value, and getting a negative value as a result, when it should be positive
* Subtracting a positive value from a negative value, and getting a positive value as a result, when the negative value should be even more negative
* Subtracting a negative value from a positive value, and getting a negative value as a result, when the value should be even more positive

Math rules are at play here. Adding two negative numbers results in a negative number (e.g. `-10 + -1 = -11`). Subtracting two negative numbers results in a negative number (e.g. `-10 - -1 = -9`). Subtracting a negative value equals an addition (e.g. `10 - -1 = 11`). Adding a negative value equals a subtraction (e.g. `10 + -1 = 9`). The scenarios described in those bullet points break these rules. 

Here are some examples of the overflow flag getting set:
```
LDA #$88  ; Number -120
CLC	      ; We do -120 + -16, which should result in -136
ADC #$F0  ; $88 + $F0 = $78, which is 120, which doesn't make sense mathematically
```

```
LDA #$80  ; Number -128
SEC       ; We do -128 - 16, which should result in -144
SBC #$10  ; $80 - $10 = $70, which is 112, which doesn't make sense mathematically
```

```
LDA #$30  ; Number 48
CLC       ; We do 48 + 112, which should result in 160
ADC #$70  ; $30 + $70 = $A0, which is -96, which doesn't make sense mathematically
```

```
LDA #$10  ; Number 16
SEC       ; We do 16 - -128, which should result in 144
SBC #$80  ; $10 - $80 = $90, which is -112, which doesn't make sense mathematically
```

## 16-bit mode math
All the previous explanations and behaviours apply to 16-bit math as well.