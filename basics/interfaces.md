# Interfaces

O D permite definir `interface`s que são tecnicamente como os tipos `class`,
mas cujas funções membros devem ser implementadas
por qualquer classe que herde da `interface`.

    interface IAnimal {
        void makeNoise();
    }

A função membro `makeNoise` deve ser implementada
por `Dog` porque ela herda da interface `Animal`.
Essencialmente, `makeNoise` se comporta como uma função membro `abstract`
em uma classe base.

    class Dog : IAnimal {
        void makeNoise() {
            ...
        }
    }

    IAnimal animal = new Dog(); // conversão implícita a interface
    animal.makeNoise();

Embora uma classe só possa herdar diretamente de *uma* classe base,
ela pode implementar *qualquer número* de interfaces.

### NVI (non virtual interface) pattern

O [NVI pattern](https://en.wikipedia.org/wiki/Non-virtual_interface_pattern)
permite métodos _não virtuais_ para uma interface comum.
Portanto, esse padrão impede a violação de um padrão de execução comum.
D ativa o padrão NVI ao
permitir funções `final` (ou seja, não substituíveis) em uma `interface`.
Isso impõe comportamentos específicos personalizados por meio da substituição de
outras funções abstratas de `interface`.

    interface IAnimal {
        void makeNoise();
        final doubleNoise() // NVI pattern
        {
            makeNoise();
            makeNoise();
        }
    }

### Maiores detalhes

- [Interfaces in _Programming in D_](http://ddili.org/ders/d.en/interface.html)
- [Interfaces in D](https://dlang.org/spec/interface.html)

## {SourceCode}

```d
import std.stdio : writeln;

interface IAnimal {
    void makeNoise();

    /*
    NVI pattern. Uses makeNoise internally
    to customize behaviour inheriting
    classes.

    Params:
        n =  number of repetitions
    */
    final void multipleNoise(int n) {
        for(int i = 0; i < n; ++i) {
            makeNoise();
        }
    }
}

class Dog: IAnimal {
    void makeNoise() {
        writeln("Woof!");
    }
}

class Cat: IAnimal {
    void makeNoise() {
        writeln("Meeoauw!");
    }
}

void main() {
    IAnimal dog = new Dog;
    IAnimal cat = new Cat;
    IAnimal[] animals = [dog, cat];
    foreach(animal; animals) {
        animal.multipleNoise(5);
    }
}
```
