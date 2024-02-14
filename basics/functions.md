# Funções

Uma função já foi introduzida: `main()` - o ponto de partida de todo programa
programa D. Uma função pode retornar um valor (ou ser declarada com `void`
se nada for retornado) e aceitar um número arbitrário de argumentos:

    int add(int lhs, int rhs) {
        return lhs + rhs;
    }

### Retornar tipo `auto`

Se o tipo de retorno for definido como `auto`, o compilador D infere o tipo de retorno
automaticamente. Portanto, várias instruções `return` devem retornar valores com
tipos compatíveis.

    auto add(int lhs, int rhs) { // retorna `int`
        return lhs + rhs;
    }

    auto lessOrEqual(int lhs, int rhs) { // retorna `double`
        if (lhs <= rhs)
            return 0;
        else
            return 1.0;
    }

### Argumentos pré-atribuidos

As funções podem, opcionalmente, definir argumentos padrão.
Isso evita o trabalho tedioso de declarar sobrecargas redundantes.

    void plot(string msg, string color = "red") {
        ...
    }
    plot("D rocks");
    plot("D rocks", "blue");

Uma vez que um argumento padrão tenha sido especificado, todos os argumentos seguintes
também devem ser argumentos padrão.

### Funções locais

As funções podem até ser declaradas dentro de outras funções, onde podem ser
usadas localmente e não são visíveis para o mundo externo.
Essas funções podem até mesmo ter acesso a objetos que são locais para
escopo da função principal:

    void fun() {
        int local = 10;
        int fun_secret() {
            local++; // that's legal
        }
        ...

Essas funções aninhadas são chamadas de delegates e serão explicadas com mais detalhes
[em breve](basics/delegates).

### Maiores detalhes

- [Functions in _Programming in D_](http://ddili.org/ders/d.en/functions.html)
- [Function parameters in _Programming in D_](http://ddili.org/ders/d.en/function_parameters.html)
- [Function specification](https://dlang.org/spec/function.html)

## {SourceCode}

```d
import std.stdio : writeln;
import std.random : uniform;

void randomCalculator()
{
    // Define 4 local functions for
    // 4 different mathematical operations
    auto add(int lhs, int rhs) {
        return lhs + rhs;
    }
    auto sub(int lhs, int rhs) {
        return lhs - rhs;
    }
    auto mul(int lhs, int rhs) {
        return lhs * rhs;
    }
    auto div(int lhs, int rhs) {
        return lhs / rhs;
    }

    int a = 10;
    int b = 5;

    // uniform generates a number between START
    // and END, whereas END is NOT inclusive.
    // Depending on the result we call one of
    // the math operations.
    switch (uniform(0, 4)) {
        case 0:
            writeln(add(a, b));
            break;
        case 1:
            writeln(sub(a, b));
            break;
        case 2:
            writeln(mul(a, b));
            break;
        case 3:
            writeln(div(a, b));
            break;
        default:
            // special code which marks
            // UNREACHABLE code
            assert(0);
    }
}

void main()
{
    randomCalculator();
    // add(), sub(), mul() and div()
    // are NOT visible outside of their scope
    static assert(!__traits(compiles,
                            add(1, 2)));
}

```
