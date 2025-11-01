# Classes

O D oferece suporte a classes e interfaces como em Java ou C++.

Qualquer tipo de `class` herda implicitamente do [`Object`](https://dlang.org/phobos/object.html) (objeto).

    class Foo { } // herda de Object
    class Bar : Foo { } // Bar é incorporado ao Foo

As classes em D geralmente são instanciadas no heap usando `new`:

    auto bar = new Bar;

Os objetos de classe são sempre tipos de referência e, ao contrário do `struct`, não são
copiados por valor.

    Bar bar = foo; // bar aponta para foo

O coletor de lixo garantirá que a memória seja liberada
quando não houver mais referências a um objeto.

#### Herança

Se uma função de membro de uma classe base for substituída, a palavra-chave
`override` deve ser usada para indicar isso. Isso evita a sobreposição não 
intencional de funções.

    class Bar : Foo {
        override functionFromFoo() {}
    }

Em D, as classes só podem herdar de uma classe.

### Funções de membro Final e abstract

- Uma função pode ser declarada como `final` em uma classe base para não permitir
a sobreposição dela
- Uma função pode ser declarada como `abstract` para forçar as classes derivadas
a sobrepor ela
- Uma classe inteira pode ser declarada como `abstract` para garantir que
que ela não seja instanciada
- `super(...)` pode ser usado para chamar explicitamente o construtor da base

### Verificação de equivalência

Para objetos de classe, os operadores `==` e `!=` comparam o conteúdo dos objetos.
Portanto, a comparação com `null` é inválida, pois `null` não tem conteúdo.
O `is` compara a equivalência. Para comparar a não-equivalência, use `e1 !is e2`.

```d
MyClass c;
if (c == null)  // erro
    ...
if (c is null)  // ok
    ...
```

Para objetos `struct`, todos os bits são comparados,
para outros tipos de operandos, equivalência é o mesmo que igualdade.

### Maiores detalhes

- [Classes in _Programming in D_](http://ddili.org/ders/d.en/class.html)
- [Inheritance in _Programming in D_](http://ddili.org/ders/d.en/inheritance.html)
- [Object class in _Programming in D_](http://ddili.org/ders/d.en/object.html)
- [Classes in D spec](https://dlang.org/spec/class.html)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Fancy type which can be used for
anything...
*/
class Any {
    // protected is just seen by inheriting
    // classes
    protected string type;

    this(string type) {
        this.type = type;
    }

    // public is implicit by the way
    final string getType() {
        return type;
    }

    // This needs to be implemented!
    abstract string convertToString();
}

class Integer : Any {
    // just seen by Integer
    private {
        int number;
    }

    // constructor
    this(int number) {
        // call base class constructor
        super("integer");
        this.number = number;
    }

    // This is implicit. And another way
    // to specify the protection level
    public:

    override string convertToString() {
        import std.conv : to;
        // The swiss army knife of conversion.
        return to!string(number);
    }
}

class Float : Any {
    private float number;

    this(float number) {
        super("float");
        this.number = number;
    }

    override string convertToString() {
        import std.string : format;
        // We want to control precision
        return format("%.1f", number);
    }
}

void main()
{
    Any[] anys = [
        new Integer(10),
        new Float(3.1415f)
    ];

    foreach (any; anys) {
        writeln("any's type = ", any.getType());
        writeln("Content = ",
            any.convertToString());
    }
}
```
