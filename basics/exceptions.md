# Exceções

Este guia trata apenas de User-`Exceptions` - System-`Errors` geralmente são fatais
e _nunca_ devem ser capturados.

### Captura de exceções

Um caso comum de exceções é a validação de entradas do usuário potencialmente inválidas.
Quando uma exceção for lançada, a pilha será deslocada até que o primeiro manipulador de exceção
correspondente seja encontrado.

```d
try
{
    readText("dummyFile");
}
catch (FileException e)
{
    // ...
}
```

É possível ter vários blocos `catch` e um bloco `finally` que é executado
independentemente da ocorrência de um erro. As exceções são lançadas com `throw`.

```d
try
{
    throw new StringException("You shall not pass.");
}
catch (FileException e)
{
    // ...
}
catch (StringException e)
{
    // ...
}
finally
{
    // ...
}
```

Lembre-se de que o [scope guard](gems/scope-guards) geralmente é uma solução melhor para o padrão `try-finally`.

### Exceções personalizadas

É possível herdar facilmente de `Exception` e criar exceções personalizadas:

```d
class UserNotFoundException : Exception
{
    this(string msg, string file = __FILE__, size_t line = __LINE__) {
        super(msg, file, line);
    }
}
throw new UserNotFoundException("D-Man is on vacation");
```

### Entre em um mundo seguro com o `nothrow`

O compilador D pode garantir que uma função não cause efeitos colaterais catastróficos.
Essas funções podem ser anotadas com a palavra-chave `nothrow`. O compilador D
proíbe estaticamente o lançamento de exceções em funções `nothrow`.

```d
bool lessThan(int a, int b) nothrow
{
    writeln("unsafe world"); // output can throw exceptions, thus this is forbidden
    return a < b;
}
```

Observe que o compilador é capaz de inferir atributos para o código de modelo
automaticamente.

### std.exception

É importante evitar o `assert` e o que será introduzido em breve
[contract programming](gems/contract-programming)
para a entrada do usuário, pois o `assert` e os contratos
são removidos quando compilados no modo de lançamento (release). Por conveniência
[`std.exception`](https://dlang.org/phobos/std_exception.html) fornece
[`enforce`](https://dlang.org/phobos/std_exception.html#enforce)
que pode ser usado como o `assert`, mas lança `Exception`s
em vez de um `AssertError`.

```d
import std.exception : enforce;
float magic = 1_000_000_000;
enforce(magic + 42 - magic == 42, "Floating-point math is fun");

// throw custom exceptions
enforce!StringException('a' != 'A', "Case-sensitive algorithm");
```

No entanto, há mais em `std.exception`. Por exemplo, quando o erro pode não ser
fatal, é possível optar por
[collect](https://dlang.org/phobos/std_exception.html#collectException):

```d
import std.exception : collectException;
auto e = collectException(aDangerousOperation());
if (e)
    writeln("The dangerous operation failed with ", e);
```

Para verificar se uma exceção foi lançada nos testes, use [`assertThrown`](https://dlang.org/phobos/std_exception.html#assertThrown).

### Maiores detalhes

- [Exception Safety in D](https://dlang.org/exception-safe.html)
- [std.exception](https://dlang.org/phobos/std_exception.html)
- system-level [core.exception](https://dlang.org/phobos/core_exception.html)
- [object.Exception](https://dlang.org/library/object/exception.html) - base class of all exceptions that are safe to catch and handle.

## {SourceCode}

```d
import std.file : FileException, readText;
import std.stdio : writeln;

void main()
{
    try
    {
        readText("dummyFile");
    }
    catch (FileException e)
    {
        writeln("Message:\n", e.msg);
        writeln("File: ", e.file);
        writeln("Line: ", e.line);
        writeln("Stack trace:\n", e.info);

        // Default formatting could be used too
        // writeln(e);
    }
}
```
