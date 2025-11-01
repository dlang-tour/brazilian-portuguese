# Memória

D é uma linguagem de programação de sistema e, portanto, permite o gerenciamento manual da memória.
Entretanto, o gerenciamento manual de memória está sujeito a erros de segurança de memória
e por conta disto, o D usa um *coletor de lixo* (GC) por padrão para gerenciar a alocação de memória.

D fornece tipos de ponteiro `T*` como em C:

    int a;       // a declarado na pilha
    int* b = &a; // b contém o endereço de a
    auto c = &a; // c é int* e contém o endereço de a

Um novo bloco de memória no heap é alocado usando uma expressão
[`new T`](https://dlang.org/spec/expression.html#new_expressions),
que retorna um ponteiro para a memória gerenciada (quando T é um tipo de valor):

    int* a = new int;

Quando a memória referenciada por `a` não for utilizada em nenhum lugar
por meio de qualquer variável no programa, o próximo ciclo de coleta de lixo
liberará sua memória. Por padrão, o coletor de lixo só pode ser
executado ao alocar memória com o GC - por exemplo, ao utilizar
a expressão `new`.

* Um ciclo do GC pode ser chamado manualmente com `GC.collect` (a partir de
  [`core.memory`](https://dlang.org/phobos/core_memory.html#.GC))
* As coletas podem ser desativadas com `GC.disable` (e reativadas com `GC.enable`)

### Segurança de memória

As violações de segurança de memória podem:

* Dificultar a reprodução de bugs
* Permitir acesso não autorizado a dados sensíveis
* Corrupção de memória

Para evitar isso, D tem um subconjunto para segurança de memória.

O D tem três níveis de segurança diferentes para as funções: `@system`, `@trusted` e `@safe`.
A menos que especificado de outra forma, o padrão é `@system`, que não verifica a segurança da memória.

O `@safe` impõe um subconjunto de D que impede a ocorrência de bugs de memória por design.
O código `@safe` só pode chamar outras funções `@safe` ou `@trusted`.
Além disso, a aritmética explícita de ponteiros é proibida no código `@safe`:

    void main() @safe {
        int a = 5;
        int* p = &a;
        int* c = p + 5; // erro
    }

As funções `@trusted` são funções verificadas manualmente, que devem ter
uma interface segura para a memória.
Internamente, elas podem executar operações `@system`.
Elas permitem uma ponte entre o SafeD e o acesso de baixo nível subjacente.

### Maiores detalhes

* [Garbage Collection](https://dlang.org/spec/garbage.html)
* [SafeD article](https://dlang.org/safed.html)
* [Memory-safe spec](https://dlang.org/spec/memory-safe-d.html)

## {SourceCode}

```d
import std.stdio : writeln;

void safeFun() @safe
{
    writeln("Hello World");
    // A alocação de memória com
    // o GC também é segura
    int* p = new int;
}

void unsafeFun()
{
    int* p = new int;
    int* fiddling = p + 5;
}

// tente declarar isso como
// @safe para obter um erro
void main()
{
    safeFun();
    unsafeFun();
}
```
