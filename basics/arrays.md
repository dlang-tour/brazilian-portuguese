# Arrays

Há dois tipos de arrays em D: **estático** e **dinâmico**.
O acesso aos arrays de qualquer tipo é submetido à verificação de limites [_bounds-checked_]
(exceto quando o compilador consegue provar que as verificações não são necessárias).
Uma falha na verificação de limites gera um `RangeError` que interrompe o aplicativo.
O desenvolvedor pode desativar esse recurso de segurança com o sinalizador do compilador
sinalizador do compilador `-boundscheck=off`
para obter melhorias na velocidade ao custo da segurança.

### Arrays estáticos

Arrays estáticos são armazenadas na pilha se forem definidas dentro de uma função,
ou na memória estática, caso contrário. Eles possuem tamanho fixo,
tamanho esse conhecido em tempo de compilação. O tipo de um array estático inclui
o tamanho fixo:

    int[8] arr;

O tipo de `arr` é `int[8]`. Observe que o tamanho do array é indicado
ao lado do tipo, e não após o nome da variável, como ocorre em C e C++.

### Arrays dinâmicos

As matrizes dinâmicas são armazenadas no heap e podem ser expandidas
ou encolhidos em tempo de execução. Um array dinâmico é criado usando
uma expressão `new` e seu tamanho:

    int size = 8; // variável em tempo de execução (run-time)
    int[] arr = new int[size];

O tipo de `arr` é `int[]`, que também é chamado de **slice**. Os slices
são pontos em um bloco contíguo de memória e serão explicadas
em mais detalhes na [próxima seção](basics/slices).
Os arrays multidimensionais podem ser criados facilmente
usando a sintaxe `auto arr = new int[3][3]`.

### Propriedades e operações do array

Os arrays estáticos e dinâmicos fornecem a propriedade `.length`,
que é somente leitura para arrays estáticos, mas pode ser usada no caso de
matrizes dinâmicas para alterar seu tamanho dinamicamente. A propriedade
`.dup` cria uma cópia do array.

A indexação de um array refere-se a um elemento desse array.
Ao indexar um array por meio da sintaxe `arr[idx]`, um símbolo especial
`$` indica o comprimento de um array. Por exemplo, `arr[$ - 1]` faz referência
o último elemento e é uma forma abreviada de `arr[arr.length - 1]`.

Os arrays podem ser concatenados usando o operador `~`, que
criará um novo array dinâmico.

    int[] a = [1, 2];
    a ~= [3, 4];
    assert(a.length == 4);
    a[0] = 10;
    a.length--;
    assert(a == [10, 2, 3]);

#### Aritmética de vetores

As operações matemáticas podem
ser aplicadas a matrizes inteiras usando uma sintaxe como `c[] = a[] + b[]`, por exemplo.
Isso adiciona todos os elementos de `a` e `b` de modo que
`c[0] = a[0] + b[0]`, `c[1] = a[1] + b[1]`, etc. Também é possível
realizar operações em um array inteiro com um único
valor:

    a[] *= 2; // multiplica todos os elementos por 2
    a[] %= 26; // calcula o módulo 26 por todos os a's

Essas operações podem ser otimizadas
pelo compilador para usar instruções especiais do processador que
realizam as operações de uma só vez.

### Exercício

Complete a função `encrypt` para criptografar a mensagem secreta.
O texto deve ser criptografado usando a criptografia *Caesar*,
que desloca os caracteres no alfabeto usando um determinado índice.
O texto a ser criptografado contém apenas caracteres no intervalo `a-z`,
o que deve facilitar as coisas.

Você pode procurar a solução [aqui](https://github.com/dlang-tour/core/issues/227).

### Maiores detalhes

- [Arrays in _Programming in D_](http://ddili.org/ders/d.en/arrays.html)
- [D Slices](https://dlang.org/d-array-article.html)
- [Array specification](https://dlang.org/spec/arrays.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
Desloque todos os caracteres do
array `input` para os caracteres `shift`.
O intervalo de caracteres é limitado a `a-z`
e o próximo caractere após z é a.

Parâmetros:
    input = array para deslocar
    shift = tamanho do deslocamento
Retonará:
    Array de caracteres deslocados
*/
char[] encrypt(char[] input, char shift)
{
    auto result = input.dup;
    // TODO: desloque cada caractere
    return result;
}

void main()
{
    // Agora, criptografaremos a mensagem com
    // criptografia Caesar e um
    // fator de deslocamento é 16!
    char[] toBeEncrypted = [ 'w','e','l','c',
      'o','m','e','t','o','d',
      // O último , não tem problema
      // e será simplesmente ignorado!
    ];
    writeln("Before: ", toBeEncrypted);
    auto encrypted = encrypt(toBeEncrypted, 16);
    writeln("After: ", encrypted);

    // Verifique se o algoritmo funciona
    // como esperado
    assert(encrypted == [ 'm','u','b','s','e',
            'c','u','j','e','t' ]);
}
```
