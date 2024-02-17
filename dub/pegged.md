# Pegged

O Pegged é um parser gerador (PEG - parsing expression grammar).

A partir dessa definição de gramática, será criado um conjunto de analisadores relacionados,
para serem usados em tempo de execução ou de compilação.

## Maiores detalhes

- [Pegged on GitHub](https://github.com/PhilippeSigaud/Pegged)
- [Reference article for Pegged's syntax](http://bford.info/pub/lang/peg)

## {SourceCode:fullWidth:incomplete}

```d
/+dub.sdl:
dependency "pegged" version="~>0.4"
+/
import pegged.grammar;
import std.stdio;

mixin(grammar(`
Arithmetic:
    Term     < Factor (Add / Sub)*
    Add      < "+" Factor
    Sub      < "-" Factor
    Factor   < Primary (Mul / Div)*
    Mul      < "*" Primary
    Div      < "/" Primary
    Primary  < Parens / Neg / Pos / Number / Variable
    Parens   < "(" Term ")"
    Neg      < "-" Primary
    Pos      < "+" Primary
    Number   < ~([0-9]+)

    Variable <- identifier
`));

void main()
{
    // Parsing at compile-time:
    enum parseTree1 = Arithmetic("1 + 2 - (3*x-5)*6");

    pragma(msg, parseTree1.matches);
    assert(parseTree1.matches == ["1", "+", "2", "-",
       "(", "3", "*", "x", "-", "5", ")", "*", "6"]);
    writeln(parseTree1);

    // And at runtime too:
    auto parseTree2 = Arithmetic(" 0 + 123 - 456 ");
    assert(parseTree2.matches == ["0", "+", "123", "-", "456"]);
}
```
