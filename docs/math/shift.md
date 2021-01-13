# Operações de deslocamento de bits
O 'deslocamento de bits' é uma operação bit a bit em que você move os bits para a esquerda ou direita. Como resultado, praticamente falando, você divide ou multiplica um número por dois.

## ASL e LSR
Existem dois opcodes que deslocam bits para a esquerda ou direita.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**ASL**|A ou mudança de memória para a esquerda|Move os bits para a esquerda uma vez sem carry, praticamente multiplicando um valor por 2|
|**LSR**|A ou mudança de memória para a esquerda|Move os bits para a direita uma vez sem carry, praticamente dividindo um valor por 2|

ASL e LSR podem afetar A ou um endereço, assim como INC e DEC. Aqui está um exemplo de ASL em ação:

```
LDA #$02           ; Carregue o valor $02 em A
ASL A              ; Multipica A por 2
                   ; A agora é $04
```

Isso é o que aparece na superfície. Quando você olha para os números em binário, porém, é assim que se parece:

```
LDA #$02           ; Carregue o valor %00000010 em A
ASL A              ; Mude os bits para a esquerda uma vez
                   ; A agora é %00000100
```
Como você pode ver, o dígito '1' foi movido para a esquerda uma vez. É isso que o ASL faz, movendo os bits para a esquerda.

O ASL também pode mover os bits em um endereço sem afetar A.

```
LDA #$02           ; Carregue o valor $02 em A
STA $00            ; Guarde isto no endereço $7E0000
ASL $00            ; Mude os bits $7E0000 para a esquerda uma vez
                   ; A ainda é $02, enquanto que $7E0000 agora é $04
```

Você também pode deslocar bits para a direita usando LSR.

```
LDA #$02           ; Carregue o valor $ 02 em A
LSR A              ; Divida A por 2
                   ; A agora é $01
```

E quando você olha para ele no nível binário, em vez de hexadecimal:

```
LDA #$02           ; Carregue o valor% 00000010 em A
LSR A              ; Mude os bits para a direita uma vez
                   ; A agora é %00000001
```
Como você pode ver, o dígito '1' foi movido para a direita uma vez. É isso que o LSR faz, movendo os bits para a direita.

O LSR também pode mover bits em um endereço sem afetar A.

```
LDA #$02           ; Carregue o valor $02 em A
STA $00            ; Guarde isso no endereço $7E0000
LSR $00            ; Mude os bits de $7E0000 para a direita uma vez
                   ; A ainda é $02, enquanto que $7E0000 é agora $01
```

### Bordas e carry flag
Você deve estar se perguntando o que acontece se você deslocar o bit `% 1000 0001` para a direita uma vez, usando LSR. O bit 7 está definido atualmente, mas não há nada para mudar para o bit 7. Ao mesmo tempo, o bit 0 também está definido, mas não tem para onde mudar. Quando isso acontecer, o flag carry recebe o bit 0. Ao mesmo tempo, o bit 7 será definido como 0.

O inverso também é válido quando você muda '%1000 0001' para a esquerda uma vez, usando ASL. O flag carry recebe o bit 7, enquanto o bit 0 será definido para 0.

Aqui estão alguns exemplos de movimentação de bits para a carry flag.

```
CLC                ; Garantindo carry (C) = 0
LDA #$80           ; A = %1000 0000 | C = 0
ASL A              ; A = %0000 0000 | C = 1
ASL A              ; A = %0000 0000 | C = 0
```

```
CLC                ; C = 0
LDA #$01           ; A = %0000 0001 | C = 0
LSR A              ; A = %0000 0000 | C = 1
LSR A              ; A = %0000 0000 | C = 0
```

### Encadeando ASL e LSR
Ao encadear ASL e LSR, você pode multiplicar ou dividir um valor por 2 várias vezes, portanto, multiplicando ou dividindo por 2, 4, 8, 16 e assim por diante. É 2ⁿ, onde n é a quantidade de instruções ASL ou LSR que você está encadeando.

Aqui está um exemplo de multiplicação de um valor por 8.

```
LDA #$07           ; A agora é $07 = %0000 0111
ASL A              ; A agora é $0E = %0000 1110
ASL A              ; A agora é $1C = %0001 1100
ASL A              ; A agora é $38 = %0011 1000
                   ; Isso é basicamente $07 * 2³
```

Aqui está um exemplo de divisão de um valor por 4
```
LDA #$07           ; A agora é $07 = %0000 0111
LSR A              ; A agora é $03 = %0000 0011
LSR A              ; A agora é $01 = %0000 0001
                   ; Isso é basicamente $07 / 2²
```

## ROL e ROR
Existem dois opcodes que *rotacionam* bits para a esquerda ou direita, em vez de mudar.

|Opcode|Nome completo|Explicação|
|-|-|-|
|**ROL**|Rotacionar A ou a memória para a esquerda|Move os bits uma vez para a esquerda com carry, envolvendo os bits ao redor|
|**ROR**|Rotacionar A ou a memória para a direita|Move os bits uma vez para a direita com carry, envolvendo os bits ao redor|

Eles se comportam da mesma forma que LSR e ASL, exceto que estão usando o carry flag como um bit extra para fazer com que os bits 'envolvam'. Isso significa que o valor não pode ficar 'preso' em $00 eventualmente, como acontece em ASL e LSR. É por isso que eles são chamados de rotação, em vez de deslocamento.


Aqui está um exemplo de ROL
```
CLC                ; Garantindo carry (C) = 0
LDA #$80           ; A = %1000 0000 | C = 0
ROL A              ; A = %0000 0000 | C = 1
ROL A              ; A = %0000 0001 | C = 0
                   ; A agora é $01
```
Como você pode ver, o bit 7 envolveu até o bit 0, basicamente 'rotacionando' os bits sem perder nenhuma informação.

```
CLC                ; C = 0
LDA #$01           ; A = %0000 0001 | C = 0
ROR A              ; A = %0000 0000 | C = 1
ROR A              ; A = %1000 0000 | C = 0
                   ; A agora é $80
```
Como você pode ver, o bit 0 envolveu todo o caminho até o bit 7, basicamente 'rotacionando' os bits sem perder nenhuma informação.


```
SEC                ; C = 1
LDA #$00           ; A = %0000 0000 | C = 1
ROL A              ; A = %0000 0001 | C = 0
                   ; A agora é $01
```
Como você pode ver, você pode definir o carry flag e rotacionar. Isso resultará em A sendo modificado de qualquer jeito, embora A tenha começado como $00.

A rotação também pode afetar os endereços, assim como ASL e LSR. Aqui está um exemplo:

```
CLC                ; Limpar o carry
LDA #$02           ; Carregar o valor $02 em A
STA $00            ; Guardar isso no endereço $7E0000
ROR $00            ; Mudar os bits de $7E0000 para a direita uma vez
                   ; A ainda é $02, enquanto que $7E0000 agora é $01
                   ; e o carry ainda está limpo
```

## Mudança de bit para o modo de 16 bits

Todas as explicações e comportamentos anteriores também se aplicam ao deslocamento de 16 bits. É que você está trabalhando com 16 bits e a carry flag, não com 8 bits.
