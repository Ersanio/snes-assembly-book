# Modos de Endereçamento

Existem diferentes modos de endereçamento no 65c816. Os modos de endereçamento são usados para fazer com que os opcodes acessem endereços e valores de maneira diferente, como "indexed" ou "direct indirect" (explicado posteriormente neste tutorial). Usando-os com sabedoria, você pode acessar valores e endereços de memória de várias maneiras. Por exemplo, você pode carregar imediatamente um valor em um registrador, como A, ou carregar um byte da ROM em A. Lembre-se de que nem todos os opcodes suportam todos os modos de endereçamento. Aqui estão alguns dos modos de endereçamento importantes que você usará com frequência.

## Immediate 8/16 bit
Este modo de endereçamento define um valor absoluto, que é escrito como #$XX no modo 8-bit ou #$XXXX no modo 16-bit. O # significa "valor imediato", enquanto o $ significa hexadecimal. Usar # sozinho torna a entrada decimal. Por exemplo, #10 é igual a #$0A. Pense em um valor imediato como um número que você está definindo diretamente.

## Direct page 
Este modo de endereçamento define um endereço direct page, que é escrito como $XX.

O direct page são os últimos 2 dígitos hexadecimais de um endereço longo. Por exemplo, endereçar $7E0011 como direct page seria $11. Ao carregar de um endereço direct page, o byte do banco é SEMPRE tratado como $00, sem exceções. Se você escrever “LDA $11” por exemplo, você carregaria o conteúdo de $000011 no acumulador, que também é espelhado em $7E0011 (lembre-se da ilustração da memória SNES). Portanto, você carrega o conteúdo de $7E0011 em A.

## Absolute
Este modo de endereçamento define um endereço absoluto, que é escrito como $XXXX.

Um endereço absoluto são os últimos 4 dígitos hexadecimais de um endereço longo. Usando o exemplo anterior, o endereço $7E0011 como um endereço absoluto seria $0011. O byte do banco do endereço absoluto é determinado pelo registrador data bank.

## Long
Este modo de endereçamento define um endereço longo, que é escrito como $XXXXXX.

Endereços longos oferecem menos complicações ao lidar com bancos e espelhamento. Você também não precisa se preocupar com o que o registrador data bank contém atualmente. Com endereços longos, você pode acessar qualquer endereço na memória SNES.

## Outros modos de endereçamento

O SNES oferece suporte a mais modos de endereçamento. Os modos de endereçamento acima são os básicos. Existem também modos de endereçamento, como: versões indexadas do direct page, endereços absolutos e longos e muito mais. Eles serão explicados no final deste tutorial porque você não precisa deles neste momento. Isso tornaria as coisas ainda mais confusas.