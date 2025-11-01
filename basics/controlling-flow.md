# Fluxo de controle

O fluxo de um aplicativo pode ser controlado condicionalmente com declarações `if` e `else`:

    if (a == 5) {
        writeln("Condição atendida!");
    } else if (a > 10) {
        writeln("Outra condição atendida!");
    } else {
        writeln("Nenhuma condição atendida!");
    }

Quando um bloco `if` ou `else` contém apenas uma instrução,
as chaves podem ser omitidas.

O D fornece os mesmos operadores que o C, C++ e o Java para testar
variáveis quanto à equivalência ou compará-las:

* `==` e `!=` para testar a equivalência ou diferença
* `<`, `<=`, `>` e `>=` para testar se são menores (ou iguais) e maiores (ou iguais)

Para combinar várias condições, o operador `||` representa
o operador lógico *OR*, e `&&` o operador lógico *AND*.

D também define uma instrução `switch`..`case` que executa um caso
dependendo do valor de *uma* variável. `switch`
funciona com todos os tipos básicos e também com strings!
É possível até mesmo definir intervalos para tipos integrais
usando a sintaxe `case START: ... case END:`. Não deixe de
dar uma olhada no exemplo do código-fonte.

### Maiores detalhes

#### Referências básicas

- [Logical expressions in _Programming in D_](http://ddili.org/ders/d.en/logical_expressions.html)
- [If statement in _Programming in D_](http://ddili.org/ders/d.en/if.html)
- [Ternary expressions in _Programming in D_](http://ddili.org/ders/d.en/ternary.html)
- [`switch` and `case` in _Programming in D_](http://ddili.org/ders/d.en/switch_case.html)

#### Referências avançadas

- [Expressions in detail](https://dlang.org/spec/expression.html)
- [If Statement specification](https://dlang.org/spec/statement.html#if-statement)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    if (1 == 1)
        writeln("You can trust math in D");

    int c = 5;
    switch(c) {
        case 0: .. case 9:
            writeln(c, " is within 0-9");
            break; // interrupção necessária!
        case 10:
            writeln("A Ten!");
            break;
        default: // não encontrou nenhum caso,
            writeln("Nothing");
            break;
    }
}
```
