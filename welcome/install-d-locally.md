# Instalar D localmente

No website da linguagem D [dlang.org](https://dlang.org) você pode [baixar](http://dlang.org/download.html) e instalar a versão mais recente do compilador **DMD** (Digital Mars D).

### Windows

* [Instalador](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd-{{latest-release}}.exe)
* ou: [Archive](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.windows.7z)
* usando [chocolatey](https://chocolatey.org/packages/dmd): `choco install dmd`

### macOS

* `.dmg` [pacote](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.dmg)
* ou: [Archive](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.osx.tar.xz)
* usando [Homebrew](http://brew.sh): `brew install dmd`

### Linux / FreeBSD / macOS

Para instalar o dmd em seu diretório de usuário, execute: `curl -fsS https://dlang.org/install.sh | bash -s dmd`

São fornecidos pacotes para várias distribuições:

* [ArchLinux](https://wiki.archlinux.org/index.php/D_(programming_language))
* [Debian/Ubuntu](http://d-apt.sourceforge.net).
* [Fedora/CentOS](http://dlang.org/download.html#dmd)
* [Gentoo](https://wiki.gentoo.org/wiki/Dlang)
* [OpenSuse](http://dlang.org/download.html#dmd)

### Outros compiladores

Além do compilador DMD que usa seu próprio backend, há outros dois compiladores
que podem ser baixados atráves do [dlang.org](https://dlang.org)

* [**GDC**](http://gdcproject.org/downloads) que usa o backend GCC
* [**LDC**](https://github.com/ldc-developers/ldc#installation) beseado no backend LLVM

GDC e LDC nem sempre estão com a versão mais recente do frontend DMD,
mas tem melhores níveis de otimização e suporte para outras plataformas,
por exemplo ARM.

Veja [mais informações](https://wiki.dlang.org/Compilers) no wiki

