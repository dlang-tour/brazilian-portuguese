# Arrays Associativos

O D tem *arrays associativos* incorporados, também conhecidos como [HashMap](https://en.wikipedia.org/wiki/Hash_table).
Um array associativo com um tipo `string` na chave e um tipo `int` no valor
é declarado da seguinte forma:

    int[string] arr;

O valor pode ser acessado por sua chave e, assim, ser definido:

    arr["key1"] = 10;

Para testar se uma chave está localizada no array associativo, a expressão
expressão `in` pode ser usada:

    if ("key1" in arr)
        writeln("Yes");

A expressão `in` retorna um ponteiro para o valor se ele
puder ser encontrado ou um ponteiro `null` caso contrário. Assim, a verificação de existência
e gravações podem ser convenientemente combinadas:

    if (auto val = "key1" in arr)
        *val = 20;

O acesso a uma chave que não existe gera um `RangeError`
que interrompe imediatamente o aplicativo. Para acesso seguro
com um valor padrão, `get(key, defaultValue)` pode ser usado.

Os AAs têm a propriedade `.length` como os arrays e fornecem
um membro `.remove(key)` para remover entradas por sua chave.
Deixamos como exercício para o leitor explorar
os intervalos especiais `.byKey` e `.byValue`.

### Maiores detalhes

- [Associative arrays in _Programming in D_](http://ddili.org/ders/d.en/aa.html)
- [Associative arrays specification](https://dlang.org/spec/hash-map.html)
- [std.array.byPair](http://dlang.org/phobos/std_array.html#.byPair)

## {SourceCode}

```d
import std.array : assocArray;
import std.algorithm.iteration: each, group,
    splitter, sum;
import std.string: toLower;
import std.stdio : writefln, writeln;

void main()
{
    string text = "Rock D with D";

    // Iterar sobre todas as palavras e contar
    // cada palavra uma vez
    int[string] words;
    text.toLower()
        .splitter(" ")
        .each!(w => words[w]++);

    foreach (key, value; words)
        writefln("key: %s, value: %d",
                       key, value);

    // `.keys` e `.values` retornam arrays
    writeln("Words: ", words.keys);

    // `.byKey`, `.byValue` e `.byKeyValue`
    // return lazy, iteratable ranges
    writeln("# Words: ", words.byValue.sum);

    // Um novo array associativo pode ser criado
    // com `assocArray` passando um
    // intervalo de tuplas de chave/valor;
    auto array = ['a', 'a', 'a', 'b', 'b',
                  'c', 'd', 'e', 'e'];

    // `.group` agrupa elementos 
    // consecutivamente equivalentes
    // em uma única tupla do elemento
    // elemento e o número de suas repetições
    auto keyValue = array.group;
    writeln("Key/Value range: ", keyValue);
    writeln("Associative array: ",
             keyValue.assocArray);
}
```
