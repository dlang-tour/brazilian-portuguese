# Lubeck

Biblioteca de álgebra linear de alto nível baseada em CBLAS, LAPACK e Mir Algorithm.

## Dependências

O Lubeck depende do CBLAS e da API do LAPACK. Talvez seja necessário instalá-los e atualizar o `dub.sdl`.
O CBLAS e o LAPACK são pré-instalados no MacOS.
Os backends [OpenBLAS](http://www.openblas.net) ou [Intel MKL](https://software.intel.com/en-us/mkl) são recomendados para Linux e Windows.

## Links

 - [GitHub](https://github.com/kaleidicassociates/lubeck)
 - [Mir Algorithm API](http://mir-algorithm.libmir.org)
 - [Mir Random API](http://mir-random.libmir.org)
 - [Mir API](http://mir.libmir.org)

## {SourceCode:incomplete}

```d
/+dub.sdl:
dependency "lubeck" version="~>1.1"
+/
import kaleidic.lubeck: mtimes;
import mir.algorithm.iteration: each;
import mir.ndslice: magic, repeat, as,
    slice, byDim;
import std.stdio: writeln;

void main()
{
    auto n = 5;
    // Magic Square
    auto matrix = n.magic.as!double.slice;
    // [1 1 1 1 1]
    auto vec = 1.repeat(n).as!double.slice;
    // Uses CBLAS for multiplication
    matrix.mtimes(vec).writeln;
    "-----".writeln;
    matrix.mtimes(matrix).byDim!0.each!writeln;
}
```
