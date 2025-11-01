# Foreach

{{#img-right}}dman-teacher-foreach.jpg{{/img-right}}

D apresenta um loop `foreach` que permite
iteração menos propensa a erros e mais legível.

### Iteração dos elementos

Dado um array `arr` do tipo `int[]`, é possível
iterar sobre os elementos usando um loop `foreach`:

    foreach (int e; arr) {
        writeln(e);
    }

O primeiro parâmetro em um loop `foreach` é o nome da variável
usada para cada iteração. Seu tipo pode ser inferido automaticamente da seguinte forma:

    foreach (e; arr) {
        // typeof(e) é int
        writeln(e);
    }

O segundo campo deve ser um array - ou um objeto iterável especial
chamado **range**, que será apresentado na [próxima seção](basics/ranges).

### Acessando por referência

Os elementos serão copiados do array ou range durante a iteração.
Isso é aceitável para tipos básicos, mas pode ser um problema para tipos
grandes. Para evitar a cópia e permitir a mutabilidade do dado
utilizando `ref`:

    foreach (ref e; arr) {
        e = 10; // sobrescrevendo o valor
    }

### Iterar `n` vezes

D nos permite escrever iterações que devem ser executadas
`n` vezes, de forma mais concisa com a sintaxe `..`:

    foreach (i; 0 .. 3) {
        writeln(i);
    }
    // 0 1 2

O último número em `a ... b` é excluído do range,
portanto, o corpo do loop é executado `3` vezes.

### Iteração com contador de índice

Para arrays, também é possível acessar uma variável de índice separada.

    foreach (i, e; [4, 5, 6]) {
        writeln(i, ":", e);
    }
    // 0:4 1:5 2:6

### Iteração reversa com `foreach_reverse`

Um array pode ser iterada na ordem inversa com
`foreach_reverse`:

    foreach_reverse (e; [1, 2, 3]) {
        writeln(e);
    }
    // 3 2 1

### Maiores detalhes

- [`foreach` in _Programming in D_](http://ddili.org/ders/d.en/foreach.html)
- [`foreach` with Structs and Classes  _Programming in D_](http://ddili.org/ders/d.en/foreach_opapply.html)
- [`foreach` specification](https://dlang.org/spec/statement.html#ForeachStatement)

## {SourceCode}

```d
import std.stdio : writefln;

void main() {
    auto arr = [
        [5, 15],      // 20
        [2, 3, 2, 3], // 10
        [3, 6, 2, 9], // 20
    ];

    foreach (i, row; arr)
    {
        double total = 0.0;
        foreach (e; row)
            total += e;

        auto avg = total / row.length;
        writefln("AVG [row=%d]: %.2f", i, avg);
    }
}
```
