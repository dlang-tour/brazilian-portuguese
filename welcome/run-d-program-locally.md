# Executando um programa em D localmente

O D é acompanhado por um compilador `dmd`, uma ferramenta de execução
em formato de script `rdmd` e um gerenciador de pacotes `dub`.

### Compilador DMD

O compilador *DMD* compila arquivo(s) em D e cria um executável.
Na linha de comando o *DMD* pode ser chamado com o nome do arquivo da seguinte forma:

    dmd hello.d

Há várias opções que permitem modificar o comportamento do compilador *DMD*.
Busque na [Documentação Online](https://dlang.org/dmd.html#switches) ou execute `dmd --help` para uma descrição das flags.

### Compilação "On-the-fly" com `rdmd`

A ferramenta auxiliar `rdmd`, distribuida com o compilador DMD, 
é encarregada de compilar todas as depencencias e automaticamente
executar o programa final.

    rdmd hello.d

Em sistemas UNIX a linha `#!/usr/bin/env rdmd` pode ser colocada na
primeira linha de um arquivo D executável para permitir seu uso como script.
Busque na [Documentação Online](https://dlang.org/rdmd.html) ou execute `dmd --help` para uma descrição das flags.

### Gerenciador de Pacotes `dub`

O gerenciador de pacotes padrão do D é o [`dub`](http://code.dlang.org). Quando 
o `dub` é instalado localmente um novo projeto `hello` pode ser criado usando 
o comando:

    dub init hello

Executando `dub` dentro de um pasta ele busca todas as dependencias, compila
a aplicação e a executa.


`dub build` compilará o projeto.

Busque na [Documentação Online](https://code.dlang.org/docs/commandline) ou execute `dmd --help` para uma descrição dos comandos e features disponíveis.

Todos os pacotes disponível do dub podem ser encontrados na [interface web](https://code.dlang.org) do dub.
