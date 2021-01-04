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

Por convenção, neste tutorial usaremos o "$" para prefixar os números hexadecimais.

Em assembly, um número hexadecimal com dois dígitos é chamado de “byte”. Isso significa que os valores entre $00 a $FF são considerados como um byte.

## Valores com e sem sinal

No mundo real, os números podem ser positivos ou negativos. Em assembly, dependendo do código, os valores podem ser tratados como "com sinal" ou "sem sinal". Isso significa que os valores com sinal também podem ser negativos: Os valores de $80 para cima são considerados números negativos em decimal, começando em -128 e diminuindo conforme o número hexadecimal vai aumentando, como você pode ver na tabela abaixo.

| Decimal | Hexadecimal |
| :--- | :--- |
| 126 | $7E |
| 127 | $7F |
| -128 | $80 |
| -127 | $81 |
| ... | ... |
| -1 | $FF |

A presença de números negativos depende da programação do jogo. Por exemplo, um jogador pode ter velocidade positiva e negativa \(resultando em ir para frente ou para trá\), mas um jogador não pode ter vidas ou pontos extras negativos \(até porque isso não faz sentido\). Não há necessidade em dizer que o valor -0 não existe.

## Four-digit hexadecimal values
Os números hexadecimais podem passar além dos dois dígitos, como pode ser visto abaixo.

| Decimal | Hexadecimal |
| :--- | :--- |
| 254 | $FE |
| 255 | $FF |
| 256 | $0100 |
| 257 | $0101 |
| ... | ... |
| 65535 | $FFFF |

O formato deste número hexadecimal é: $HHLL.

* HH é o "high byte"
* LL é o "low byte" 
