# Flags do processador
Como vimos no capítulo "Modos de Operação", o SNES pode alternar entre o modo 8-bit e 16-bit usando as instruções `REP` e `SEP`. Elas alteram os flags do processador, que por sua vez alteram o comportamento do SNES. Dois dos flags do processador são dedicadas ao modo de 8-bit ou 16-bit dos registradores `A` e `X` e `Y`. Há no total 9 flags que compõem o registrador de status do processador (Processor status) e são representadas com um único byte:

```
Flags do processador
Bits: 7   6   5   4   3   2   1   0

                                 |e├──── Emulação: 0 = Modo nativo
     |n| |v| |m| |x| |d| |i| |z| |c|
     └┼───┼───┼───┼───┼───┼───┼───┼┘
      │   │   │   │   │   │   │   └──────── Carry: 1 = Carry
      │   │   │   │   │   │   └───────────── Zero: 1 = Resultado zero
      │   │   │   │   │   └────────── IRQ Disable: 1 = IRQ Desativado
      │   │   │   │   └───────────── Decimal Mode: 1 = Decimal, 0 = Hexadecimal
      │   │   │   └──────── Index Register Select: 1 = 8-bit, 0 = 16-bit
      │   │   └─────────────── Accumulator Select: 1 = 8-bit, 0 = 16-bit
      │   └───────────────────────────── Overflow: 1 = Overflow
      └───────────────────────────────── Negative: 1 = Negativo
```

Você pode memorizar esses flags do processador lembrando do termo `nvmxdizc`. Este capítulo explicará todos os flags do processador em detalhes.

## Negative flag (n)
Ele será habilitado quando o valor da última operação resultar em um numero entre $80-$FF ou $8000-$FFFF (dependendo do modo de 8-bit/16-bit).

A maioria das instruções modificam e dependem dessa flag.  Essas instruções geralmente são as que fazem carregamentos, comparações e pulls, e também outras instruções que serão abordadas no capítulo [Hardware math](../math /arithmetic.md).

## Overflow flag (v)
Apenas quatro instruções fazem uso dessa flag. Essas instruções são: `CLV`, `ADC`, `SBC` e `BIT`. 
Este flag é habilitado quando o valor resultante da operação anterior "cair" fora intervalo de -128 a +127.
Por exemplo, $90+$C8=$158. Em decimal, seria -112+(-56) = -168. O valor -168 está fora do intervalo de -128 e + 127, portanto, ocorre o overflow.

Para obter explicações mais detalhadas sobre como a overflow flag é modificada, consulte os capítulos [Hardware math](../math /arithmetic.md)  e [Bitwise operations](../math/logic.md).

O flag overflow não afeta o comportamento do SNES. Em contra partida, existem algumas instruções de desvio que fazem uso desse flag.

## Memory select (m)

Este flag determina se o registrador acumulador deve estar no modo de operação de 8-bit ou 16-bit.

Quando está definida como 1, ativa o modo de 8-bit para `A`.
Quando está definida como 0, ativa o modo de 16-bit para `A`.

## Index select (x)
Esse flag determina se os registradores `X` e `Y` devem estar no modo de operação de  8-bit ou 16-bit

Quando está definida como 1, ativa o modo de 8-bit para `X` e `Y`.
Quando está definida como 0, ativa o modo de 16-bit para `X` e `Y`.

## Decimal mode (d)
Definir esta flag como 1, faz com que o SNES entre no modo decimal. Isso afetará *apenas* as instruções `ADC`, `SBC` e `CMP`.

Essas instruções ajustam o acumulador em tempo real. Isso significa que, se você, por exemplo, adicionar `$03` a`$09`, o resultado será `$12` ao invés de`$0C`.

Embora as operações matemáticas do modo decimal afetem adequadamente o flag carry e o flag negative, elas não fazem o mesmo com o flag overflow.

# Codificação binária decimal

Como esses números são armazenados em decimais, eles são armazenados em 'codificação binária decimal'(BCD). BCD é basicamente o mesmo que hexadecimal, só que com a valores $0A-0F, $1A-1F, etc... 'cortados'. A tabela a seguir mostra como funciona a contagem em BCD:

| Binário | Hexadecimal | Decimal | BCD |
| - | - | - | - |
|%0000 0000 | $00 | #00 |%0000 0000 |
|%0000 0001 | $01 | #01 |%0000 0001 |
|%0000 0010 | $02 | #02 |%0000 0010 |
|%0000 0011 | $03 | #03 |%0000 0011 |
|%0000 0100 | $04 | #04 |%0000 0100 |
|%0000 0101 | $05 | #05 |%0000 0101 |
|%0000 0110 | $06 | #06 |%0000 0110 |
|%0000 0111 | $07 | #07 |%0000 0111 |
|%0000 1000 | $08 | #08 |%0000 1000 |
|%0000 1001 | $09 | #09 |%0000 1001 |
|%0000 1010 | $0A | Não disponível | Não disponível |
|%0000 1011 | $0B | Não disponível | Não disponível |
|%0000 1100 | $0C | Não disponível | Não disponível |
|%0000 1101 | $0D | Não disponível | Não disponível |
|%0000 1110 | $0E | Não disponível | Não disponível |
|%0000 1111 | $0F | Não disponível | Não disponível |
|%0001 0000 | $10 | #10 |%0001 0000 |
|%0001 0001 | $11 | #11 |%0001 0001 |
|%0001 0010 | $12 | #12 |%0001 0010 |
| ... | ... | ... | ... |
|%1001 1000 | $98 | #98 |%1001 1000 |
|%1001 1001 | $99 | #99 |%1001 1001 |
|%1001 1010 | $9A | Não disponível | Não disponível |
|%1001 1011 | $9B | Não disponível | Não disponível |
| ... | ... | ... | ... |

Neste modo, o SNES suporta cálculos matemáticos com valores de `$00` a` $99`. No modo 16-bit do acumulador, estes valores seriam de `$0000` a` $9999`.

## Interrupt disable (i)
Este flag determina se o IRQ do SNES está desabilitado ou não.

Quando está definido como 1, o IRQ é desativado.
Quando está definido como 0, o IRQ é habilitado.


## Flag zero (z)
A maioria das instruções modificam e dependem dessa flag.  Essa instruções geralmente são as instruções que fazem carregamentos, comparações e pulls, e também outras instruções que serão abordadas no capítulo [Hardware math](../math /arithmetic.md).

O flag zero não afeta o comportamento do SNES. Em contra partida, existem algumas instruções de desvio que fazem uso dessa flag.

## Carry flag (c)
O SNES oferece suporte à operações matemáticas na forma de adição e subtração de números. Ele também suporta operações bitwise e bit shifting. O SNES também suporta operações lógicas, como AND ou XOR.

A “carry flag” é uma flag do processador usado para a maioria dessas operações aritméticas. Além disso, também é usada em instruções de desvio. Essa flag tem o mesmo conceito de "vai 1" que você aprende na escola primária. Em uma  típica conta de adição com lápis e papel, você escreveria assim:
```
  ¹
  27
+ 59
----
  86
```
7 + 9 é igual a 16, portanto *vai* o 1 para a casa decimal a esquerda.

Considerando que o carry é uma "flag", quando a flag de carry estiver definida com 0, o carry também será 0 e quando estiver definida com 1, o carry também será 1. Você pode assumir com segurança que a carry flag é o “9º bit” do  registrador `A` quando `A` estiver no modo de 8-bit e o “17º bit” quando A estiver no modo de 16-bit. 

Supondo que A esteja no modo de 8-bit, a carry flag ficará assim:

```
BBBBBBBB C
```
Onde C é a carry flag e B são os bits - em outras palavras, o conteúdo do registrador A.

Dependendo de como a carry flag estiver definida , várias instruções de operações matemáticas e de bitwise se comportarão de maneira diferente no SNES.

## Flag de modo de emulação (e)
Definir esta flag, faz com que o 65c816 se comporte como o 6502. Quando você entra no modo de emulação:

* O high byte registrador stack pointer permanece estático como $01
* Os registradores A, X e Y serão sempre de 8-bit
* Os registradores  program bank e data bank são definidos como $00
* O registrador direct page é inicializado como $0000, e o high byte alto permanece estático como $00

O modo de emulação do 65c816 também corrige alguns dos bugs que o 6502 tinha. Por exemplo, o modo de endereçamento indireto `JMP` agora envolve os endereços corretamente. Por exemplo: `JMP ($10FF)` obterá agora o high byte de `$1100`, ao invés de` $1000`.

### Flags do processador
O modo de emulação possui um conjunto diferente de flags do processador.

```
Flags do processador
Bits: 7   6   5   4   3   2   1   0

                                 |e├─── Emulation: 1 = Emulation Mode
     |n| |v| |1| |b| |d| |i| |z| |c|
     └┼───┼───┼───┼───┼───┼───┼───┼┘
      │   │   │   │   │   │   │   └──────── Carry: 1 = Carry
      │   │   │   │   │   │   └───────────── Zero: 1 = Resultado zero
      │   │   │   │   │   └────────── IRQ Disable: 1 = Desabilitado
      │   │   │   │   └───────────── Decimal Mode: 1 = Decimal, 0 = Hexadecimal
      │   │   │   └─────────────────── Break Flag: 1 = Break executado
      │   │   └─────────────────────────── Unused: 1
      │   └───────────────────────────── Overflow: 1 = Overflow
      └───────────────────────────────── Negative: 1 = Negativo
```

Como você pode ver, é muito semelhante as flags do processador do SNES, com algumas exceções. Os bits de mode select de A e X e Y são substituídos.

### Flag Unused
Esta flag não é usada e é sempre definida como 1.

### Flag Break
Esta flag é definida como 1 quando uma instrução `BRK` é executada no modo de emulação, portanto, ela apenas indica que houve uma interrupção; esta flag não afeta realmente o SNES.