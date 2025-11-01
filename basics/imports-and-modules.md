# Imports e modules

{{#img-right}}turtle.svg{{/img-right}}

Uma das principais decisões de design do D foi ser consistente e evitar casos isolados
na linguagem.
Isto é conhecido como [_turtles all the way down_](https://en.wikipedia.org/wiki/Turtles_all_the_way_down). [tradução livre: _Tartarugas até lá embaixo._]
Um bom exemplo dessa consistência são os `import`s.

## Imports

Para um simples programa Olá Mundo em D, são necessários `import`s.
A instrução `import` torna disponíveis todas as funções públicas
e tipos públicos do **módulo** fornecido.

### As tartarugas começam a cair

Uma instrução `import` não precisa aparecer na parte superior de um arquivo de código-fonte.
Ela também pode ser usada localmente em funções ou em qualquer outro escopo.
Nos próximos capítulos, você verá que isso se aplica a quase todos os conceitos em D. A linguagem não impõe restrições arbitrárias a você.

### Imports seletivos

A biblioteca padrão, chamado [Phobos](https://dlang.org/phobos/),
está localizado no **pacote** `std`
e seus módulos são referenciados por meio do `import std.MODULE`.

A instrução `import` também pode ser usada para importar seletivamente
determinados símbolos de um módulo:

    import std.stdio : writeln, writefln;

As importações seletivas podem ser usadas para melhorar a legibilidade, tornando
óbvia a origem de um símbolo e também como uma forma de
evitar o conflito de símbolos com o mesmo nome de módulos diferentes.

### As importações correspondem a diretórios e arquivos

O sistema de módulos do D, em contraste com outros sistemas, é totalmente baseado em arquivos.
Por exemplo, `import my.cat` sempre se refere a um arquivo `cat.d` na pasta `my/`.
A pasta `my` precisa estar no diretório de trabalho atual ou
em uma das importações de diretório especificadas explicitamente (`-I`).
Por fim, para facilitar a divisão de módulos grandes em vários arquivos menores,
em vez de `cat.d`, uma pasta `cat/` também poderia ser usada.
O compilador D tentaria então carregar `my/cat/package.d` em vez de `my/cat.d`.

A convenção (mas não uma regra rígida) para arquivos `package.d` é importar publicamente
todos os outros módulos na mesma pasta.

### Maiores detalhes

- [Modules and Libraries in _Programming in D_](http://ddili.org/ders/d.en/modules.html)
- [Modules specification](https://dlang.org/spec/module.html)

## {SourceCode}

```d
void main()
{
    import std.stdio;
    // ou import std.stdio : writeln;
    writeln("Olá, Mundo!");
}
```