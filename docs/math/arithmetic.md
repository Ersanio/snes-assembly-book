# Operações Aritméticas

Em algum momento, você provavelmente vai querer *incrementar* o endereço $7E000F da RAM em $01, mas um simples `LDA` e `STA`  não resolverão, isso porque vocês simplesmente está alterando o conteúdo do endereço da RAM para $01, ao invés de incrementá-lo. Talvez você prefira incrementar em 2 ou até mesmo multiplicar por 2.

O SNES possui algumas instruções que são capazes de fazer operações matemáticas básicas.

## INC e DEC

Essas são instruções para somar ou subtrair um valor em 1.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**INC**|Increase|Incrementa o acumulador ou a memória em 1.|
|**DEC**|Decrease|Decrementa o acumulador ou a memória em 1.|

`INC` e `DEC` suportam tanto o acumulador quanto endereços. Segue um exemplo de incremento do acumulador em 1:

```
LDA #$02           ; Carrega o valor $02 no acumulador
INC A              ; Incrementa o acumulador em 1. Agora A é igual a $03
```
E mais um exemplo para decrementar o acumulador em 1:
```
LDA #$02           ; Carrega o valor $02 no acumulador
DEC A              ; Decrementa o acumulador em 1. Agora A é igual a $01
```
A seguir um exemplo para de como incrementar o valor de um endereço em 1:
```
INC $0F            ; Incrementa o valor em $7E000F em 1
RTS                ; Fim.
```
E mais um exemplo para deprementar o valor do endereço em 1:
```
DEC $0F            ; Decrementa o valor em $7E000F em 1
RTS                ; Fim
```

Quando você usa o `INC` e o valor que está sendo incrementado for o $FF, resultará em $00 e o flag zero será definido como 1. Por outro lado, se  você usa `DEC` e o valor que está sendo modificado é $00, resultaria em $FF.

> `INC` e `DEC` não funcionam com modos de endereçamento longos. Eles funcionam apenas com modos de endereçamento de direct page ou absoluto. Portanto, instruções como `INC $7E000F` são inválidas. Em contrapartida,, você deve usar `INC $000F` ou `INC $0F`.
>
> Por que não existe um modo de endereçamento longo? O processador foi simplesmente criado para funcionar dessa maneira, então você terá que lidar com isso de uma forma ou de outra.

## INX, DEX, INY e DEY
Também existem instruções para incrementar ou decrementar os valores dos indexadores X e Y:

|Opcode|Nome completo|Explicação|
|-|-|-|
|**INX**|Increase X|Incrementa X em 1.|
| **DEX** | Decrease X    |Decrementa X em 1.|
|**INY**|Increase Y|Incrementa Y em 1.|
|**DEY**|Decrease Y|Decrementa Y em 1.|

Você não pode usar as quatro intruções acima para manipular um endereço. Elas são usados exclusivamente para os registradores `X` e `Y`.

## ADC e SBC
E se você quisesse aumentar ou diminuir um valor em, digamos, 95, você não ia querer escrever `INC` ou `DEC` 95 vezes. Felizmente, o SNES também tem instruções para essas situações.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**ADC**|Add with carry|Adiciona um determinado valor a A.|
|**SBC**|Subtract with carry|Subtrai um determinado valor de A.|

> Apenas para enfatizar: `ADC` adiciona um valor ao acumulador, não um endereço de RAM. `SBC` subtrai um valor do acumulador.

Com `ADC` e `SBC`, você pode fazer operações de somar e subtrair com valores usando endereços. Segue um exemplo que adiciona 4 ao valor de um endereço:
```
LDA $0F            ; Carrega o valor do endereço em A
CLC                ; Limpa o flag carry
ADC #$04           ; Adiciona $04 a A.
STA $0F            ; Grava o valor de A em $0F
```
Como o `ADC` altera `A` em vez de endereços, você deve primeiro carregar o valor do endereço em `A`, depois fazer a adição e, em seguida, armazenar o resultado de volta no endereço.

Se o flag carry não fosse zerado, seria adicionado $05 ao invés de $04. Ademais, é importante certificar-se acerca do flag carry, desabilitando-o com um `CLC`, antes de usar `ADC`.

O oposto também é válido para subtrair:

```
LDA $0F            ; Carregua o valor do endereço em A
SEC                ; Habilita o flag carry
SBC #$04           ; subtrai $04 de A.
STA $0F            ; Grava o valor de A em $0F
```
Isso subtrairá 4 do conteúdo do endereço de RAM (`$0F-#$04`). Você notará que, para subtrair, habilitamos o flag carry ao invés de habilitá-lo. Se você não habilitar o flag carry, será subtraído $05 em vez de $04. Isso pode parecer estranho, mas é assim  que o processador funciona.

> Resumindo: ao adicionar, use `CLC`. Ao subtrair, use `SEC`.

### Flag carry
Quando você usa `ADC`, se o valor em `A` mudar de $FF passa para $00, o flag carry é habilitado. Isso também é válido para `A` no modo 16-bit, quando o valor passar de $FFFF para $0000.

Já no caso do `SBC`, se o valor em A passar de $00 para $FF, o flag carry é desabilitado. Isso também é válido para `A` no modo 16-bit, quando o valor passar de $0000 para $FFFF.

Com isso, você pode usar o flag carry para verificar se algum valor excedeu o tamanho máximo do registrador.

### Flag overflow

Os opcodes`ADC` e `SBC` são únicos no sentido de que são dois dos três opcodes que podem afetar o flag *signed overflow* do processador como resultado de um cálculo.

O flag overflow é especialmente relevante quando você decide tratar os valores como valores com sinal. Você se lembra do capítulo hexadecimal? Os valores de $00 a $7F são considerados positivos e os valores $80-$FF são considerados negativos.

O flag overflow é definido quando o resultado de uma operação não faz sentido no mundo da matemática:

* Adicionando um valor negativo a um valor negativo e obtendo um valor positivo como resultado, quando deveria ser negativo;
* Adicionando um valor positivo a um valor positivo e obtendo um valor negativo como resultado, quando deveria ser positivo;
* Subtraindo um valor positivo de um valor negativo e obtendo um valor positivo como resultado, quando o valor negativo deveria ser ainda mais negativo;
* Subtraindo um valor negativo de um valor positivo e obtendo um valor negativo como resultado, quando o valor deveria ser ainda mais positivo

As regras matemáticas estão em jogo aqui. Adicionar dois números negativos resulta em um número negativo (`-10 +-1 = -11`). Subtrair dois números negativos resulta em um número negativo (por exemplo, `-10 - -1 = -9`). Subtrair um valor negativo é igual a uma adição (por exemplo, `10 - -1 = 11`). Adicionar um valor negativo equivale a uma subtração (por exemplo, `10 + -1 = 9`). Os cenários descritos nos tópicos acima quebram essas regras.

Aqui estão alguns exemplos do flag overflow sendo habilitado:
```
LDA #$88           ; -120 em decimal
CLC                ; -120 + (-16) = -136
ADC #$F0           ; $88 + $F0 = $78, que é 120, o que não faz sentido matematicamente
```

```
LDA #$80           ; -128 em decimal
SEC                ; -128 - 16 = -144
SBC #$10           ; $80 - $10 = $70, que é 112, o que não faz sentido matematicamente
```

```
LDA #$30           ; Número 48
CLC                ; 48 + 112 = 160
ADC #$70           ; $30 + $70 = $A0, que é -96, o que não faz sentido matematicamente
```

```
LDA #$10           ; Numero 16
SEC                ; Fazemos 16 -(-128), o que deve resultar em 144
SBC #$80           ; $10 - $80 = $90, que é -112, o que não faz sentido matematicamente
```

## modo de operação 16-bit
Todas as explicações e comportamentos anteriores também se aplicam ao modo 16-bit.