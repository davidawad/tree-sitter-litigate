# AGENTS.md -- tree-sitter-litigate

Standalone, publishable **tree-sitter grammar** for litigate (`.lit` rule
modules and `.case` matters). Grammar and nothing else: `grammar.js` and
`grammar/*.js`, the external scanner in `src/scanner.c`, the committed
generated sources under `src/`, the queries under `queries/litigate/`, and
the test corpus.

This repo is consumed by editors, not by people's vimrcs. The Vim/Neovim
plugin is a separate repo, [litigate.vim](https://github.com/davidawad/litigate.vim),
the Helix packaging is [helix-litigate](https://github.com/davidawad/helix-litigate),
and the Emacs package is [litigate-mode.el](https://github.com/davidawad/litigate-mode.el).

## For agents

- Read `README.md` first; it is the complete feature, install-per-consumer,
  and known-limitations reference.
- Regenerate the parser after any `grammar.js`, `grammar/*.js` or
  `src/scanner.c` change: `tree-sitter generate`, then `tree-sitter test`
  (corpus in `test/corpus/`, highlight assertions in `test/highlight/`),
  then `tree-sitter parse` over real `.lit`/`.case` files and confirm zero
  `(ERROR)` / `MISSING` nodes before considering a change done.
- The compiler's hand-written parser (`crates/litigate-syntax` in litgraph)
  is the source of truth for syntax. A file it accepts and this grammar
  rejects is a grammar bug; the reverse is the compiler's business.
- **Grammar correctness and consumer discovery are different problems.**
  `tree-sitter test` passing says nothing about whether an editor can find
  the parser or the queries. Query-under-the-wrong-directory, a `.so` built
  to a path nothing searches, and a plugin manager's default-branch mismatch
  all fail silently. When you change layout, verify resolution by name (see
  README's "Testing / verification"), not by eye.
- **Consumers pin this repo's commit** (`GRAMMAR_REV` in litigate.vim,
  `rev` in helix-litigate and in every `languages.toml`). Pushing here does
  not change what anyone's editor does until they bump the pin; bump all
  of them in the same change that matters.
- The parser is committed as tree-sitter ABI 15. Consumers on an older
  libtree-sitter regenerate with `--abi 14`; do not commit an ABI 14 parser.
- `queries/*.scm` are symlinks into `queries/litigate/`. Edit the real files.
  Helix-flavoured query copies live in helix-litigate and must be updated
  by hand when `highlights.scm`/`indents.scm`/`locals.scm` change (Helix
  names some captures differently).
- Keeping the generated sources (`src/parser.c`, `src/grammar.json`,
  `src/node-types.json`) committed is deliberate: consumers that build
  without running `tree-sitter generate` depend on them.
- Zero references to the owner's dotfiles are allowed here -- the repo
  must remain publishable as-is.
- Authorized: david, swe.

## Provenance

Extracted from `editors/tree-sitter-litigate` in litgraph on 2026-10-23
(litgraph commit `6dbf033`). litgraph keeps its in-repo copy and the Rust
bindings; this repo is what editors pin.
