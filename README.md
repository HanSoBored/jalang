# jalang

**jalang** — a C compiler with Javanese keywords, based on [LLVM/Clang](https://llvm.org).

A fork of LLVM/Clang that renames C keywords and preprocessor directives to Javanese, and recognizes `.jawa`/`.jawah` files.

## Features

- **Javanese keywords** — `bilangan` → `int`, `nek` → `if`, `bali` → `return`, etc. (see [SYNTAX.md](SYNTAX.md))
- **Javanese preprocessor directives** — `#melokke` → `#include`, `#tentokke` → `#define`, etc.
- **`.jawa` / `.jawah` extensions** — recognized by the driver as C source
- **`jalang` binary** — the clang driver is built as `jalang`
- **Full C semantics** — pointers, structs, syscalls, all C libraries work
- **Native output** — machine code via the LLVM backend

## Example

```halo.jawa
#melokke <stdio.h>

bilangan utama() {
    printf("halo jawa\n");
    bali 0;
}
```

Compile:

```sh
jalang -o halo halo.jawa
./halo
# halo jawa
```

## Build

### GitHub Actions

Workflow [`.github/workflows/build-jalang.yml`](.github/workflows/build-jalang.yml) builds for:

- **Linux x86_64**
- **Android arm64** — NDK cross-compile, for Termux

Artifacts: `jalang-linux-x86_64.tar.gz` and `jalang-android-arm64.tar.gz`.

### Manual

```sh
cmake -G Ninja -S llvm -B build \
  -DLLVM_ENABLE_PROJECTS=clang \
  -DCMAKE_BUILD_TYPE=Release \
  -DLLVM_TARGETS_TO_BUILD=host
cmake --build build --target clang -j$(nproc)
# binary: build/bin/jalang
```

## License

Fork of the [LLVM Project](https://github.com/llvm/llvm-project), licensed under the **Apache License v2.0 with LLVM Exceptions** (see [LICENSE.TXT](LICENSE.TXT)).