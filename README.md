# Simple WIT reuse example

This repository aims to contain the simplest possible example of [WIT][wit] reuse across components
(or [host components][host-components] which are not discussed here).

## As simple as it gets, no tools

The basic idea is to:

1. Create a "shared" WIT package (text files, in a folder, e.g. `somewhere/wit/resuable/stuff.wit`)
2. Copy them to the `wit/deps` of relevant projects
3. Create the WIT files that correspond to the relevant project *outside* of `wit/deps` (e.g. `project/wit/component.wit`)
4. Write relevant `world`s in the project-specific WIT file (e.g. `project/wit/component.wit`)

From there, maintaining this setup is a matter of copying the "shared" WIT package `pkg/wit/reusable/*.wit` 
to `project/wit/deps/reusable/`, repeatedly after updates.

[wit]: https://github.com/WebAssembly/component-model/blob/main/design/mvp/WIT.md
[host-components]: https://docs.rs/wasmtime/latest/wasmtime/component/index.html

## The "correct" way (using `wkg`)

The ecosystem has built tooling, in particular [`wkg`][wkg].

`wkg` can be somewhat surprising to configure (Issues and PRs are welcome!), but it enables a lot of functionality:

1. Publishing OCI images that contain WIT-only WASM binaries
  - WIT-only WASM binaries are as-good-as-text as far as specifying interfaces
2. Resolving local/on-disk packages

As (2) is probably important, the first place to look is the [documentation around `wkg.toml`][wkg-toml-docs],
which allow you to set overrides that control `wkg` and where it searches for packages (e.g. `resuable:logging`).

For example, a minimal correct `components/a/wkg.toml` should look something like this:

```
[overrides]
"reusable:logging" = { path = "../../wit/resuable" }
```

Adding that file and using `wkg` is left as an exercise for the reader.

After you have `wkg.toml` in place, from the folder you should be able to run `wkg wit fetch`,
and see `wit/deps` populated correctly.

[wkg]: https://github.com/bytecodealliance/wasm-pkg-tools
[wkg-toml-docs]: https://github.com/bytecodealliance/wasm-pkg-tools/tree/main?tab=readme-ov-file#wkgtoml-and-wkglock
