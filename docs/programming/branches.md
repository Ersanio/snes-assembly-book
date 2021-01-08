# Comparações, desvios e labels

Você pode executar determinadas partes do código dependendo de certas condições. Para isso, você terá que fazer uso de instruções de comparação e desvio. As instruções de comparação, equiparam o conteúdo de A, X ou Y com um valor qualquer. Um opcode de desvio controla o fluxo do programa, dependendo (ou não) do resultado de uma comparação.

## Branches
Branches são instruções que controlam o fluxo do código que, dependendo do resultado das comparações, os Branches "desviam" para outros partes do código que são predeterminados por **labels**.

As instruções de desvio são limitadas a um intervalo de -128 a 127 bytes. Isso significa que elas só podem saltar 128 bytes para trás ou 127 bytes para frente, em relação ao program counter. Uma exceção é o `BRL` (Branch Long). `BRL` possui um intervalo de 32768 bytes (8000 em hexadecimal), que é igual ao tamanho de  um banco inteiro. Se o branch sair do intervalo, o assembler acusará um erro. Você terá que encontrar uma maneira de colocar o label de destino ao alcance do branch. O capítulo "dicas e truques" explicará mais sobre isso.

## Labels
Os labels são rótulos de textos colocados no código para demarcar um ponto de entrada de um salto ou uma “tabela”. Os labels não são instruções nem nada parecido. É basicamente uma maneira mais fácil de especificar um endereço, pois labels são transformados em números pelo assembler. É uma boa prática dar nomes significativos aos labels, para seu próprio bem. Os códigos de exemplo neste capítulo farão uso de labels.

## CMP
Para fazer comparações, você geralmente usa o conteúdo de `A` e um outro valor qualquer. A principal forma de fazer isso é com o instrução `CMP`.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**CMP**|Compare A|Compara A com outro valor.|

`CMP` pega o valor que está carregado em A e compara com um parâmetro especifico. Depois de usar uma instrução `CMP`, você precisará usar uma instrução que realizará o “tipo de desvio” que você deseja que ocorra.

Também é possível comparar valores de 16-bit. Basta alterar `CMP #$xx` para `CMP #$xxxx`.

## BEQ e BNE
Existem instruções de desvio que saltam dependendo se a comparação dos valores forem iguais ou diferentes.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**BEQ**|Branch if equals|Salta se o valores comparados forem iguais.|
|**BNE**|Branch if not equals|Salta se o valores comparados forem diferentes.|

`BEQ` salta se os valores forem iguais. Veja o exemplo abaixo:

```
LDA $00            ; Carrega o valor atual do endereço da RAM $7E0000 em A
CMP #$02           ; Compara A com o valor imediato $02
BEQ Label1         ; Se A = $02, vá para Label1. NOTA: Diferencia maiúsculas e minúsculas
LDA #$01           ; \ Senão
STA $1245          ; / Armazena o valor $01 no endereço $7E1245.
RTS                ; Esta instrução é usada para encerrar uma rotina.

Label1:
STZ $19            ; Armazene zero em $7E0019
RTS                ; Fim.
```
Este código armazenará zero ($00) em $7E0019 quando $7E0000 possuir  o valor $02, caso contrário o código armazenará o valor $01 em $7E1245. Como você pode ver, `BEQ` irá "saltar" para uma parte do código quando os valores comparados forem iguais, pulando um determinado código. Neste caso, o código salta para o código localizado na label “Label1”

`BNE` salta se os valores forem diferentes. Segue mais um exemplo:

```
LDA $00           ; Carrega o valor atual do endereço da RAM $7E0000 em A
CMP #$02          ; Compara A com $02
BNE Label1        ; A NÃO = $02, não faça nada e finalize o código.
LDA #$01          ; \ Senão
STA $1245         ; / Armazene algo no endereço da RAM $7E1245
Label1:           ;
RTS               ; Fim.
```
O código acima armazenará $01 em $7E1245, se $7E0000 possuir o valor $02. Senão, o código não fará nada e simplesmente finalizará.

## Comparando endereços

Você também pode comparar a partir de endereços da RAM. Por exemplo:
```
LDA $00           ; Carregue o valor de $7E0000 em A
CMP $02           ; Compare A com $7E0002
BEQ Equal         ; Vá para "Equal" se igual
```
Quando os endereços $7E0000 e $7E0002 tiverem os mesmos valores, o salto ocorrerá.

## CPX e CPY

Você também pode comparar usando os registradores `X` e `Y`.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**CPX**|Compare X|Compara X com outro valor.|
|**CPY**|Compare Y|Compara Y com outro valor.|
Não é somente `A` que pode ser comparado. Por exemplo, você pode carregar um valor em `X` ou `Y` e compará-lo com outro valor. Aqui temos um exemplo de uso do `X`:

```
LDX $00           ; Carregue o valor de $7E0000 em X
CPX $02           ; Compare X com $7E0002
BEQ Equal         ; Vá para "Equal" se igual
```
Teremos o mesmo resultado do exemplo com a comparação de endereços. Você também pode comparar `Y` usando `CPY`. No entanto, você não pode misturar registradores. O código a seguir está errado:
```
LDX $00
CMP $02
BEQ Equal
```
`CMP $02` tentaria comparar o endereço $7E0002 com o registrador `A` em vez de `X`, causando resultados inesperados.

## BMI e BPL
Estas são instruções de desvio que saltam dependendo se um valor é com sinal  ou sem sinal.

| Opcode | Nome completo | Explicação |
| - | - | - |
| **BMI** | Branch if minus | Salta se a última operação resultou em um valor negativo. |
| **BPL** | Branch if plus | Salta se a última operação resultou em um valor positivo. |

`BMI` salta se a última operação resultar em um valor negativo. Os valores negativos são os valores de $80 a $FF. `BPL` salta se a última operação resultar em um valor positivo, ou seja, de $00 a $7F.

## BCS e BCC
Estes são instruções de desvio, dependendo se um valor é maior ou menor que.
| Opcode | Nome completo | Explicação |
| - | - | - |
| **BCS** | Branch if carry set | Salta se o valor carregado for maior ou igual ao valor comparado. |
| **BCC** | Branch if carry clear | Salta se o valor carregado for menor que o valor comparado. |

`BCS` salta se o valor carregado for igual ou maior que o valor comparado. Como alternativa, também salta quando a flag de carry estiver ativada.

`BCC` salta se o valor carregado for menor que o valor comparado. Como alternativa, também salta quando o a flag de carry não estiver ativada. Observe que ao contrário do `BCS`, `BCC` não salta se os valores comparados forem iguais.

## BVS e BVC
Esses são instruções de desvio, dependendo se um valor resulta em um overflow matemático ou não.
| Opcode | Nome completo | Explicação |
| - | - | - |
| **BVS** | Branch if overflow set | Salta se a comparação causar um overflow matemático. |
| **BVC** | Branch if overflow clear | Salta se a comparação não causar um overflow matemático. |

As flags de  “overflow” e "carry" são flags do processador, serão abordadas posteriormente no tutorial.

## BRA e BRL
Estas são instruções de desvios incondicionais que sempre serão executados.
| Opcode | Nome completo | Explicação |
| - | - | - |
| **BRA** | Branch always | Sempre efetua um desvio |
| **BRL** | Branch always long | Sempre efetua um desvio, mas com maior alcance |

`BRA` **sempre** saltará; ele nem mesmo valida as comparações.
O `BRL` faz o mesmo, mas com um maior alcance, o suficiente para cobrir metade de um banco para cada direção.