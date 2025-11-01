# Mutabilidade

D é uma linguagem estaticamente tipada: depois que uma variável é declarada,
seu tipo não pode ser alterado daquele ponto em diante. Isso permite que o
que o compilador evite bugs desde o início e imponha restrições
em tempo de compilação. Uma boa segurança de tipo oferece o suporte necessário
para tornar os programas grandes mais seguros e mais fáceis de manter.

Há vários qualificadores de tipo em D, mas os mais comumente usados são
`const` e `immutable`.

### `immutable`

Além de um sistema de tipos estáticos, o D fornece qualificadores de tipos (às vezes também
chamados de "construtores de tipos") que impõem restrições adicionais a determinados
objetos. Por exemplo, um objeto `immutable` só pode ser inicializado uma vez e
depois disso, não pode ser alterado.

    immutable int err = 5;
    // ou: immutable err = 5 e int será inferido.
    err = 5; // não compila

Os objetos `imutáveis` podem, portanto, ser compartilhados com segurança entre diferentes threads sem sincronização, pois, por definição, eles nunca mudam. Isso também implica que os objetos `immutable` podem ser armazenados em cache perfeitamente.

### `const`

Os objetos não podem ser modificados por meio de uma referência const.
Uma referência mutável para o mesmo objeto ainda pode modificá-lo.
Um ponteiro `const` pode ser criado a partir de um objeto *mutável* ou
objeto `immutable`. É comum que as APIs aceitem argumentos `const`
para garantir que não modifiquem a entrada, pois isso permite que a mesma
função processe dados mutáveis e imutáveis.

    void foo(const char[] s)
    {
        // se não for comentada, a próxima linha
        // resultará em erro (não é possível modificar const):
        // s[0] = 'x';

        import std.stdio : writeln;
        writeln(s);
    }

    // graças ao `const`, ambas as chamadas serão compiladas:
    foo("abcd"); // string é um array imutável
    foo("abcd".dup); // .dup returna uma cópia mutável

## Transitividade

Tanto `immutable` quanto `const` são qualificadores de tipo _transitivos_, garantindo assim que, uma vez que `const` seja aplicado a um tipo, ele se aplica recursivamente a cada subcomponente desse tipo.

    immutable int* p = new int;
    p = null; // erro
    *p = 5; // erro

O tipo de elemento de um ponteiro (ou array) pode ser qualificado separadamente:

    immutable p = new int; // immutable(int*)
    immutable(int)* q = p; // OK, ponteiro mutável
    q = null; // OK
    *q = 5; // erro

O tipo [`string`] (basics/alias-strings) é definido como `immutable(char)[]`:

    string s = "hi";
    s ~= " world"; // OK, acrescenta novos caracteres
    s[0] = 'c'; // erro, não é possível alterar dados existentes

### Maiores detalhes

#### Referências básicas

- [Immutable in _Programming in D_](http://ddili.org/ders/d.en/const_and_immutable.html)
- [Scopes in _Programming in D_](http://ddili.org/ders/d.en/name_space.html)

#### Referências avançadas

- [const(FAQ)](https://dlang.org/const-faq.html)
- [Type qualifiers in D](https://dlang.org/spec/const3.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    /**
    * Variables are mutable by default and
    * editing them is allowed:
    */
    int m = 100; // mutable
    writeln("m: ", typeof(m).stringof);
    m = 10; // fine

    /**
    * Pointing to mutable memory:
    */
    // A const pointer to mutable memory is
    // allowed
    const int* cm = &m;
    writeln("cm: ", typeof(cm).stringof);
    // By defintion `const` can't be modified:
    // *cm = 100; // error!

    // As `immutable` is guaranteed to stay
    // unchanged, it can't point to
    // mutable memory
    // immutable int* im = &m; // error!

    /**
    * Pointing to read-only memory:
    */
    immutable v = 100;
    writeln("v: ", typeof(v).stringof);
    // v = 5; // error!

    // `const` may point to read-only memory,
    // but it is read-only as well
    const int* cv = &v;
    writeln("cv: ", typeof(cv).stringof);
    // *cv = 10; // error!
}
```
