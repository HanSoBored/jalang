# Jalang Syntax

Jalang is **C with Javanese keywords**. All C semantics remain — only the cosmetics change: keywords, preprocessor directives, and file extensions. Original C keywords **still work** (`int` and `bilangan` are both valid).

## File extensions

| Extension | Meaning |
|---|---|
| `.jawa` | C source |
| `.jawah` | C source |

## Keywords

### Control flow

| C | Javanese |
|---|---|
| `if` | `nek` |
| `else` | `nekora` |
| `for` | `ngo` |
| `while` | `pas` |
| `do` | `lakokke` |
| `switch` | `mileh` |
| `case` | `kasus` |
| `default` | `standar` |
| `break` | `mandek` |
| `continue` | `lanjutke` |
| `return` | `bali` |

### Data types

| C | Javanese |
|---|---|
| `int` | `bilangan` |
| `char` | `huruf` |
| `void` | `rabali` |
| `float` | `desimal` |
| `double` | `gede` |
| `short` | `cilik` |
| `long` | `ombo` |
| `bool` | `logika` |
| `struct` | `wadah` |
| `union` | `tumpuk` |
| `enum` | `daftar` |
| `typedef` | `jenengi` |

### Qualifiers & storage

| C | Javanese |
|---|---|
| `const` | `paten` |
| `static` | `internal` |
| `extern` | `njobo` |
| `register` | `simpen` |
| `volatile` | `isoowah` |
| `restrict` | `batesi` |
| `sizeof` | `ukurane` |

### Literals & C23

| C | Javanese |
|---|---|
| `true` | `bener` |
| `false` | `salah` |
| `static_assert` | `pastekke` |
| `thread_local` | `thread_lokal` |

### Unchanged keywords

These keywords keep their C spelling:

`goto`, `signed`, `unsigned`, `inline`, `auto`, `typeof`, `typeof_unqual`, `alignas`, `alignof`, `_Atomic`, `_Generic`, `_Noreturn`, `_Complex`, `_Imaginary`, `__attribute__`, `asm`

## Preprocessor directives

| C | Javanese |
|---|---|
| `#include` | `#melokke` |
| `#define` | `#tentokke` |
| `#if` | `#nek` |
| `#ifdef` | `#nekenek` |
| `#ifndef` | `#nekoraenek` |
| `#elif` | `#nekliyane` |
| `#else` | `#nekora` |
| `#endif` | `#rampungnek` |
| `#undef` | `#batalke` |
| `#pragma` | `#prentah` |
| `#warning` | `#atiati` |
| `#line` | `#garis` |

### Unchanged directives

These directives keep their C spelling:

`#error`, `#include_next`, `#import`, `#embed`, `#assert`

## Full example

```program.jawa
#melokke <stdio.h>
#melokke <unistd.h>

bilangan utama(bilangan argc, huruf **argv) {
    nek (argc > 1) {
        printf("halo %s\n", argv[1]);
    } nekora {
        printf("argumen kosong\n");
    }

    ngo (bilangan i = 0; i < argc; i++) {
        printf("arg[%d] = %s\n", i, argv[i]);
    }

    bali 0;
}
```

Compile:

```sh
jalang -o program program.jawa
./program "dunia"
```

## Notes

- Original C keywords and directives **still work** — `int`/`bilangan`, `#include`/`#melokke` are both valid
- Compiler error messages remain in English
- Compile with `-std=gnu23` for C23 features without compat warnings (`logika`, `bener`, `salah`, `pastekke`, `thread_lokal`)