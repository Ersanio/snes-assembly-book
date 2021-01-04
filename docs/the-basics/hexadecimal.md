# Hexadecimal

Para programar em ASM 65c816 , você precisará entender o básico de hexadecimal. Hexadecimal, também conhecido como "hex", é um sistema numérico muito parecido com o decimal, que é o sistema de contagem que as pessoas usam diariamente. No hexadecimal, existem 6 dígitos adicionais para cada casa numérica, que são representandos pelas letras A, B, C, D, E e F, conforme a tabela abaixo.

| Decimal | Hexadecimal |
| :--- | :--- |
| 0 | 0 |
| 1 | 1 |
| 2 | 2 |
| 3 | 3 |
| 4 | 4 |
| 5 | 5 |
| 6 | 6 |
| 7 | 7 |
| 8 | 8 |
| 9 | 9 |
| 10 | A |
| 11 | B |
| 12 | C |
| 13 | D |
| 14 | E |
| 15 | F |
| 16 | 10 |
| 17 | 11 |
| ... | ... |
| 255 | FF |

Há várias maneiras de escrever números hexadecimais para que os leitores não possam confundi-los com números decimais. São os seguintes:

* Prefixo "0x" \(0x42\)
* Prefixo "$"  \($42\)
* Sufixo  "H"  \(42H\)

Por convenção, usaremos o "$" para prefixar números hexadecimais neste tutorial.

Em assembly, um número hexadecimal com dois dígitos é chamado de “byte”. Isso significa que os valores entre $00 a $FF são considerados como um byte.

## Signed and unsigned values

In the real world, numbers can be positive or negative. In assembly, depending on the code, values can be treated as "signed" or "unsigned". Signed values mean that they can also be negative: The value $80 and higher are considered to be negative numbers in decimal, starting from -128, and counting down as the hex number is counting up, as you can see in the table below.

| Decimal | Hexadecimal |
| :--- | :--- |
| 126 | $7E |
| 127 | $7F |
| -128 | $80 |
| -127 | $81 |
| ... | ... |
| -1 | $FF |

The presence of negative numbers depends on the game’s programming. For example, a player can have positive and negative speed \(resulting in going forward or backward\), but a player cannot have negative extra lives or points \(because normally that doesn’t make sense\). Needless to say, the value -0 does not exist.

## Four-digit hexadecimal values
Hexadecimal numbers can count well past two digits, as you can see below.

| Decimal | Hexadecimal |
| :--- | :--- |
| 254 | $FE |
| 255 | $FF |
| 256 | $0100 |
| 257 | $0101 |
| ... | ... |
| 65535 | $FFFF |

The format of such a hexadecimal number is as follows: $HHLL.

* HH is the "high byte" of the number
* LL is the "low byte" of the number
