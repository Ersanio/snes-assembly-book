# Carregando e armazenando

A primeira coisa que você definitivamente deve saber ao iniciar em assembly é como carregar e armazenar os dados usando os registradores do SNES. Os opcodes básicos para carregar e armazenar dados são `LDA` e `STA`.

Conforme mencionado anteriormente, há 3 registradores principais:
* o acumulador **A** 
* os indexadores **X** e **Y**

Embora esses registradores possam estar no modo de 8-bit ou 16-bit, neste tutorial iremos considerá-los 8-bit por padrão.

## LDA e STA

| Opcode | Nome completo | Explicação |
| - | - | - |
| **LDA** | Load into accumulator | Carrega um valor em A |
| **STA** | Store from accumulator | Armazena o valor de A em um endereço |

Usaremos endereços da RAM por uma questão de simplicidade. Aqui temos um exemplo de como carregar e armazenar valores.

```
LDA #$03           ; A = $03
STA $7E0001
```

Agora vamos ver o que  esse código está fazendo linha por linha.
```
LDA #$03
`````

Essa instrução carrega o valor $03 em A. O "#" significa que estamos carregando um valor real, e não um endereço. Após a execução desta instrução, o conteúdo do registrador A agora é $03. LDA pode carregar valores em A, no intervalo entre $00 a $FF no modo 8-bit e entre $0000 a $FFFF no modo 16-bit.
```
STA $ 7E0001
```

Essa instrução armazena o valor de A no endereço $7E0001 da RAM. Como o valor de A é $03, agora o valor do armazenado no endereço $7E0001 também passa a ser $03. O conteúdo do registrador A *não* é apagado. Isso significa que você pode armazená-lo em vários endereços diferentes, dessa forma:

```
LDA #$03
STA $7E0001
STA $7E0053
```

Um erro comum de iniciante é escrever STA #$7E0001 ou outra forma qualquer de "STA #$". Esta instrução não existe. Também não faz sentido; não há lógica por trás do armazenamento do valor de A em outro valor.

{% hint style = "info"%}
Lembre-se de que usar $ em vez de #$ após um opcode, significa que o parâmetro é um endereço e não um valor imediato.
{% endhint%}

Colocar um ponto-e-vírgula (;) permitirá que tudo além disso seja ignorado pelo assembler, durante a escrita do código. Em outras palavras, ` ;` é usado para inserir comentários, por exemplo:

```
LDA #$03         ; Isto é um comentário!
```

### Carregando e armazenando endereços

Claro, qual seria a utilidade de armazenar valores em um endereço da RAM quando você não sabe como acessá-lo novamente? Você pode carregar o conteúdo de um endereço da RAM no registrador A usando LDA com um modo de endereçamento diferente. veja o exemplo a seguir:

```
LDA $7E0013
STA $7E0042
```
E novamente, linha por linha.
```
LDA $7E0013
```

Isso carregará o conteúdo do endereço $7E0013 da RAM em A. Suponhamos que o conteúdo seja $33. Portanto, neste momento, A possui o valor $33. O conteúdo de $7E0013 permanece o mesmo, porque o LDA copia o número ao invés de extraí-lo do endereço. Observe que desta vez usamos $ em vez de #$. Isso porque queríamos acessar um endereço da RAM. E finalmente, tanto A quanto  $7E0013 possuem o valor $33.

```
STA $7E0042
```
Esta instrução armazenará o conteúdo do registrador A no endereço $7E0042 da RAM. Claro, A permanecerá inalterado. O endereço $7E0042 agora é $33. Resumindo: no exemplo completo do código será copiado o conteúdo de $7E0013 para $7E0042.

## LDY, STY, LDX e STX

Agora que aprendemos o básico sobre como carregar e armazenar valores em endereços, vamos introduzir os mesmos opcodes de carregamento, mas para os registradores de índice:
| Opcode | Nome completo | Explicação |
| - | - | - |
| **LDY** | Load into Y | Carrega um valor em Y |
| **STY** | Store from Y | Armazena o valor de Y em um endereço |
| **LDX** | Load into X | Carrega um valor em X |
| **STX** | Store from X | Armazena o valor de X em um endereço |

Os opcodes acima se comportam exatamente como LDA e STA. A única diferença é que eles usam os registradores X e Y em vez do acumulador. Por exemplo:
```
LDY #$03
STY $0001
```
Isso irá o número $03 no endereço $7E0001 da RAM, utilizando o registrador Y. Para o registrador X, use LDX e STX. Quanto ao motivo pelo qual o endereço é $0001 em vez de $7E0001, consulte este capítulo:  [Shorter addresses](./shorter-addresses.md)

## STZ
Existe outro opcode que armazena o número $00 diretamente nos endereços.

| Opcode | Nome completo | Explicação |
| - | - | - |
| **STZ** | Store zero to memory | Define o valor de um endereço como 0 |

Este opcode armazena o número $00 em um endereço. Ele nem precisa dos registradores A, X ou Y  previamente carregados com $00.

Se você quiser fazer um código que armazene diretamente $00 em um endereço da RAM, você pode fazê-lo com apenas uma linha de código:

```
STZ $01          ; $7E0001 = $00. O registradorA não é afetado.
```

STZ armazenará zero em um endereço especifico da RAM. Após este opcode, o endereço $7E0001 conterá o número $00. Usar o STZ quando A estiver no modo de 16-bit  **irá** armazenar  $0000 em ambos os endereços $7E0001 e $7E0002 da RAM.