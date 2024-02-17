# Gerador de código (Parser)

Neste exemplo, um parser de configuração é gerado em tempo de compilação.
Vamos supor que nosso programa tenha algumas opções de configuração,
resumidas em uma `struct` de configurações:

```d
struct Config
{
    int runs, port;
    string name;
}
```

Embora escrever um parser para essa estrutura não seja difícil, teríamos que
atualizar constantemente o parser, sempre que modificarmos o objeto `Config`.
Por isso, estamos interessados em escrever uma função `parse` genérica que possa
ler opções de configuração arbitrárias. Para simplificar, `parse` aceitará
um formato muito simples de opções de configuração `key1=value1,key2=value2`, mas a mesma técnica
pode ser usada para qualquer formato de configuração arbitrário. Para muitos formatos de configuração
formatos de configuração populares, é claro, já existem soluções prontas de outros parsers no [registro do DUB](https://code.dlang.org).

Configuração de leitura
-------------------------

Vamos supor que o usuário tenha __"name=dlang,port=8080"__ como uma string de configuração.
Em seguida, dividimos diretamente as opções de configuração por vírgula e chamamos `parse` para cada definição de configuração, individualmente.
Depois que todas as opções de configurações tiverem sido analisadas, todo o objeto de configuração será exibido.

Análise
-----

O `parse` é onde a verdadeira mágica acontece, mas primeiro dividimos a opção de configuração fornecida (por exemplo, "name=dlang") por "=" em chave ("name") e valor ("dlang").
A instrução `switch` é executada com a chave analisada, mas o mais interessante é que
os casos de `switch` foram gerados estaticamente. O `c.tupleof` retorna uma lista de todos os membros no formato `(idx, name)`. O compilador detecta que o `c.tupleof` é conhecido em tempo de compilação e desenrolará o loop foreach em tempo de compilação.
Portanto, `Conf.tupleof[idx].stringof` produzirá os membros individuais do objeto struct
e gerará uma instrução de caso para cada membro.

Da mesma forma, enquanto estiver no loop estático, os membros individuais podem ser acessados pelo índice:
`c.tupleof[idx]` e, assim, podemos atribuir ao respectivo membro o valor analisado da
string de configuração fornecida. Além disso, `dropOne` é necessário, pois o `range` dividido ainda aponta para a chave e, portanto, `dropOne.front` retornará o segundo elemento.
Além disso, `to!(typeof(field))` fará a análise real da string de entrada
para o respectivo tipo do membro da estrutura de configuração.
Por fim, como o loop `foreach` é executado em tempo de compilação, um `break` interromperia esse loop.

Entretanto, depois que uma opção de configuração for analisada com sucesso, não queremos pular para o próximo caso na instrução `switch` e, portanto, um `break` é utilizado para interromper a instrução `switch`.

## {SourceCode}

```d
import std.algorithm, std.conv, std.range,
        std.stdio, std.traits;
struct Config
{
    int runs, port;
    string name;
}
void main()
{
    Config conf;
    // use o parser gerado a
    // cada entrada
    "runs=1,port=2,name=hello"
        .splitter(",")
        .each!(e => conf.parse(e));
    conf.writeln;
}
void parse(Conf)(ref Conf c, string entry)
{
    auto r = entry.splitter("=");
    auto key = r.front, value = r.dropOne.front;
    `Switch`: `switch` (key)
    {
        static foreach(idx, field; Conf.tupleof)
        {
            case field.stringof:
                c.tupleof[idx] =
                    value.to!(typeof(field));
                break `Switch`;
        }
        default:
        assert (0, "Unknown member name.");
    }
}
```
