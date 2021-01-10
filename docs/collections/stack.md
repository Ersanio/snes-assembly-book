# A pilha
A pilha é um tipo especial de 'tabela' localizada dentro da RAM do SNES. Imagine a pilha como uma pilha de pratos. Você só pode adicionar (push) ou remover (pull/pop) um prato pela parte superior. Você pode visualizar tal pilha como algo assim:

```
|   |
|[ ]|
|[ ]|
|[ ]|
|[ ]|
‾‾‾‾‾
```
As 'caixas' vazias no exemplo acima são basicamente os valores dentro da pilha e podem conter qualquer valor. A coleção de caixas está fechada por todos os lados, exceto pela parte de cima.

> Tecnicamente falando, como a pilha é uma área dentro da RAM, você pode acessar qualquer valor que desejar usando modos especiais de endereçamento relativo à pilha em vez de usar apenas os opcodes 'push' e 'pull'. Para os propósitos deste capítulo, iremos assumir que a pilha na verdade é uma pilha perfeita de pratos. 


## Push
'Pushing' é a ação de adicionar (colocar) um valor no topo da pilha. Segue um exemplo:

```
 [$32]
  | push
  v
|     |   |[$32]|
|[$B0]|   |[$B0]|
|[$A9]| > |[$A9]|
|[$82]|   |[$B2]|
|[$14]|   |[$14]|
‾‾‾‾‾‾‾   ‾‾‾‾‾‾‾
```
Como pode ver, o valor $32 é adicionado no topo da pilha. A pilha agora tem cinco valores em vez de quatro.

## Pull/Pop
'Pulling' (ou 'popping') é a ação de ler (recuperar) um valor do topo da pilha. Aqui está um exemplo:

```
           [$32]
   ^ pull
   |
|[$32]|   |     |
|[$B0]|   |[$B0]|
|[$A9]| > |[$A9]|
|[$82]|   |[$B2]|
|[$14]|   |[$14]|
‾‾‾‾‾‾‾   ‾‾‾‾‾‾‾
```
Como pode ver, o valor $32 é recuperado do topo da pilha.

> *Todas* as instruções de 'pulling' afetam as flags Negativo e Zero do processador.

## Push (colocar/adicionar) A, X, Y
Existem opcodes para adicionar o valor atual contido nos registradores `A`, `X` ou `Y` na pilha:
|Opcode|Nome completo|Significado|
|-|-|-|
|**PHA**|Push A onto stack|Adiciona o valor atual de `A` na pilha.|
|**PHX**|Push X onto stack|Adiciona o valor atual de `X` na pilha.|
|**PHY**|Push Y onto stack|Adiciona o valor atual de `Y` na pilha.|

## Pull (ler/retirar) A, X, Y
Também existem opcodes para retirar o valor atual da pilha para os registradores `A`, `X` ou `Y`:
|Opcode|Nome completo|Significado|
|-|-|-|
|**PLA**|Pull into A|Retira um valor da pilha e coloca no registrador `A`.|
|**PLX**|Pull into X|Retira um valor da pilha e coloca no registrador `X`.|
|**PLY**|Pull into Y|Retira um valor da pilha e coloca no registrador `Y`.|

## Exemplo
Imagine o seguinte cenário: O registrador `X` deve manter o valor $19, não importa como, mas você precisa usar `X` para outra coisa. Como se faria isso? Você deve usar `PHX` para preservar o valor de `X` na pilha, antes de usar uma instrução que modifica o conteúdo de `X`:
```
                   ; Imagine que X tem o valor $19 na pilha
PHX                ; Adiciona X ($19) na pilha. Resultado: 1° valor da pilha = #$19
LDX $91            ; Carrega o valor de $7E0091 no registrador X
LDA $1000, x       ; \ X agora está modificado, e usamos isso para indexar RAM
STA $0100          ; /
PLX                ; Restaura X. X agora contém $19 novamente
```

> Os registradores `A`, `X` e `Y` não tem uma pilha separada. Há apenas uma pilha, especificada pelo 'registrador de ponteiro de pilha' (ou 'stack pointer register'). Para explicações mais detalhadas sobre o registrador de ponteiro de pilha (stack pointer register), veja: [Stack pointer register](../processor/stackpointer.md)

## Operações de pilha em modo 16-bits
Todas as explicações e comportamentos anteriores também se aplicam a operações de pilha de 16-bits. Só que você está adicionando e retirando valores de 16-bits e não 8-bits.

Isso também significa que se você colocar dois valores de 8-bits na pilha, poderá retirar um único valor de 16-bits futuramente.

## Outras operações de 'push' e 'pull'
Outras operações de 'push' (colocar/adicionar) e 'pull' (ler/retirar), que não são afetados pelos modos 8-bits ou 16-bits para `A`, `X` e `Y`:

|Opcode|Nome completo|Significado|Tamanho do valor|
|-|-|-|-|
|**PHB**|Push data bank register|Coloca o valor atual do registrador do data bank na pilha.|8-bit|
|**PLB**|Pull data bank register|Retira um valor da pilha e coloca no registrador do data bank.|8-bit|
|**PHD**|Push direct page register|Coloca valor atual do registrador de direct page na pilha.|16-bit|
|**PLD**|Pull direct page register|Retira um valor da pilha e adiciona ao registrador de direct page.|16-bit|
|**PHP**|Push processor flags|Adiciona o valor atual do registrador de flags do processador na pilha.|8-bit|
|**PLP**|Pull processor flags|Retira um valor da pilha e adiciona no registrador de flags do processador.|8-bit|
|**PHK**|Push program bank|Adiciona o valor de banco do opcode atualmente executado (o opcode PHK) na pilha. Não há versão 'pull' (ler/retirar) deste.|8-bit|
|**PEA $XXXX**|Push effective address|Adiciona um valor específico de 16-bits  na pilha. Não se deixe enganar pelo nome do opcode. Ele não lê nenhum endereço.|16-bit|
|**PEI ($XX)**|Push effective indirect address|Adiciona o valor do endereço fornecido da direct page na pilha. O registrador de direct page pode afetar o endereço fornecido.|16-bit|
|**PER *rótulo***|Push program counter relative|Uma adição bem complicada. Simplificando, ele coloca o endereço absoluto do rótulo (label) indicado, na pilha. O que acontece é que, quando você fornece um rótulo, o assembler calcula a distância relativa entre o opcode PER e o rótulo fornecido. Tal distância relativa é um valor 16-bits com sinal, e portanto o opcode é montado em `PER $XXXX`.|16-bit|