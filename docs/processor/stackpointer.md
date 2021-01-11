# O stack pointer
O stack pointer é um registrador de 16 bits que o processador usa para determinar a localização atual da pilha na RAM do SNES. Após cada push, o stack pointer *decrementa*, de modo que os bytes são "empurrados" para trás. Eles substituem o conteúdo do endereço da RAM. Por outro lado, após cada pull, o stack pointer *incrementa*, mas o valor retirado ainda permanece na pilha. Portanto, pull é na verdade uma leitura. O stack pointer aumenta ou diminui em 1 quando se trata de valores 8-bit e em 2 quando  se para valores 16-bit. Isso pode ser resumido da seguinte forma:

|Operação|Onde|Atualização do stack pointer|
|-|-|-|
|Push (8-bit)|Local do stack pointer|-1|
|Pull (8-bit)|Local do stack pointer +1|+1|
|Push (16-bit)|Local do stack pointer|-2|
|Pull (16-bit)|Local do stack pointer +1|+2|

O registrador stack pointer assume que vai trabalhar com o banco $00, independentemente do valor atual do registrador bank register. Isso significa que, do ponto de vista do mapeamento de memória do SNES, se o stack pointer apontar para o endereço absoluto $8000 ou acima, ele começará a apontar para a ROM. Se apontar para o endereço absoluto $2000 ou acima, ele começará a apontar para os registradores de hardware do SNES. Portanto, a única área de pilha utilizável é de $000000 a $001FFF.

A pilha não tem tamanho definido. Em contra partida, você como programador, apenas reserva uma área de RAM para a pilha que acha que é necessário. A razão pela qual não tem um tamanho definido é porque enquanto você continua empurrando, o ponteiro da pilha continua diminuindo sem nenhum limite definido. Se você empurrar muitos valores, poderá sobrescrever acidentalmente outros endereços de RAM que têm outras finalidades predeterminadas.

## Push
Aqui temos um exemplo de como a pilha funciona do ponto de vista da RAM, quando você usa push com valor de 8 bits:
```
         ; Pilha: .. 55 55 55 55 55 55 ..
LDA #$42                             └─ O stack pointer aponta para este valor.
PHA
         ; Pilha: .. 55 55 55 55 55 42 ..
LDA #$AA                          └─ O stack pointer aponta para este valor.
PHA
         ; Pilha: .. 55 55 55 55 AA 42 ..
                               └─ O stack pointer aponta para este valor.
```

E aqui temos um exemplo de push de 16-bit:
```
REP #$20 ; registrador A no modo 16-bit
         ; Pilha: .. 55 55 55 55 55 55 ..
LDA #$4210                           └─ O stack pointer aponta para este valor.
PHA
         ; Pilha: .. 55 55 55 55 10 42 ..
LDA #$AA99                     └─ O stack pointer aponta para este valor.
PHA
         ; Pilha: .. 55 55 99 AA 10 42 ..
                         └─ O stack pointer aponta para este valor.
```

## Pull
Quando você "retira" algo da pilha, ele é lido do local do stack pointer +1. Após cada extração, o stack pointer aumenta dependendo do tamanho do valor retirado. Você não extrai literalmente o valor da RAM. Você apenas copia o valor do local da pilha para o registrador de destino. O valor na pilha continua inalterado.

Aqui temos um exemplo de pull de um valor 8-bit:
```
        ; Pilha: .. 55 55 12 34 56 78 ..
                           └─ O stack pointer aponta para este valor.
PLA     ; Agora A é $34
        ; Pilha: .. 55 55 12 34 56 78 ..
                              └─ O stack pointer aponta para este valor.
PLA
        ; Agora A é $56
        ; Pilha: .. 55 55 12 34 56 78 ..
                                 └─ O stack pointer aponta para este valor.
```

E temos um exemplo de pull de um valor 16-bit:
```
REP #$20 ; registrador A no modo 16-bit
         ; Pilha: .. 55 55 12 34 56 78 ..
                         └─ O stack pointer aponta para este valor.
PLA      ; Agora A é $3412
         ; Pilha: .. 55 55 12 34 56 78 ..
                               └─ O stack pointer aponta para este valor.
PLA
         ; Agora A é $7856
         ; Pilha: .. 55 55 12 34 56 78 ..
                                     └─ O stack pointer aponta para este valor.
```