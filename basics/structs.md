# Structs

Uma maneira de definir tipos compostos ou personalizados em D é
usar um `struct`:

    struct Person {
        int age;
        int height;
        float ageXHeight;
    }

Por padrão, os `struct`s são construídos na pilha (a menos que sejam criados
com `new`) e são copiados **por valor** em atribuições ou
como parâmetros para chamadas de função.

    auto p = Person(30, 180, 3.1415);
    auto t = p; // cópia

Quando um novo objeto de um tipo `struct` é criado, seus membros podem ser inicializados
na ordem em que são definidos no `struct`. Um construtor personalizado pode ser definido por meio de
uma função membro chamado `this(...)`. Se necessário, para evitar conflitos de nomes, a instância atual
pode ser acessada explicitamente com `this`:

    struct Person {
        this(int age, int height) {
            this.age = age;
            this.height = height;
            this.ageXHeight = cast(float)age * height;
        }
            ...

    Person p = Person(30, 180); // inicialização
    p = Person(30, 180);  // atribuiu nova instância

Um `struct` pode conter qualquer número de funções-membro. Por padrão
elas são `public` e acessíveis do lado de fora. Elas podem
também podem ser `private` e, portanto, só podem ser chamadas por outras
funções-membro do mesmo `struct`, ou outro código no mesmo
módulo.

    struct Person {
        void doStuff() {
            ...
        private void privateStuff() {
            ...

    // Em outro módulo:
    p.doStuff(); // chamando o método doStuff
    p.privateStuff(); // proibido

### Função membro `const`

Se uma função membro for declarada com `const`, não será permitido
modificar nenhum de seus membros. Isso é imposto pelo compilador.
Tornar uma função membro `const` faz com que ela possa ser chamada em qualquer objeto `const`
ou objeto `immutable`, mas também garante aos chamadores que
a função membro nunca alterará o estado do objeto.

### Função membro `static`

Se uma função membro for declarada como `static`, ela poderá ser chamada
sem um objeto instanciado (por exemplo, `Person.myStatic()`), mas ela
não poderá acessar nenhum membro que não seja `static`.  Ele pode ser usado se um método
não precisar acessar nenhum dos campos de membro do objeto, mas logicamente
pertence à mesma classe. Também pode ser usado para fornecer alguma funcionalidade
sem criar uma instância explícita, por exemplo, algumas implementações do padrão de design Singleton usam `static`.

### Herança

Observe que um `struct` não pode herdar de outro `struct`.
As hierarquias de tipos só podem ser construídas usando classes,
que veremos em uma seção posterior.
Entretanto, com `alias this` ou `mixins` é possível obter facilmente
herança polimórfica.

### Maiores detalhes

- [Structs in _Programming in D_](http://ddili.org/ders/d.en/struct.html)
- [Struct specification](https://dlang.org/spec/struct.html)

### Exercício

Dado o `struct Vector3`, implemente as seguintes funções e faça com que
o aplicativo de exemplo seja executado com êxito:

* `length()` - retorna o tamanho do vetor
* `dot(Vector3)` - retorna o ponto do produto de dois vetores
* `toString()` - retorna uma representação de string desse vetor.
  A função [`std.string.format`](https://dlang.org/phobos/std_format.html)
  retorna uma string usando a sintáxe parecido com `printf`:
  `format("MyInt = %d", myInt)`. As strings serão explicadas em detalhes em uma
  seção posterior.

## {SourceCode:incomplete}

```d
struct Vector3 {
    double x;
    double y;
    double z;

    double length() const {
        import std.math : sqrt;
        // TODO: implement the length of Vector3
        return 0.0;
    }

    // rhs will be copied
    double dot(Vector3 rhs) const {
        // TODO: implement the dot product
        return 0.0;
    }
}

void main() {
    auto vec1 = Vector3(10, 0, 0);
    Vector3 vec2;
    vec2.x = 0;
    vec2.y = 20;
    vec2.z = 0;

    // If a member function has no parameters,
    // the calling braces () may be omitted
    assert(vec1.length == 10);
    assert(vec2.length == 20);

    // Test the functionality for dot product
    assert(vec1.dot(vec2) == 0);

    // 1 * 1 + 2 * 1 + 3 * 1
    auto vec3 = Vector3(1, 2, 3);
    assert(vec3.dot(Vector3(1, 1, 1)) == 6);

    // 1 * 3 + 2 * 2 + 3 * 1
    assert(vec3.dot(Vector3(3, 2, 1)) == 10);
}
```
