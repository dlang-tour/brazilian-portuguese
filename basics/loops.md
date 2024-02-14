# Loops

D oferece quatro construções de loop.

### 1) `while`

`while` executa o bloco de código fornecido
enquanto uma determinada condição é atendida:

    while (condition)
    {
        foo();
    }

### 2) `do ... while`

`do ... while` executa o bloco de código fornecido
enquanto uma determinada condição é atendida, mas, ao contrário do `while`
o _bloco do loop_ é executado antes que a condição do loop seja
avaliada pela primeira vez.

    do
    {
        foo();
    } while (condition);

### 3) O clássico `for` loop

O clássico `for` loop conhecido em C, C++ ou Java
com _initializer_, _loop condition_ e _loop statement_:

    for (int i = 0; i < arr.length; i++)
    {
        ...

### 4) `foreach`

[`foreach`](basics/foreach), será apresentado em mais detalhes
na próxima seção:

    foreach (el; arr)
    {
        ...
    }

#### Palavra-chave especial e labels

A palavra-chave especial `break` interromperá imediatamente o loop atual.
Em um loop aninhado, um _label_ pode ser usado para sair de qualquer loop externo:

    outer: for (int i = 0; i < 10; ++i)
    {
        for (int j = 0; j < 5; ++j)
        {
            ...
            break outer;

A palavra-chave `continue` inicia a próxima iteração do loop.

### Maiores detalhes

- `for` loop in [_Programming in D_](http://ddili.org/ders/d.en/for.html), [specification](https://dlang.org/spec/statement.html#ForStatement)
- `while` loop in [_Programming in D_](http://ddili.org/ders/d.en/while.html), [specification](https://dlang.org/spec/statement.html#WhileStatement)
- `do-while` loop in [_Programming in D_](http://ddili.org/ders/d.en/do_while.html), [specification](https://dlang.org/spec/statement.html#do-statement)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Computes the average of
the elements of an array.
*/
double average(int[] array)
{
    immutable initialLength = array.length;
    double accumulator = 0.0;
    while (array.length)
    {
        // this could be also done with .front
        // with import std.array : front;
        accumulator += array[0];
        array = array[1 .. $];
    }

    return accumulator / initialLength;
}

void main()
{
    auto testers = [ [5, 15], // 20
          [2, 3, 2, 3], // 10
          [3, 6, 2, 9] ]; // 20

    for (auto i = 0; i < testers.length; ++i)
    {
      writeln("The average of ", testers[i],
        " = ", average(testers[i]));
    }
}
```
