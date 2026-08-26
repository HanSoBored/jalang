# Editor support for jalang

Editor tooling (tree-sitter grammar, Vim syntax, filetype detection) lives in
its own repository: **[HanSoBored/jalang-editor](https://github.com/HanSoBored/jalang-editor)**.

- **Tree-sitter grammar** — `tree-sitter-jalang/` (highlighting, parsing)
- **Vim syntax** — `syntax/jalang.vim` + `ftdetect/jalang.vim`
- **clangd LSP** — built from the jalang tree, knows the Javanese keywords
- **Indent** — `tree-sitter-jalang/queries/indents.scm` (derived from C)

## Quick setup (Neovim)

Clone the editor repo and add it to your runtimepath:

```sh
git clone https://github.com/HanSoBored/jalang-editor ~/jalang-editor
```

```lua
-- init.lua
vim.opt.rtp:append(vim.fn.stdpath("config") .. "/jalang-editor")
vim.filetype.add({ extension = { jawa = "jalang", jawah = "jalang" } })

local jalang_so = vim.fn.expand("~/jalang-editor/tree-sitter-jalang/jalang.so")
if vim.fn.filereadable(jalang_so) == 1 then
  vim.treesitter.language.add("jalang", { path = jalang_so })
end
```

Or symlink the support dirs into your config:

```sh
mkdir -p ~/.config/nvim/jalang-editor/{syntax,ftdetect,queries/jalang}
ln -s ~/jalang-editor/syntax/jalang.vim   ~/.config/nvim/jalang-editor/syntax/jalang.vim
ln -s ~/jalang-editor/ftdetect/jalang.vim ~/.config/nvim/jalang-editor/ftdetect/jalang.vim
ln -s ~/jalang-editor/tree-sitter-jalang/queries/highlights.scm ~/.config/nvim/jalang-editor/queries/jalang/highlights.scm
ln -s ~/jalang-editor/tree-sitter-jalang/queries/indents.scm    ~/.config/nvim/jalang-editor/queries/jalang/indents.scm
```

## Tree-sitter grammar

The grammar is derived from tree-sitter-c: both C keywords (`int`, `if`, ...)
and Javanese aliases (`bilangan`, `nek`, ...) are valid. Javanese
preprocessor directives parse as their C node aliases (`#melokke` →
`#include`), so existing C queries keep working.

Rebuild the parser after changing the grammar:

```sh
cd tree-sitter-jalang
tree-sitter generate
cc -shared -fPIC -O2 -o jalang.so -I src src/parser.c
```

Test:

```sh
tree-sitter parse test.jawa
tree-sitter test   # corpus tests
```

## clangd LSP

Use the clangd built from the jalang tree (shipped in the jalang CI
artifacts) — the stock clangd doesn't know the Javanese keywords. It
recognizes `.jawa` as C source and parses the Javanese keywords natively.

Register the filetype with nvim-lspconfig:

```lua
vim.lsp.config("clangd", {
  filetypes = { "c", "cpp", "objc", "objcpp", "jalang" },
})
vim.lsp.enable("clangd")
```

For multi-file projects, generate a `compile_commands.json`:

```sh
jalang -MJ compile_commands.json -o /dev/null *.jawa
```

## Auto-indent

`tree-sitter-jalang/queries/indents.scm` is the C indents query (the grammar
shares C's node names). Enable tree-sitter indent with a cindent fallback for
incomplete code (unclosed braces while typing):

```lua
vim.api.nvim_create_autocmd("FileType", {
  pattern = { "c", "cpp", "jalang" },
  callback = function()
    vim.bo.cindent = true
    vim.bo.indentexpr = "v:lua.require'config.indent'.c_indentexpr()"
  end,
})
```

Where `config.indent.c_indentexpr` falls back to `cindent` when the
tree-sitter parse has errors (see the nvim-treesitter indent docs).

## Troubleshooting

- **No highlighting** — the filetype isn't detected: check `:set filetype?`
  in a `.jawa` buffer; it must be `jalang`. Make sure the runtimepath entry
  above is active (restart Neovim).
- **`gd` doesn't work** — clangd isn't running: `:LspInfo` should show
  clangd attached. Make sure the clangd from the jalang build is on `PATH`
  (`clangd --version` must report version 24, not 21).
- **No auto-indent after `{`** — the FileType autocmd above must include
  `jalang` in its pattern.