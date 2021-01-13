# Opcodes diversos

Este capítulo explica opcodes que não se enquadram em outros capítulos.

## NOP
|Opcode|Nome completo|Explanation|
|-|-|-|
|**NOP**|No operation| Não faz absolutamente nada. |

É usado, com frequêcia, para desabilitar opcodes existentes em uma ROM sem mudar o bytecode. Também pode ser usado para dar tempo aos [registradores matemáticos do hardware](../math / math.md) fazerem seu trabalho.

## XBA
|Opcode|Nome completo|Explicação|
|-|-|-|
|**XBA**|Exchange B and A|Troca os bytes high e low do registrador A, indiferente ao tamanho do registrador.|

Um exemplo:
```
LDA #$0231         ; A = $0231
XBA                ; A torna-se $3102
```

Isso funciona até mesmo com A de 8 bits, o high byte de `A` fica 'oculto' em vez de definido como $00:

```
LDA #$12           ; A = $xx12
XBA                ; A = $12xx
LDA #$05           ; A = $1205
```

## WAI
|Opcode|Nome completo|Explicação|
|-|-|-|
|**WAI**|Wait for interrupt|Suspende a CPU do SNES até que ocorra um NMI ou IRQ.|

Faz exatamente o descrito, suspende a CPU do SNES até que ocorra um NMI ou IRQ (ambos são interrupções). Na prática o opcode entra em um loop infinito até que aconteça uma interrupção.

## STP
|Opcode|Nome completo|Explicação|
|-|-|-|
|**STP**|Stop the clock|Neste caso o 'clock' é a CPU do SNES, que é interrompida completamente até que ocorra um soft ou hard reset.|

Faz exatamente o descrito, suspende a CPU do SNES até que você pressione reset ou desligue e ligue o SNES. Ele reduz o consumo de energia do SNES, caso a pouca economia obtida faça diferença para você.

## BRK
|Opcode|Nome completo|Explicação|
|-|-|-|
|**BRK**|Software break|Faz uma pausa acontecer.|

Este opcode faz com que o SNES salte para o [break vector](../indepth/vector.md). Também toma um byte como parâmentro. Eis um exemplo:
```
BRK #$02
```
O parâmentro não tem nenhuma finalidade em particular, mas você pode escrever uma captura para `BRK`, em que poderia ler qual o valor de `BRK` e tomar determinadas atitudes dependendo desse valor.

Quando o opcode `BRK` é executado, acontece o que se segue:

* Primeiro, o endereço da instrução (24-bit) após `BRK #$xx` é enviado para a pilha;
* Então, o registrador de flags do processador (8-bit) é enviado para a pilha;
* O flag interrupt disable é habilitado (similar a `SEI`);
* O flag decimal mode é desabilitado (similar a `CLD`);
* O registrador program bank recebe o valor $00;
* O program counter é carregado com o endereço do break vector. Em outras palavras, o código salta para o endereço do break vector.

## COP
|Opcode|Full name|Explanation|
|-|-|-|
|**COP**|Coprocessor empowerment|Efeitos similares aos de BRK, o SNES não tem um coprocessador para empoderar.|

Este opcode faz com que o SNES salte para o [hardware vector COP](../indepth/vector.md). Também toma um byte como parâmentro. Eis um exemplo:

```
COP #$04
```
O parâmentro não tem nenhuma finalidade em particular, mas você pode escrever uma captura para `COP`, em que poderia ler qual o valor de `COP` e tomar determinadas atitudes dependendo desse valor.

Quando o opcode `COP` é executado, acontece o que se segue:
* Primeiro, o endereço da instrução (24-bit) após `COP #$xx` é enviado para a pilha;
* Então, o registrador de flags do processador (8-bit) é enviado para a pilha;
* O flag interrupt disable é habilitado (similar a `SEI`);
* O flag decimal mode é desabilitado (similar a `CLD`);
* O registrador program bank recebe o valor $00;
* O program counter é carregado com o endereço do hardware vector COP. Em outras palavras, o código salta para o endereço do cop vector.

## WDM
|Opcode|Nome completo|Explicação|
|-|-|-|
|**WDM**|Reservado para uma expansão futura|Não faz absolutamente nada.|
WDM significa '[William (Bill) David Mensch, Jr.](https://en.wikipedia.org/wiki/Bill_Mensch)', o designer do 65c816. Este opcode foi reservado para uma possibilidade de opcodes com múltiplos bytes. Além disso, recebe um parâmetro, por exemplo:
```
WDM #$01
```
Esse opcode tem o mesmo efeito de um NOP, não faz nada.