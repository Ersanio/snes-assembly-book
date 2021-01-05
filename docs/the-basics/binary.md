# Binário

Outro sistema de numérico importante é o “binário”. O binário tem apenas valores de dois dígitos para cada casa numérica: 0 e 1. Um dígito binário também é chamado de "bit". Na sintaxe do assembly, os bits são prefixados por "%".

Um byte é composto por oito “bits”. Como um dígito binário tem dois valores possíveis e um byte tem 8 bits, isso significa que ha 2⁸ possibilidades em apenas um byte.

Por exemplo,em um byte pode conter os seguintes bits: `1001 0110` ou` 1001 0101`. O primeiro bit da esquerda é chamado de “bit 7” e o bit final é chamado de “bit 0”. Eles NÃO são chamados de bits 0-7, nem bits 8-1. A seguir temos uma visão geral dos bits:

```
Bit 7654 3210

    1001 0110
    1001 0101
    .... ....
```

A tabela abaixo mostra um modo relativamente fácil de memorizar binários.

| Binário | Hexadecimal |
| :--- | :--- |
| `% 0000 0001` | `$ 01` |
| `% 0000 0010` | `$ 02` |
| `% 0000 0100` | `$ 04` |
| `% 0000 1000` | `$ 08` |
| `% 0001 0000` | `$ 10` |
| `% 0010 0000` | `$ 20` |
| `% 0100 0000` | `$ 40` |
| `% 1000 0000` | `$ 80` |

Observe que há um espaço entre 4 bits para facilitar a leitura, embora os assemblers geralmente não aceitem ess formato. Os grupos de 4 bits são chamados de "nibbles" e, para os propósitos deste capítulo, eles existem para tornar o binário mais fáceis de serem lidos. Um nibble corresponde a um dígito em hexadecimal.

O SNES é capaz de trabalhar com números de 8 e 16 bits. Enquanto os números de 8 bits são chamados de byte, os números de 16 bits são chamados de "word". Eles tem a seguinte aparência quando convertidos para binarios de 16 bits: `10000101 11010101` (que é iqual a `$85D5` em hexadecimal\). No caso de números de 16 bits, o bit mais à esquerda é chamado de "bit 15" ou , enquanto o bit mais à direita é chamado de "bit 0":

```text
    1111 11             (leia de cima para baixo)
Bit 5432 1098 7654 3210
    1000 0101 1101 0101
    0000 0000 1001 0110
    .... .... .... ....
```

## Flags

O binário é imensamente útil quando você está atribuindo a um valor hexadecimal várias finalidades, como uma chave que liga e desliga determinados recursos. Esses bits são chamados de "Flags" e geralmente são usados para economizar espaço na memória dos jogos.

Por exemplo, você pode dividir um byte em 8 bits, com cada bit tendo um significado diferente. O bit 7 pode indicar que um nível tem chuva ou não. O bit 6 pode indicar que um layout de nível é horizontal ou vertical. O bit 5 pode indicar que a configuração do nível é durante o dia ou noite, etc. Dessa forma, você pode compactar as informações em um único byte. Ficaria assim em binário:

```text
10100000
││└───── Sinaliza que "Está de dia"
│└───── Sinaliza que "Está no nível horizontal"
└───── Sinaliza que "Está chovendo"
```

Finalmente, aqui está uma visão geral de como contar em decimal, hexadecimal e binário:

| Decimal | Hexadecimal | Binário |
| :--- | :--- | :--- |
| `00` | `$ 00` | `% 0000 0000` |
| `01` | `$ 01` | `% 0000 0001` |
| `02` | `$ 02` | `% 0000 0010` |
| `03` | `$ 03` | `% 0000 0011` |
| `04` | `$ 04` | `% 0000 0100` |
| `05` | `$ 05` | `% 0000 0101` |
| `06` | `$ 06` | `% 0000 0110` |
| `07` | `$ 07` | `% 0000 0111` |
| `08` | `$ 08` | `% 0000 1000` |
| `09` | `$ 09` | `% 0000 1001` |
| `10` | `$ 0A` | `% 0000 1010` |
| `11` | `$ 0B` | `% 0000 1011` |
| `12` | `$ 0C` | `% 0000 1100` |
| `13` | `$ 0D` | `% 0000 1101` |
| `14` | `$ 0E` | `% 0000 1110` |
| `15` | `$ 0F` | `% 0000 1111` |
| `16` | `$ 10` | `% 0001 0000` |
| `17` | `$ 11` | `% 0001 0001` |
| ... | ... | ... |
| `254` | `$ FE` | `% 1111 1110` |
| `255` | `$ FF` | `% 1111 1111` |

## Notação

Às vezes, os bits podem ser escritos de forma inconsistente, como `11` ou` 110 0000`. Isso torna o número binário mais difícil de ler, porque a convenção geral é escrever bits em grupos de oito. Para lê-los, você precisará adicionar zeros antes dos dígitos até que haja 8 bits ou 16 bits no total.

Em 8 bits:

* `11` torna-se `00000011`
* `1100000` torna-se `01100000`

Em 16 bits:

* `11` torna-se `00000000 00000011`
* `1100000` torna-se `00000000 01100000`

Você pode converter entre decimal, hexadecimal e binário, usando o modo de "programação" da calculadora do Windows. Existem também muitas calculadoras online que podem fazer isso. A sintaxe do assembly também aceita números decimais, portanto, geralmente não é necessário converter entre decimal e hexadecimal.
