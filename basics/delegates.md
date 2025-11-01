# Delegates

### Funções como argumentos

Uma função também pode ser um parâmetro para outra função:

    void doSomething(int function(int, int) doer) {
        // call passed function
        doer(5,5);
    }

    doSomething(&add); // use a função global `add` aqui
                       // add deve ter 2 parâmetros int

`doer` pode então ser chamado como qualquer outra função normal.

### Funções locais com contexto

O exemplo acima usa o tipo `function` que é
um ponteiro para uma função global. Assim que uma função membro
ou uma função local é referenciada, os `delegate`
devem ser usados. Trata-se de um ponteiro de função
que, além disso, contém informações sobre seu
contexto - ou *enclosure*, portanto também chamado de **closure**
em outras linguagens. Por exemplo, um `delegate`
que aponta para uma função membro de uma classe também inclui
o ponteiro para o objeto da classe. Um `delegate` criado por
uma função aninhada inclui um vínculo para o contexto
em vez disso. No entanto, o compilador D pode fazer automaticamente uma cópia do
contexto no heap se isso for necessário para a segurança da memória.
então um delegate será vinculado a essa área de heap.

    void foo() {
        void local() {
            writeln("local");
        }
        auto f = &local; // f é do tipo delegate()
    }

A mesma função `doSomething` que recebe um `delegate`
teria a seguinte aparência:

    void doSomething(int delegate(int,int) doer);

Os objetos de `delegate` e `function` não podem ser misturados. Porém, é possível a função
[`std.functional.toDelegate`](https://dlang.org/phobos/std_functional.html#.toDelegate)
converter `function` em `delegate`.

### Funções anônimas & Lambdas

Como as funções podem ser salvas como variáveis e passadas para outras funções,
é trabalhoso dar a elas seu próprio nome e defini-las. Por isso, o D permite
funções sem nome e _lambdas_ de uma linha.

    auto f = (int lhs, int rhs) {
        return lhs + rhs;
    };
    auto f = (int lhs, int rhs) => lhs + rhs; // Lambda - convertido internamente para o valor acima

Também é possível passar strings como argumentos de template para partes funcionais
da biblioteca padrão do D. Por exemplo, elas oferecem uma maneira conveniente
de definir um *folding* (também conhecido como *reducer*):

    [1, 2, 3].reduce!`a + b`; // 6

As funções de strings só são possíveis para _um ou dois_ argumentos e usam `a`
como o primeiro e `b` como o segundo argumento.

### Maiores detalhes

- [Delegate specification](https://dlang.org/spec/function.html#closures)

## {SourceCode}

```d
import std.stdio : writeln;

enum IntOps {
    add = 0,
    sub = 1,
    mul = 2,
    div = 3
}

/**
Provides a math calculuation
Params:
    op = selected math operation
Returns: delegate which does a math operation
*/
auto getMathOperation(IntOps op)
{
    // Define 4 lambda functions for
    // 4 different mathematical operations
    auto add = (int lhs, int rhs) => lhs + rhs;
    auto sub = (int lhs, int rhs) => lhs - rhs;
    auto mul = (int lhs, int rhs) => lhs * rhs;
    auto div = (int lhs, int rhs) => lhs / rhs;

    // we can ensure that the switch covers
    // all cases
    final switch (op) {
        case IntOps.add:
            return add;
        case IntOps.sub:
            return sub;
        case IntOps.mul:
            return mul;
        case IntOps.div:
            return div;
    }
}

void main()
{
    int a = 10;
    int b = 5;

    auto func = getMathOperation(IntOps.add);
    writeln("The type of func is ",
        typeof(func).stringof, "!");

    // run the delegate func which does all the
    // real work for us!
    writeln("result: ", func(a, b));
}
```
