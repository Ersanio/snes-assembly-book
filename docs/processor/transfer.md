# Transferências
Imagine a seguinte situação: Você está usando o registrador X e gostaria de adicionar mais #$40. Há o `INX` que aumenta o valor em X em um, mas que tal somar $40? Não há instrução que aumenta o valor em X em mais de 1, [mas há um](.../math/arithmetic.md) que aumenta o valor em A. Como se passa valor A para X?

Há uma série de instruções que possuem exatamente essa finalidade, e eles são chamados de 'transfers'. Elas realizam transferências de valores de um registador para o outro, todas começando com a letra T.

## Transferring A, X and Y
Existem instruções que transferem os valores entre os registradores A, X e Y. Todas as combinações são possíveis:
|Opcode|Nome completo|Explicação|
|-|-|-|
|**TAX**|Transfer A to X|Transfere o valor de A para X|
|**TAY**|Transfer A to Y|Transfere o valor de A para Y|
|**TXA**|Transfer X to A|Transfere o valor de X para A|
|**TXY**|Transfer X to Y|Transfere o valor de X para Y|
|**TYA**|Transfer Y to A|Transfere o valor de Y para A|
|**TYX**|Transfer Y to X|Transfere o valor de Y para X|
As instruções são auto-explicativas. Veja um exemplo de uma transferência:
```
LDA #$45 ;A = $45
LDY #$99 ;Y = $99
TAY      ;A = $45, Y = $45
```
Como você pode ver, os valores são *copiados* para o registrador de destino.

## Modo 8 e 16-bit
As transferências funcionam da mesma forma quando os registradores de origem e destino são ambos de 16-bit:

```
LDA #$4512 ;A = $4512
LDY #$9900 ;Y = $9900
TAY        ;A = $4512, Y = $4512
```

Se você quiser transferir o valor de um registrador de 8-bit para um de 16-bit, o SNES analisa o tamanho do registrador alvo, e transfere apenas o valor do low byte. O exemplo a seguir, temos uma transferência de Y de 8-bit para um a A de 16-bit :

```
LDA #$4512 ;A = $4512
LDY #$99   ;Y = $0099
TYA        ;A = $4599, Y = $0099
```
E aqui mais um exemplo de transferência de Y de 16-bit para um a A de 8-bit :

```
LDA #$45   ;A = $xx45
LDY #$99AA ;Y = $99AA
TYA        ;A = $xxAA, Y = $99AA
```
No segundo exemplo, o `xx` pode ser qualquer coisa. Lembre-se de que [na introdução dos registros A, X e Y](.../the-basics/register.md), é mencionado que o registrador A sempre terá  16 bits. O high byte não é afetado durante a transferência, mesmo que A esteja no modo 8-bit. Se o valor de a era `$1245` antes da transferência, após a transferência seu valor será `$12AA`  Isto não se aplica a X e Y, pois seus bytes altos serão imediatamente apagados quando entram do modo 8-bit.

## Transferência de registros de uso geral
Há mais instruções que envolvem os registradores de uso geral do SNES.

| Opcode  | Nome completo                        | Explicação                                                   |
|-|-|-|
|**TCD**|Transfer A to direct page register|Transfere o valor 16-bit de A para o registrador direct page, independentemente de A estar ou não no modo 16-bit|
|**TDC**|Transfer direct page register to A|Transfere o valor de 16-bit do registrador direct page para A, independentemente de A estar ou não no modo 16-bit|
|**TCS**|Transfer A to stack pointer register|Transfere o valor de 16-bit de A para o registrador stack pointer, independentemente de A estar ou não no modo 16-bit|
|**TSC**|Transfer stack pointer register to A|Transfere o valor de 16-bit do registrador stack pointer para  A, independentemente de A estar ou não no modo 16-bit|
|**TXS**|Transfer X to stack pointer register|Transfere o valor de 16-bit de X para o registrador stack pointer, independentemente de X estar ou não no modo 16-bit. Note que quando X estiver no modo de 8 bits, seu o high byte será $00|
|**TSX**|Transfer stack pointer register to X|Transfere o valor de 16-bit do registrador stack pointer para  X. Observe que quando X estiver no modo 8-bit, seu high byte permanece $00 após a transferência|