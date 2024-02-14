# Templates

**D** permite definir funções com template semelhantes a C++ e Java
que é um meio de definir funções ou objetos **genéricos** que funcionam
para qualquer tipo que seja compilado com as instruções no corpo da função:

    auto add(T)(T lhs, T rhs) {
        return lhs + rhs;
    }

O parâmetro com template `T` é definido em um conjunto de parênteses
na frente dos parâmetros reais da função. `T` é um espaço reservado
que é substituído pelo compilador ao *instanciar* a função usando o operador `!`:

    add!int(5, 10);
    add!float(5.0f, 10.0f);
    add!Animal(dog, cat); // não será compilado; o tipo Animal não implementou `+`

### Parâmetros com template implícitos

As funções de tipos genéricos utilizando template, têm dois conjuntos de parâmetros:
o primeiro é para argumentos de argumentos em tempo de compilação e o segundo para 
argumentos em tempo de execução.
(As funções sem template podem aceitar apenas argumentos em tempo de execução).
Se um ou mais argumentos em tempo de compilação não forem especificados quando a função for chamada,
o compilador tentará deduzi-los da lista de argumentos de tempo de execução como os tipos desses argumentos.

    int a = 5; int b = 10;
    add(a, b); // T é deduzido como `int`
    float c = 5.0;
    add(a, c); // T é deduzido como `float`

### Propriedades do template

Diferentemente dos genéricos em Java, os templates em D funcionam apenas em tempo de compilação e produzem código altamente otimizado, adaptado ao conjunto específico de tipos
usados ao chamar a função de fato

Obviamente, os tipos `struct`, `class` e `interface` também podem ser definidos como tipos genéricos usando templates também.

    struct S(T) {
        // ...
    }

### Maiores detalhes

- [Tutorial to D Templates](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Templates in _Programming in D_](http://ddili.org/ders/d.en/templates.html)

#### Referências avançadas

- [D Templates spec](https://dlang.org/spec/template.html)
- [Templates Revisited](http://dlang.org/templates-revisited.html):  Walter Bright writes about how D improves upon C++ templates.
- [Variadic templates](http://dlang.org/variadic-function-templates.html): Articles about the D idiom of implementing variadic functions with variadic templates

## {SourceCode}

```d
import std.stdio : writeln;

/**
Template class that allows
generic implementation of animals.
Params:
    noise = string to write
*/
class Animal(string noise) {
    void makeNoise() {
        writeln(noise ~ "!");
    }
}

class Dog: Animal!("Woof") {
}

class Cat: Animal!("Meeoauw") {
}

/**
Template function which takes any
type T that implements a function
makeNoise.
Params:
    animal = object that can make noise
    n = number of makeNoise calls
*/
void multipleNoise(T)(T animal, int n) {
    for (int i = 0; i < n; ++i) {
        animal.makeNoise();
    }
}

void main() {
    auto dog = new Dog;
    auto cat = new Cat;
    multipleNoise(dog, 5);
    multipleNoise(cat, 5);
}
```
