# Fibras

**Fibras** são uma forma de implementar a concorrência
de forma *cooperativa*. A classe `Fiber`
é definida no módulo [`core.thread`](https://dlang.org/phobos/core_thread.html).

A ideia básica é que, quando uma fibra
não tem nada para fazer ou espera por mais entrada, ela
*ativamente* abre mão de sua possibilidade de
executar instruções chamando `Fiber.yield()`.
O contexto principal ganha controle novamente,
mas o estado da fibra - todas as variáveis na pilha - são
salvas. A fibra pode então ser retomada
e continuará na instrução logo *depois* de chamar `Fiber.yield()`.

    void foo() {
        writeln("Hello");
        Fiber.yield();
        writeln("World");
    }
    // ...
    auto f = new Fiber(&foo);
    f.call(); // Imprime Hello
    f.call(); // Imprime World

Esse recurso pode ser usado para implementar a concorrência
em que várias fibras compartilham cooperativamente um único
núcleo. A vantagem das fibras em relação as threads
é que seu uso de recursos é menor porque
não há troca de contexto envolvida.

Um uso muito bom dessa técnica pode ser visto em
[vibe.d](http://vibed.org), que implementa
operações de E/S não bloqueantes (ou assíncronas)
em termos de fibras, o que resulta em um código muito mais limpo.

### Maiores detalhes

- [Fibers in _Programming in D_](http://ddili.org/ders/d.en/fibers.html)
- [Documentation of core.thread.Fiber](https://dlang.org/library/core/thread/fiber.html)

## {SourceCode}

```d
import core.thread : Fiber;
import std.stdio : write;
import std.range : iota;

/**
Itera sobre o `range` e aplica
a função `Fnc` a cada elemento x
e o retorna em `result`. A fibra libera
após cada aplicação.
*/
void fiberedRange(alias Fnc, R, T)(
    R range,
    ref T result)
{
    for(; !range.empty; range.popFront) {
        result = Fnc(range.front);
        Fiber.yield();
    }
}

void main()
{
    int squareResult, cubeResult;
    // Cria uma fibra que é inicializada
    // com um delegate que gera uma raíz
    // quadrada com range.
    auto squareFiber = new Fiber({
        fiberedRange!(x => x*x)(
            iota(1,11), squareResult);
    });
    // .. e aqui criará raíz cúbica!
    auto cubeFiber = new Fiber({
        fiberedRange!(x => x*x*x)(
            iota(1,9), cubeResult);
    });

    // Se o estado for TERM, a fibra finaliza
    // executando sua função associada.
    squareFiber.call();
    cubeFiber.call();
    while (squareFiber.state
        != Fiber.State.TERM && cubeFiber.state
        != Fiber.State.TERM)
    {
        write(squareResult, " ", cubeResult);
        squareFiber.call();
        cubeFiber.call();
        write("\n");
    }

    // squareFiber ainda pode ser executado
    // porque ele ainda não terminou!
}
```
