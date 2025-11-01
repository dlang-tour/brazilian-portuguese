# Ranges

Se um `foreach` for encontrado pelo compilador

```
foreach (element; range)
{
    // corpo do Loop...
}
```

ele é reescrito internamente de forma semelhante à seguinte:

```
for (auto __rangeCopy = range;
     !__rangeCopy.empty;
     __rangeCopy.popFront())
 {
    auto element = __rangeCopy.front;
    // Loop body...
}
```

Qualquer objeto que atenda à seguinte interface é chamado de **range**
(ou, mais especificamente, `InputRange`) e, portanto, é um tipo que pode ser iterado:

```
    interface InputRange(E)
    {
        bool empty();
        E front();
        void popFront();
    }
```

Dê uma olhada no exemplo à direita para inspecionar a implementação e o uso
de um intervalo de entrada mais próximo.

## Laziness (preguiçoso)

Os ranges são __lazy__. Eles não serão avaliados até que sejam solicitados.
Portanto, um range de um range infinito pode ser obtido:

```d
42.repeat.take(3).writeln; // [42, 42, 42]
```

## Tipos: Valor vs. Referência

Se o objeto range for um tipo de valor, então o range será copiado e somente a cópia
será utilizada:

```d
auto r = 5.iota;
r.drop(5).writeln; // []
r.writeln; // [0, 1, 2, 3, 4]
```

Se o objeto range for um tipo de referência (por exemplo, `class` ou [`std.range.refRange`](https://dlang.org/phobos/std_range.html#refRange)),
então o range será utilizado e não será redefinido:

```d
auto r = 5.iota;
auto r2 = refRange(&r);
r2.drop(5).writeln; // []
r2.writeln; // []
```

### `InputRanges` copiáveis são `ForwardRanges`

A maioria dos ranges na biblioteca padrão são structs e, portanto, a iteração `foreach`
geralmente não é destrutiva, embora não seja garantida. Se essa
garantia for importante, uma especialização de um `InputRange` pode ser usada
**forward** ranges com um método `.save`:

```
interface ForwardRange(E) : InputRange!E
{
    typeof(this) save();
}
```

```d
// por valor (Structs)
auto r = 5.iota;
auto r2 = refRange(&r);
r2.save.drop(5).writeln; // []
r2.writeln; // [0, 1, 2, 3, 4]
```

### `ForwardRanges` pode ser estendido por ranges bidirecionais + ranges de acesso aleatório

Há duas extensões do `ForwardRange` copiável: (1) um range bidirecional
e (2) um range de acesso aleatório.
Um range bidirecional permite a iteração de trás para frente:

```d
interface BidirectionalRange(E) : ForwardRange!E
{
     E back();
     void popBack();
}
```

```d
5.iota.retro.writeln; // [4, 3, 2, 1, 0]
```

Um range de acesso aleatório tem um `length` (comprimento) conhecido e cada elemento pode ser acessado diretamente.

```d
interface RandomAccessRange(E) : ForwardRange!E
{
     E opIndex(size_t i);
     size_t length();
}
```

Em termos práticos, o range de acesso aleatório mais conhecido é o array de D:

```d
auto r = [4, 5, 6];
r[1].writeln; // 5
```

### Algoritmos lazy range

As funções em [`std.range`](http://dlang.org/phobos/std_range.html) e
[`std.algorithm`](http://dlang.org/phobos/std_algorithm.html) fornecem
blocos de construção que fazem uso dessa interface. Os ranges permitem a
composição de algoritmos complexos por trás de um objeto que
pode ser iterado com facilidade. Além disso, os ranges permitem a criação de objetos **lazy**
que só executam um cálculo quando ele é realmente necessário
em uma iteração, por exemplo, quando o próximo elemento do range é acessado.
Algoritmos especiais do range serão apresentados mais adiante na seção
[D's Gems](gems/range-algorithms).

### Maiores detalhes

- [`std.algorithm`](http://dlang.org/phobos/std_algorithm.html)
- [`std.range`](http://dlang.org/phobos/std_range.html)

## {SourceCode}

```d
import std.stdio : writeln;

struct FibonacciRange
{
    // States of the Fibonacci generator
    int a = 1, b = 1;

    // The fibonacci range never ends
    enum empty = false;

    // Peek at the first element
    int front() const @property
    {
        return a;
    }

    // Remove the first element
    void popFront()
    {
        auto t = a;
        a = b;
        b = t + b;
    }
}

void main()
{
    FibonacciRange fib;

    import std.range : drop, take;
    import std.algorithm.iteration :
        filter, sum;

    // Select the first 10 fibonacci numbers
    auto fib10 = fib.take(10);
    writeln("Fib 10: ", fib10);

    // Except the first five
    auto fib5 = fib10.drop(5);
    writeln("Fib 5: ", fib5);

    // Select the even subset
    auto even = fib5.filter!(x => x % 2 == 0);
    writeln("FibEven : ", even);

    // Sum of all elements
    writeln("Sum of FibEven: ", even.sum);

    // Usually this is summarized as:
    fib.take(10)
         .drop(5)
         .filter!(x => x % 2 == 0)
         .sum
         .writeln;
}
```
