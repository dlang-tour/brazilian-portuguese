# Tipos básicos

O D fornece vários tipos básicos que sempre têm o mesmo
tamanho **independentemente** da plataforma - a única exceção
é o tipo `real` que fornece a maior precisão possível de ponto flutuante.
Não há diferença entre o tamanho de um número inteiro, independentemente
do aplicativo ser compilado para sistemas de 32 ou 64 bits.

| tipo                          | tamanho
|-------------------------------|------------
|`bool`, `byte`, `ubyte`, `char`| 8-bit
|`short`, `ushort`, `wchar`     | 16-bit
|`int`, `uint`, `dchar`         | 32-bit
|`long`, `ulong`                | 64-bit

#### Tipos ponto flutuante:

| tipo    | tamanho
|---------|--------------------------------------------------
|`float`  | 32-bit
|`double` | 64-bit
|`real`   | >= 64-bit (geralmente 64-bit, mas 80-bit em Intel x86 32-bit)

O prefixo `u` indica tipos *unsigned*. `char` se traduz em caracteres
caracteres UTF-8, `wchar` é usado em strings UTF-16 e `dchar`
em cadeias de caracteres UTF-32.

Uma conversão entre variáveis de tipos diferentes só é
permitida pelo compilador se não houver perda de precisão. No entanto,
uma conversão entre tipos de ponto flutuante
(por exemplo, `double` para `float`) é permitida.

Uma conversão para outro tipo pode ser forçada com o uso da expressão
`cast(TYPE) myVar`. No entanto, ela precisa ser usada com muito cuidado,
pois a expressão `cast` pode quebrar o sistema de tipos.

A palavra-chave especial `auto` cria uma variável e infere
seu tipo a partir do lado direito da expressão. `auto myVar = 7`
deduzirá o tipo `int` para `myVar`. Observe que o tipo ainda é
definido em tempo de compilação e não pode ser alterado, como em qualquer outra
variável com um tipo explicitamente fornecido.

### Propriedades de tipos

Todos os tipos de dados têm uma propriedade `.init` para a qual são inicializados.
Para todos os números inteiros, essa propriedade é `0` e, para pontos flutuantes, é `nan` (*não é um número*).

Os tipos de ponto flutuante e integral têm uma propriedade `.max` para o valor mais alto que podem representar.
Os tipos integrais também têm uma propriedade `.min` para o valor mais baixo
que podem representar, enquanto os tipos de ponto flutuante têm uma propriedade `.min_normal`
que é definida como o menor valor normalizado representável que não é 0.

Os tipos de ponto flutuante também têm as propriedades `.nan` (valor NaN), `.infinity`
(valor infinito), `.dig` (número de dígitos decimais das precisões), `.mant_dig`
(número de bits na mantissa) e outras.

Cada tipo também tem uma propriedade `.stringof` que produz seu nome como uma string.

### Índices em D

Em D, os índices geralmente têm o alias do tipo `size_t`, pois é um tipo que
é grande o suficiente para representar um deslocamento em toda a memória endereçável, 
isto é, `uint` para arquiteturas de 32 bits e `ulong` para arquiteturas de 64 bits.

### Expressão Assert

O `assert` é uma expressão que verifica as condições no modo de depuração e aborta com um `AssertionError` se falhar.
O `assert(0)` é, portanto, usado para marcar código inacessível.

### Maiores Detalhes

#### Referências Básicas

- [Assignment](http://ddili.org/ders/d.en/assignment.html)
- [Variables](http://ddili.org/ders/d.en/variables.html)
- [Arithmetics](http://ddili.org/ders/d.en/arithmetic.html)
- [Floating Point](http://ddili.org/ders/d.en/floating_point.html)
- [Fundamental types in _Programming in D_](http://ddili.org/ders/d.en/types.html)

#### Referencias Avançadas

- [Overview of all basic data types in D](https://dlang.org/spec/type.html)
- [`auto` and `typeof` in _Programming in D_](http://ddili.org/ders/d.en/auto_and_typeof.html)
- [Type properties](https://dlang.org/spec/property.html)
- [Assert expression](https://dlang.org/spec/expression.html#AssertExpression)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    // Números grandes podem ser separados
    // utilizando sublinha "_"
    // melhorando a legibilidade.
    int b = 7_000_000;
    // necessário cast
    // para conversão explícita
    short c = cast(short) b;
    uint d = b; // conversão implícita
    int g;
    assert(g == 0);

    // f denota-se ser do tipo float
    auto f = 3.1415f;

    // typeid(VAR) retorna a informação
    // sobre o tipo da expressão.
    writeln("type of f is ", typeid(f));
    double pi = f; // conversão implícita
    // para tipos de ponto flutuante
    // permitindo a redução implícita
    float demoted = pi;

    // acessando as propriedades do tipo
    assert(int.init == 0);
    assert(int.sizeof == 4);
    assert(bool.max == 1);
    writeln(int.min, " ", int.max);
    writeln(int.stringof); // int
}
```