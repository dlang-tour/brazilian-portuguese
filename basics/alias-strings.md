# Alias & Strings

Agora que sabemos o que são arrays, entramos em contato com `immutable`,
e demos uma olhada rápida nos tipos básicos, é hora de introduzir duas
novas construções em uma única linha:

    alias string = immutable(char)[];

O termo `string` é definido por uma instrução `alias` que o define
como uma slice de `immutable(char)`s. Isso significa que, uma vez que uma `string`
tenha sido construída seu conteúdo nunca mais será alterado. E, na verdade,
esta é a segunda introdução: bem-vindo ao `string` UTF-8!

Devido à sua imutabilidade, as `string`s podem ser compartilhadas perfeitamente entre
diferentes threads. Como a `string` é uma slice, partes podem ser retiradas dela sem
alocar memória. A função padrão
[`std.algorithm.splitter`](https://dlang.org/phobos/std_algorithm_iteration.html#.splitter)
por exemplo, divide uma string por nova linha sem nenhuma alocação de memória.

Além da `string` UTF-8, há mais dois tipos:

    alias wstring = immutable(wchar)[]; // UTF-16
    alias dstring = immutable(dchar)[]; // UTF-32

As variantes são mais facilmente convertidas entre si usando
o método `to` do `std.conv`:

    dstring myDstring = to!dstring(myString);
    string myString   = to!string(myDstring);

### Unicode strings

Isso significa que uma `string` simples é definida como um array Unicode de 8 bits.
Todas as operações do array podem ser usadas em strings, mas elas funcionarão em
um nível de [unidade de código](http://unicode.org/glossary/#code_unit),
e não em um nível de caractere.
Ao mesmo tempo, os algoritmos da biblioteca padrão interpretarão `string`s como sequências
de [pontos de código](http://unicode.org/glossary/#code_point), e há também uma
opção para tratá-las como sequências de
[graphemes](http://unicode.org/glossary/#grapheme) por meio do uso explícito de
[`std.uni.byGrapheme`](https://dlang.org/library/std/uni/by_grapheme.html).

Este pequeno exemplo ilustra essa diferença de interpretação:

    string s = "\u0041\u0308"; // Ä

    writeln(s.length); // 3

    import std.range : walkLength;
    writeln(s.walkLength); // 2

    import std.uni : byGrapheme;
    writeln(s.byGrapheme.walkLength); // 1

Aqui, o tamanho atual do array `s` é 3, porque ele contém 3 unidades de código:
`0x41`, `0x03` e `0x08`. Esses dois últimos definem um único ponto de código
(combinando caracteres diacríticos) e
[`walkLength`](https://dlang.org/library/std/range/primitives/walk_length.html)
(função da biblioteca padrão para calcular o comprimento de um range arbitrário)
conta dois pontos de código no total. Por fim, `byGrapheme` realiza cálculos bastante caros
para reconhecer que esses dois pontos de código se combinam em um único caractere exibido.

O processamento correto do Unicode pode ser muito complicado, mas, na maioria das vezes, os desenvolvedores de D
podem simplesmente considerar as variáveis `string` como arrays mágicos de bytes e
e confiar nos algoritmos da biblioteca padrão para fazer o trabalho correto.
Se a iteração por elemento (unidade de código) for desejada, é possível usar
[`byCodeUnit`](http://dlang.org/phobos/std_utf.html#.byCodeUnit).

A decodificação automática em D é explicada em mais detalhes
no capítulo [Unicode gems](gems/unicode).

### Strings de múltiplas linhas

As strings em D sempre podem se estender por várias linhas:

    string multiline = "
    This
    may be a
    long document
    ";

Quando as aspas aparecem no documento, as strings Wysiwyg (veja abaixo) ou
[strings delimitadas](http://dlang.org/spec/lex.html#delimited_strings) podem ser usadas.

### Strings Wysiwyg

Também é possível usar strings brutas para minimizar o trabalho de escapar
de símbolos reservados. As cadeias de caracteres brutas podem ser declaradas com
o uso de backticks (`` `... ` ``) ou o prefixo r(aw) (`r" ... "`).

    string raw  =  `raw "string"`; // raw "string"
    string raw2 = r"raw `string`"; // raw `string`

O D oferece ainda mais maneiras de representar strings - não hesite em
em [explorar](https://dlang.org/spec/lex.html#string_literals) essas formas.

### Maiores detalhes

- [Unicode gem](gems/unicode)
- [Characters in _Programming in D_](http://ddili.org/ders/d.en/characters.html)
- [Strings in _Programming in D_](http://ddili.org/ders/d.en/strings.html)
- [std.utf](http://dlang.org/phobos/std_utf.html) - UTF en-/decoding algorithms
- [std.uni](http://dlang.org/phobos/std_uni.html) - Unicode algorithms
- [String Literals in the D spec](http://dlang.org/spec/lex.html#string_literals)

## {SourceCode}

```d
import std.stdio : writeln, writefln;
import std.range : walkLength;
import std.uni : byGrapheme;
import std.string : format;

void main() {
    // format generates a string using a printf
    // like syntax. D allows native UTF string
    // handling!
    string str = format("%s %s", "Hellö",
        "Wörld");
    writeln("My string: ", str);
    writeln("Array length (code unit count)"
        ~ " of string: ", str.length);
    writeln("Range length (code point count)"
        ~ " of string: ", str.walkLength);
    writeln("Character length (grapheme count)"
        ~ " of string: ",
        str.byGrapheme.walkLength);

    // Strings are just normal arrays, so any
    // operation that works on arrays works here
    // too!
    import std.array : replace;
    writeln(replace(str, "lö", "lo"));
    import std.algorithm : endsWith;
    writefln("Does %s end with 'rld'? %s",
        str, endsWith(str, "rld"));

    import std.conv : to;
    // Convert to UTF-32
    dstring dstr = to!dstring(str);
    // .. which of course looks the same!
    writeln("My dstring: ", dstr);
}
```
