# Os registradores do SNES

O SNES possui vários “registradores” que são usados para diferentes finalidades. Não podemos nos esquecer deles; são uma das razões pelas quais possibilitam o SNES funcionar corretamente.. Basicamente, registradores são “variáveis globais” que podem ser usados para armazenar valores, ou podem ser usados em operações matemáticas, lógica e todas aquelas coisas sofisticadas! Esses registradores podem ser acessados a qualquer momento.

## Acumulador
O acumulador, também conhecido como **A**, é usado para operações matemáticas em geral, deslocamento de bits, operações bit a bit e carregamento de valores indiretos. A também pode conter variáveis ​​de uso geral para armazenar valores na memória e em outros registradores. Este registrador pode conter um valor de 8 ou 16-bit

O acumulador às vezes é referido como `B` ou `C` em alguns opcodes. B significa o high byte do acumulador, enquanto C significa o acumulador completo de 16-bit.

{% hint style = "aviso"%}
Na verdade, esse registrador pode ser sempre considerado como de 16-bit. Quando A está no modo de 8-bit, você acessa o low byte desse registrador. Quando A está no modo de 16-bit, você acessa o ambos high e low byte desse registrador ao mesmo tempo. O high byte não é apagado quando A entra no modo de 8-bit, mesmo quando novos valores são gravados em A, razão pela qual o high byte pode ser considerado "oculto". Além disso, certas instruções usam high e low bytes do registrador A, independentemente de A estar no modo de 8 ou 16-bit.
{% endhint%}

## Indexadores
Os indexadores são dois registradores, conhecidos como **X** e **Y**. Embora sejam registradores separados, eles têm exatamente as mesmas finalidades e se comportam exatamente da mesma forma. Esses registradores são feitos para indexação, explicada posteriormente neste tutorial. Esses registradores também podem ser de 8 ou 16-bit. X e Y também podem conter variáveis ​​de uso geral para armazenar valores na memória e em outros registradores.

X e Y são “interligados” - e só podem estar no modo de 8 ou 16-bit ao mesmo tempo. Um deles não pode ser de 8-bit e o outro de 16-bit.

{% hint style = "aviso"%}
Quando X e Y saem do modo de 16-bit, seus high bytes são zerados para o valor $00, ao contrário do registrador A, onde o high byte permanece intacto.
{% endhint%}

## Direct page
O registrador de Direct page é um registrador de 16-bit, usado no  modo de endereçamento de Direct page (explicado posteriormente neste tutorial). Quando você acessa um endereço da memória pela notação de Direct Page valor da Direct Page atual é adicionado a esse endereço. Geralmente, você pode ignorar esse registrador se estiver apenas iniciando em assembly.

## Stack Pointer
O stack pointer é um registrador de 16-bit que mantém o ponteiro do stack na RAM (explicado mais tarde neste tutorial), relativo ao endereço de memória $000000. O registrador muda dinamicamente, conforme você adiciona e requisita valores na stack (explicado posteriormente no tutorial).

## Processor Status
O registrador de status do processador contém os sinalizadores do processador atual no formato de 8-bit. Existem 8 sinalizadores de processador e todos ocupam um bit. Alterar esse registrador alteraria muito o comportamento do SNES. Os sinalizadores do processador são explicados posteriormente neste tutorial.

## Data bank
O registrador de data bank  contém um único byte do endereço do data bank atual. Quando você acessa um endereço usando a notação de "endereço absoluto", o SNES usará esse registrador para determinar o banco do endereço.

## Program bank
Este registrador contém o primeiro byte do endereço instrução do bank atual que será executada no momento. Assim, se houver um código executado no endereço $018009, este registrador terá valor $01.

## Program counter
Este registrador contém os high e low bytes do endereço da instrução que será executada no momento. Portanto, se houver uma instrução executada em $018009, este registrador terá o valor $8009.
