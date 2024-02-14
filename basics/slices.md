# Slices

Slices são objetos do tipo `T[]` para qualquer tipo `T`.
Os slices fornecem uma visualização em um subconjunto de uma matriz
de valores `T` ou simplesmente apontam para todo o array.

**Slices e arrays dinâmicos são a mesma coisa.**

Um slice consiste em dois membros - um ponteiro para o elemento inicial e o
tamanho do slice:

    T* ptr;
    size_t length; // unsigned 32 bit em 32bit, unsigned 64 bit em 64bit

### Obtendo um slice por meio de uma nova alocação

Se um novo array dinâmico for criado, será retornado um slice para essa
memória recém-alocada:

    auto arr = new int[5];
    assert(arr.length == 5); // referenciando memória em arr.ptr

A memória real alocada, nesse caso, é totalmente gerenciada pelo coletor de lixo.
O slice retornado funciona como um ponto entre dois elementos subjacentes.

### Obtendo um slice numa memória existente

Usando um operador de slicing, também é possível obter um slice apontando para alguma memória já existente.
O operador de slicing pode ser aplicado a outro slice, arrays estáticos, structs/classes que implementam `opSlice` e algumas outras entidades.

Em uma expressão de exemplo `origin[start .. end]`, o operador de divisão é usado para obter
um slice de todos os elementos de `origin` de `start` até o elemento _before_ `end`:

    auto newArr = arr[1 .. 4]; // O índice 4 NÃO está incluso
    assert(newArr.length == 3);
    newArr[0] = 10; // altera newArr[0] também conhecido como arr[1]

Esses slices geram um novo ponto de extremidade numa memória existente. Eles *não* criam
uma nova cópia. Se nenhum slice tiver mais uma referência a essa memória - ou uma parte *cortada* dela - ela será liberada pelo coletor de lixo.

Usando slices, é possível escrever um código muito eficiente para coisas (como parsers, por exemplo)
que operam somente em um bloco de memória e separam somente as partes que realmente precisam
trabalhar. Dessa forma, não há necessidade de alocar novos blocos de memória.

Como visto na [seção anterior](basics/arrays), a expressão `[$]` é uma forma abreviação de
`arr.length`. Portanto, `arr[$]` indexa o elemento após o final do slice e, portanto, geraria um `RangeError` (se a verificação de limites não tiver sido desativada).

### Maiores detalhes

- [Introduction to Slices in D](http://dlang.org/d-array-article.html)
- [Slices in _Programming in D_](http://ddili.org/ders/d.en/slices.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    int[] test = [ 3, 9, 11, 7, 2, 76, 90, 6 ];
    test.writeln;
    writeln("First element: ", test[0]);
    writeln("Last element: ", test[$ - 1]);
    writeln("Exclude the first two elements: ",
        test[2 .. $]);

    writeln("Slices are views on the memory:");
    auto test2 = test;
    auto subView = test[3 .. $];
    test[] += 1; // increment each element by 1
    test.writeln;
    test2.writeln;
    subView.writeln;

    // Create an empty slice
    assert(test[2 .. 2].length == 0);
}
```
