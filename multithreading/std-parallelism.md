# Paralelismo

O módulo `std.parallelism` implementa
primitivas de alto nível para uma programação simultânea conveniente.

### Parallel

[`std.parallelism.parallel`](http://dlang.org/phobos/std_parallelism.html#.parallel) permite distribuir automaticamente
o corpo de um `foreach` para diferentes threads:

    // raíz quadrada em paralelo
    // dos elementos arr
    auto arr = iota(1,100).array;
    foreach(ref i; parallel(arr)) {
        i = i*i;
    }

`parallel` utiliza internamente o operador `opApply`.
`parallel` global é um atalho para o `taskPool.parallel`
que é um `TaskPool` que usa *número total de cpus - 1*
de _working threads_. Criar sua própria instância permite
controlar o grau de paralelismo.

Observe que o corpo de uma iteração `parallel` deve
garantir que não modifique itens aos quais outra
unidade de trabalho possa ter acesso.

O `workingUnitSize` opcional especifica o número de elementos processados
por __worker thread__.

### reduce

A função
[`std.algorithm.iteration.reduce`](http://dlang.org/phobos/std_algorithm_iteration.html#reduce) -
conhecida em outros contextos funcionais como *accumulate* ou *foldl* -
chama uma função `fun(acc, x)` para cada elemento `x`
em que `acc` é o resultado anterior:

    // 0 é a "semente"
    auto sum = reduce!"a + b"(0, elements);

[`Taskpool.reduce`](http://dlang.org/phobos/std_parallelism.html#.TaskPool.reduce)
é o análogo paralelo do `reduce`:

    // Encontre a soma de um range em paralelo, usando o primeiro
    // elemento de cada unidade de trabalho como semente.
    auto sum = taskPool.reduce!"a + b"(nums);

O `TaskPool.reduce` divide o range em
sub-ranges que são reduzidos em paralelo. Depois que esses
resultados tiverem sido calculados, os resultados serão reduzidos
por si mesmos.

### `task()`

[`task`](http://dlang.org/phobos/std_parallelism.html#.task) é um wrapper para uma função
que pode demorar mais ou que deve ser executada em
sua própria __worker thread__. Ela pode ser enfileirada
em um *taskpool*:

    auto t = task!read("foo.txt");
    taskPool.put(t);

Ou ser executado diretamente em sua própria e nova thread:

    t.executeInNewThread();

Para obter o resultado de uma __task__, chame `yieldForce`
sobre ela. Ela será bloqueada até que o resultado esteja disponível.

    auto fileData = t.yieldForce;

### Maiores detalhes

- [Parallelism in _Programming in D_](http://ddili.org/ders/d.en/parallelism.html)
- [std.parallelism](http://dlang.org/phobos/std_parallelism.html)

## {SourceCode}

```d
import std.parallelism : task,
    taskPool, TaskPool;
import std.array : array;
import std.stdio : writeln;
import std.range : iota;

string theTask()
{
    import core.thread : dur, Thread;
    Thread.sleep( dur!("seconds")(1) );
    return "Hello World";
}

void main()
{
    // taskpool com duas threads
    auto myTaskPool = new TaskPool(2);
    // Interromper taskpool é importante!
    scope(exit) myTaskPool.stop();

    // Começa executnado uma task
    // que durará um certo tempo
    auto task = task!theTask;
    myTaskPool.put(task);

    auto arr = iota(1, 10).array;
    foreach(ref i; myTaskPool.parallel(arr)) {
        i = i*i;
    }

    writeln(arr);

    import std.algorithm.iteration : map;

    // Usa reduce para calcular a soma
    // de todas as raizes quadradas
    // paralelamente.
    auto result = taskPool.reduce!"a+b"(
        0.0, iota(100).map!"a*a");
    writeln("Sum of squares: ", result);

    // Pega o resultado em segundo-plano
    // antecipadamente.
    writeln(task.yieldForce);
}
```
