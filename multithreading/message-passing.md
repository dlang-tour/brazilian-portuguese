# Troca de Mensagens

Em vez de lidar com threads e fazer a sincronização
você mesmo, o D permite usar a *troca de mensagens* como um meio
para aproveitar a potência de vários núcleos. As threads se comunicam
com *mensagens*, que são valores arbitrários, para distribuir
trabalho e sincronizar-se. Eles não compartilham dados
por padrão, o que evita os problemas comuns de
multi-threading.

Todas as funções que implementam a troca de mensagens em D
podem ser encontradas no módulo [`std.concurrency`](https://dlang.org/phobos/std_concurrency.html). O `spawn` cria uma nova *thread* com base em uma
função definida pelo usuário:

    auto threadId = spawn(&foo, thisTid);

`thisTid` faz parte do `std.concurrency` e faz referência a
thread atual, que é necessário para a troca de mensagens. `spawn`
recebe uma função como primeiro parâmetro e
parâmetros adicionais para essa função como argumentos.

    void foo(Tid parentTid) {
        receive(
          (int i) { writeln("An ", i, " was sent!"); }
        );
        
        send(parentTid, "Done");
    }

A função `receive` é como um `switch`-`case`
e despacha os valores que recebe de outras threads
para os `delegates` enviados, dependendo do tipo de valor recebido.

Para enviar uma mensagem a uma thread específico, use a função `send`
e seu identificador:

    send(threadId, 42);

O `receiveOnly` pode ser usado para receber apenas um
tipo especificado:

    string text = receiveOnly!string();
    assert(text == "Done");

As funções da família `receive` bloqueiam até que algo
tenha sido enviado para a caixa de correio da thread.


### Maiores detalhes

- [Exchanging Messages between Threads](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=5)
- [Messaging passing concurrency](http://ddili.org/ders/d.en/concurrency.html)
- [Pattern Matching with receive](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=6)
- [Multi-threaded file copying](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=7)
- [Thread Termination](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=8)
- [Out-of-Band Communication](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=9)
- [Mailbox crowding](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=10)

## {SourceCode}

```d
import std.stdio: writeln;
import std.concurrency;

/*
A custom struct that is used as a message
for a little thread army.
*/
struct NumberMessage {
    int number;
    this(int i) {
        this.number = i;
    }
}

/*
Message is used as a stop sign for other
threads
*/
struct CancelMessage {
}

/// Acknowledge a CancelMessage
struct CancelAckMessage {
}

/*
The thread worker main
function which gets its parent id
passed as argument.
*/
void worker(Tid parentId)
{
    bool canceled = false;
    writeln("Starting ", thisTid, "...");

    while (!canceled) {
      receive(
        (NumberMessage m) {
          writeln("Received int: ", m.number);
        },
        (string text) {
          writeln("Received string: ", text);
        },
        (CancelMessage m) {
          writeln("Stopping ", thisTid, "...");
          send(parentId, CancelAckMessage());
          canceled = true;
        }
      );
    }
}

void main()
{
    Tid threads[];
    // Spawn 10 little worker threads.
    for (size_t i = 0; i < 10; ++i) {
        threads ~= spawn(&worker, thisTid);
    }

    // Odd threads get a number, even threads
    // a string!
    foreach(int idx, ref tid; threads) {
        import std.string: format;
        if (idx  % 2)
            send(tid, NumberMessage(idx));
        else
            send(tid, format("T=%d", idx));
    }

    // And all threads get the cancel message!
    foreach(ref tid; threads) {
        send(tid, CancelMessage());
    }

    // And we wait until all threads have
    // acknowledged their stop request
    foreach(ref tid; threads) {
        receiveOnly!CancelAckMessage;
        writeln("Received CancelAckMessage!");
    }
}
```
