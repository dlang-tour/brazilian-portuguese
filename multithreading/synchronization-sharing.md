# Sincronização & Compartilhamento

Em D, a maneira preferida de fazer multi-threading
é confiar em dados `immutable` e sincronizar as threads
usando a troca de mensagens. No entanto, a linguagem
tem suporte a primitivas de *sincronização*, bem como
suporte ao sistema de tipos com `shared` para marcar objetos
que são acessados de várias threads.

Use o identificador de tipo `shared` para marcar variáveis
que são compartilhadas entre diferentes threads:

    shared(int)* p = new int;
    int* t = p; // ERRO

Por exemplo, o `std.concurrency.send` só permite o envio de
dados `immutable` ou `shared` e copiar a mensagem
a ser enviada. O `shared` é transitivo, portanto, se uma `class` ou `struct`
for marcado como `shared`, todos os seus membros também serão.
Observe que as variáveis `static` não são `shared` por
padrão porque são implementadas usando
*thread local storage* (TLS) e cada thread recebe
sua própria variável.

Os blocos `synchronized` são usados para informar ao compilador
para criar uma seção crítica que só pode ser acessada
por uma thread de cada vez. Sem argumentos, um mutex exclusivo
para essa instrução será bloqueado e desbloqueado.

    synchronized {
        importStuff();
    }

A sincronização pode ser limitada somente a um objeto da classe
*mutex*, passando o objeto como um argumento usando
`synchronized (obj)` para reduzir a contenção.

O compilador D insere *seções críticas* automaticamente.
Uma classe inteira pode ser marcada
como `synchronized` e, nesse caso, o compilador
garante que apenas uma thread acesse uma instância
concreta de cada vez.

As operações atômicas em variáveis `shared` podem ser
executadas usando o operador auxiliar `core.atomic.atomicOp`:

    shared int test = 5;
    test.atomicOp!"+="(4);

### Maiores detalhes

- [Data Sharing Concurrency in _Programming in D_](http://ddili.org/ders/d.en/concurrency_shared.html)
- [`shared` type qualifier](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=11)
- [Lock-Based Synchronization with `synchronized`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=13)
- [Deadlocks and `synchronized`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=15)
- [`synchronized` specification](https://dlang.org/spec/statement.html#SynchronizedStatement)
- [`synchronized` classes](https://dlang.org/spec/class.html#synchronized-classes)
- [`shared` type qualifier](https://dlang.org/spec/const3.html#shared)
- [Implicit conversions with `shared` data types](https://dlang.org/spec/const3.html#implicit_qualifier_conversions)

## {SourceCode}

```d
import std.concurrency : receiveOnly, send,
    spawn, Tid, thisTid;
import core.atomic : atomicOp, atomicLoad;

/*
Queue that can be used safely among
different threads. All access to an
instance is automatically locked thanks to
synchronized keyword.
*/
synchronized class SafeQueue(T)
{
    // Note: must be private in synchronized
    // classes otherwise D complains.
    private T[] elements;

    void push(T value) {
        elements ~= value;
    }

    /// Return T.init if queue empty
    T pop() {
        import std.array : empty;
        T value;
        if (elements.empty)
            return value;
        value = elements[0];
        elements = elements[1 .. $];
        return value;
    }
}

/*
Safely print messages independent of
number of concurrent threads.
Note that variadic parameters are used
for args! That is args might be 0 .. N
parameters.
*/
void safePrint(T...)(T args)
{
    // Just executed by one concurrently
    synchronized {
        import std.stdio : writeln;
        writeln(args);
    }
}

void threadProducer(shared(SafeQueue!int) queue,
    shared(int)* queueCounter)
{
    import std.range : iota;
    // Push values 1 to 10
    foreach (i; 1..11) {
        queue.push(i);
        safePrint("Pushed ", i);
        atomicOp!"+="(*queueCounter, 1);
    }
}

void threadConsumer(Tid owner,
    shared(SafeQueue!int) queue,
    shared(int)* queueCounter)
{
    int popped = 0;
    while (popped != 10) {
        auto i = queue.pop();
        if (i == int.init)
            continue;
        ++popped;
        // safely fetch current value of
        // queueCounter using atomicLoad
        safePrint("Popped ", i,
            " (Consumer pushed ",
            atomicLoad(*queueCounter), ")");
    }

    // I'm done!
    owner.send(true);
}

void main()
{
    auto queue = new shared(SafeQueue!int);
    shared int counter = 0;
    spawn(&threadProducer, queue, &counter);
    auto consumer = spawn(&threadConsumer,
                    thisTid, queue, &counter);
    auto stopped = receiveOnly!bool;
    assert(stopped);
}
```
