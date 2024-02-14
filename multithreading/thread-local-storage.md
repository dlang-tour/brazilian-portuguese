# Thread local storage

A classe de armazenamento `static` permite declarar objetos
que são inicializados apenas uma vez. Se a mesma linha
for executada uma segunda vez, a inicialização
será omitida.
Cada thread terá seu próprio objeto `static` (*TLS - thread local storage*)
e não poderá ler ou modificar o objeto `static` de outra thread,
embora o nome da variável
permaneça o mesmo. Assim, o `static` permite declarar um objeto
que mantém o estado que é global para a thread *atual*.

Isso é diferente de
C, C++ e Java, por exemplo, onde `static` de fato significa global
para o aplicativo, o que acarreta problemas de sincronização
em aplicativos com múltiplas threads.

O valor atribuído a uma variável `static` deve
ser avaliável em tempo de compilação. Ele não deve ter
dependências de tempo de execução! É possível inicializar
variáveis `static` em tempo de execução usando um construtor
único `static this()` para structs, classes e módulos.

    static int b = 42;
    // b é inicializado apenas uma vez!
    // Quando executado em diferentes threads
    // cada thread terá sua
    // "própria" variavel b sem interferência de
    // outras threads.

Além disso, para a declaração de uma variável global "clássica" que
cada thread pode ver e modificar,
use a classe de armazenamento `__gshared`, que é equivalente
ao `static` do C.
Seu nome feio é um lembrete amigável para usá-la raramente.

    __gshared int b = 50;
    // Também inicializado apenas uma vez!
    // Um b verdadeiramente global que toda thread
    // pode ler - e que o torna perigoso -
    // modificar!

### Maiores detalhes

- [Thread-local storage on Wikipedia](https://en.wikipedia.org/wiki/Thread-local_storage)

## {SourceCode}

```d
import std.concurrency : spawn, thisTid;

void worker(bool firstTime)
{
    import std.stdio : writeln;
    // threadState é global para
    // thread atual. Nenhuma outra thread
    // poderá acessá-la. Observe que
    // é inicializado apenas na
    // primeira vez que a linha é executada.
    static int threadState = 0;
    writeln("Thread ", thisTid,
        ": My state = ", threadState++);
    if (firstTime)
        worker(false);
}

void main()
{
    // Cria 5 threads nesta chamada
    // havendo um worker(true,i) cada.
    for (size_t i = 0; i < 5; ++i) {
        spawn(&worker, true);
    }
}
```
