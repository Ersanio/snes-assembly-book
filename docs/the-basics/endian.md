# Little-endian

Dentro da memória do SNES, os valores de 16-bit e 24-bit são sempre armazenados em "little-endian". Tome por exemplo o valor $1234 que armazenamos na RAM; $1234 não aparece como $12 $34. Ao invés disso, aparece como $34 $12. É assim que funciona o SNES. Quando esse número é lido no modo de 16 bits, ele mostra $1234, NÃO $3412. O SNES reverte isso automaticamente.

Os valores de 24-bit não são exceção. Valores, como $123456, são armazenados na memória como $56 $34 $12.

Você pode escrever tudo em ASM normal sem se preocupar com o endianness, pois tudo é tratado automaticamente pelo SNES e pelo assembler! Você deve se preocupar com endianness ao lidar com valores de 16-bit no modo de 8-bit.

Por exemplo: se você armazenar o valor $1234 no endereço $7E0000, ele será armazenado como $34 $12. Então, se quiser acessar o byte inferior de $1234 (que é $34), você precisará ler $7E0000, NÃO $7E0001.

O conceito de little-endian é especialmente importante ao lidar com "ponteiros", que é explicado mais tarde neste tutorial.