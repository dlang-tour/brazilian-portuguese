# Bem-vindo ao D

Bem-vindo ao tour interativo da *linguagem de programação D*.

{{#dmanmobile}}

Esta tour dá um visão geral desta __poderosa__ e __expressiva__
linguagem que compila diretamente para __eficiente__ código __nativo__ de máquina.

{{/dmanmobile}}

### O que é D?

D é o culminar de _décadas de experiência implementando compiladores_
para diversas linguagens e tem um grande número de
[funcionalidades únicas](http://dlang.org/overview.html):

{{#dmandesktop}}

- Construção de _alto nível_ para uma modelagem poderosa
- _alta performance_, linguagem compilada
- tipagem estática
- interface direta para APIs do sistema operacional e hardware
- tempo de compilação incrivelmente rápido
- permite programação com memory-safe (SafeD)
- _maintainable_, _easy to understand_ code
- _fácil de entender_ o código
- baixa curva de aprendizado (sintaxe semelhante a C, Java e outras lingiagens)
- compatible with C application binary interface
- limited compatibility with C++ application binary interface
- multi-paradigm (imperative, structured, object oriented, generic, functional programming purity, and even assembly)
- built-in error prevention (contracts, unittests)

... and many more [features](http://dlang.org/overview.html).

{{/dmandesktop}}

### About the tour

Each section comes with a source code example that can be modified and used
to experiment with D's language features.
Click the run button (or `Ctrl-enter`) to compile and run it.

### Contributing

This tour is [open source](https://github.com/dlang-tour)
and we are glad about pull requests making this tour even better.

## {SourceCode}

```d
import std.stdio;

// Let's get going!
void main()
{
    writeln("Hello World!");
}
```
