# Stack pointer register
The stack pointer register is a 16-bit register which the processor uses to determine the current stack location in the SNES RAM. After each push, the stack pointer *decreases*, so the bytes are pushed backwards. They overwrite the RAM address’ contents. Conversely, after each pull, the stack pointer *increases*, but the pulled value still remains in the stack. Therefore, a pull is actually a read. The stack pointer increases or decreases by 1 when it deals with an 8-bit value, and by 2 when it deals with a 16-bit value. This can be summarized as follows:

|Operation|Where|Stack pointer modification|
|-|-|-|
|Push (8-bit)|Stack pointer location|-1|
|Pull (8-bit)|Stack pointer location +1|+1|
|Push (16-bit)|Stack pointer location|-2|
|Pull (16-bit)|Stack pointer location +1|+2|

The stack pointer register assumes that it works with bank $00, regardless of the current value of the data bank register. This means that from the SNES memory mapping point of view, if the stack pointer ever starts pointing to absolute address $8000 or above, it'll start pointing to the ROM. If it points to absolute address $2000 or above, it'll start pointing to the SNES hardware registers. Therefore, the only useable stack area is $000000-$001FFF.

The stack doesn’t have a defined size. Instead, you, as a programmer, just reserve an area of RAM for the stack you think you need. The reason it doesn’t have a defined size is because as long as you keep pushing, the stack pointer keeps decreasing without any set limits. If you push too many values, you might accidentally overwrite other RAM addresses which have other predetermined purposes.

## Push
Here is an example of how the stack works from the RAM’s point of view, when you push an 8-bit value:
```
         ;Stack: .. 55 55 55 55 55 55 ..
LDA #$42                            └─Stack pointer points to this value
PHA
         ;Stack: .. 55 55 55 55 55 42 ..
LDA #$AA                         └─Stack pointer now points to this value
PHA
         ;Stack: .. 55 55 55 55 AA 42 ..
                              └─Stack pointer now points to this value
```

Here is an example of a 16-bit push:
```
REP #$20 ;16-bit A mode
         ;Stack: .. 55 55 55 55 55 55 ..
LDA #$4210                          └─Stack pointer points to this value
PHA
         ;Stack: .. 55 55 55 55 10 42 ..
LDA #$AA99                    └─Stack pointer now points to this value
PHA
         ;Stack: .. 55 55 99 AA 10 42 ..
                        └─Stack pointer now points to this value
```

## Pull
When you pull something from the stack, it gets pulled from the location of the stack pointer, +1. After each pull, the stack pointer increases depending on the size of the pulled value. You do not literally extract the byte out of the RAM. You just copy the byte to the register you pull it into. The byte in the stack does not reset or anything. It remains the same. 

Here's an example of pulling an 8-bit value:
```
        ;Stack: .. 55 55 12 34 56 78 ..
                          └─Stack pointer points to this value
PLA     ; A is now $34
        ;Stack: .. 55 55 12 34 56 78 ..
                             └─Stack pointer now points to this value
PLA
        ; A is now $56
        ;Stack: .. 55 55 12 34 56 78 ..
                                └─Stack pointer now points to this value
```

Here's an example of pulling a 16-bit value:
```
REP #$20 ;16-bit A mode
         ;Stack: .. 55 55 12 34 56 78 ..
                        └─Stack pointer points to this value
PLA      ; A is now $3412
         ;Stack: .. 55 55 12 34 56 78 ..
                             └─Stack pointer now points to this value
PLA
         ; A is now $7856
         ;Stack: .. 55 55 12 34 56 78 ..
                                    └─Stack pointer now points to this value
```
