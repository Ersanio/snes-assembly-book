# Encurtando endereços
É possível encurtar endereços, mas há pré-requisitos.

Para encurtar um endereço longo da RAM em um endereço absoluto (4 dígitos), o endereço deve estar entre $ 7E0000 - $7E1FFF. $7E1234 pode ser reduzido para $1234, por exemplo. Se você encurtar o endereço $7E2000 ou superior em um endereço de 4 dígitos, você escreverá em outras áreas além da RAM. Isso tem a ver com o registrador data bank e com o mapa de memória do SNES.

Se você quiser encurtar os endereços longos de RAM para um endereço direct page (2 dígitos), os highe low bytes do endereço longo nunca devem exceder o valor $ 00FF. O endereço que você deseja armazenar deve ser no banco $00 ou $7E. Portanto, você pode encurtar `LDA $7E0001` para` LDA $01` e `STA $000001` para` STA $01`.

Freqüentemente, é necessário escrever endereços mais curtos como parâmetros, pois certos opcodes não suportam certos modos de endereçamento. Por exemplo, o opcode STZ não suporta endereços longos, então você não pode escrever `STZ $7E1234`. Você terá que escrever `STZ $1234` em vez disso.

Lembre-se de que quando você usa 2 dígitos para carregar e armazenar, o banco é sempre $00 por padrão, independentemente do registrador data bank! Você pode usar endereços de 2 dígitos para endereços de RAM $7E0000 - $7E00FF, porque a RAM $7E0000 - $7E1FFF é espelhado nos bancos $00 - $3F.

