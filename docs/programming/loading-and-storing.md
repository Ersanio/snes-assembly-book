# Carregando e armazenando

A primeira coisa que você precisa saber ao iniciar em assembly é como carregar e armazenar os dados usando os registradores do SNES. Os opcodes básicos para carregar e armazenar dados são `LDA` e `STA`.

Como mencionado anteriormente, há 3 registradores principais:
* O acumulador **A**;
* Os indexadores **X** e **Y**.

Embora esses registradores possam estar no modo de 8-bit ou 16-bit, neste tutorial os consideraremos 8-bit por padrão.

## LDA e STA

| Opcode | Nome completo | Explicação |
| - | - | - |
| **LDA** | Load into accumulator | Carrega um valor em A. |
| **STA** | Store from accumulator | Armazena o valor de A em um endereço. |

Usaremos endereços da RAM para simplificar. Abaixo, temos um exemplo de como carregar e armazenar valores.

```
LDA #$03           ; A = $03
STA $7E0001
```

Agora, veremos o que  esse código está fazendo linha por linha.
```
LDA #$03
`````

Essa instrução carrega o valor $03 em `A`. O `#` significa que estamos carregando um valor de fato e não um endereço. Após a execução da instrução, o conteúdo do registrador `A` torna-se $03. `LDA` pode carregar valores em `A`, no intervalo entre $00 a $FF no modo 8-bit e entre $0000 a $FFFF no modo 16-bit.
```
STA $7E0001
```

A instrução acima armazena o valor de `A` no endereço $7E0001 da RAM. Como o valor de `A` é $03, o valor armazenado no endereço $7E0001 passa a ser $03 também. O conteúdo do registrador `A` **não** é apagado. Isso significa que você pode armazená-lo em vários endereços diferentes, dessa forma:

```
LDA #$03
STA $7E0001
STA $7E0053
```

Um erro comum de iniciante é escrever `STA #$7E0001` ou outra forma qualquer de "`STA #$`". Esta instrução não existe. Também não faz sentido; não há lógica por trás do armazenamento do valor de `A` em outro valor.

> Lembre-se de que usar `$` em vez de `#$` após o opcode, significa que o parâmetro é um endereço e não um valor imediato.

Colocar um ponto-e-vírgula (;) faz com que tudo após este sinal seja ignorado pelo *assembler*, durante a estapa de montagem do código. Em outras palavras: ` ;` é usado para comentários, por exemplo:

```
LDA #$03         ; Isto aqui é um comentário!
```

### Carregando e armazenando endereços

Qual seria a utilidade de armazenar valores em um endereço da RAM se você não pudesse acessá-lo novamente? Você pode carregar o conteúdo de um endereço da RAM no registrador `A` usando `LDA` com um modo de endereçamento diferente. vejamos o exemplo a seguir:

```
LDA $7E0013
STA $7E0042
```
E mais uma vez, linha por linha.
```
LDA $7E0013
```

Esta linha carregará o conteúdo do endereço $7E0013 da RAM em `A`. Suponhamos que o conteúdo seja $33. Então, `A` possuirá o valor $33. O conteúdo de $7E0013 permanecerá o mesmo, pois `LDA` copia o número ao invés de extraí-lo do endereço. Observe que desta vez usamos `$` em vez de `#$`, pois queríamos acessar um endereço da RAM. E por fim, tanto `A` quanto  $7E0013 possuem o valor $33.

```
STA $7E0042
```
Esta instrução armazenará o conteúdo do registrador `A` no endereço $7E0042 da RAM. A permanecerá inalterado. O endereço $7E0042 agora é $33. Em síntese: Este código está copiando o conteúdo de $7E0013 para $7E0042.

## LDY, STY, LDX e STX

Agora que aprendemos o básico sobre como carregar e armazenar valores em endereços, vamos introduzir os mesmos opcodes de carregamento, mas para os registradores de indexação:
| Opcode | Nome completo | Explicação |
| - | - | - |
| **LDY** | Load into Y | Carrega um valor em Y. |
| **STY** | Store from Y | Armazena o valor de Y em um endereço. |
| **LDX** | Load into X | Carrega um valor em X. |
| **STX** | Store from X | Armazena o valor de X em um endereço. |

Os opcodes acima se comportam da mesma forma que `LDA` e `STA`. A diferença é que eles usam os registradores `X` e `Y` em vez do acumulador. Por exemplo:
```
LDY #$03
STY $0001
```
Este código irá armazenar o número $03 no endereço $7E0001 da RAM, utilizando o registrador `Y`. Para o registrador `X`, use `LDX` e `STX`. Quanto ao motivo pelo qual o endereço é $0001 em vez de $7E0001, consulte o capítulo:  [Shorter addresses](./shorter-addresses.md)

## STZ
Existe um opcode que armazena o número $00 nos endereços.

| Opcode | Nome completo | Explicação |
| - | - | - |
| **STZ** | Store zero to memory | Define o valor de um endereço como 0. |

Este opcode armazena o número $00 em um endereço. Ele nem precisa que os registradores `A`, `X` ou `Y` sejam previamente carregados com $00.

Se você precisar de um código que armazene diretamente $00 em um endereço da RAM, você pode fazê-lo com apenas uma linha de código:

```
STZ $01          ; $7E0001 = $00. Nenhum registrador é afetado.
```

`STZ` armazenará zero em um endereço da RAM. Após esta instrução, o endereço $7E0001 conterá o número $00. Usar o `STZ` quando no modo de 16-bit  **irá** armazenar $0000 em ambos os endereços $7E0001 e $7E0002 da RAM.