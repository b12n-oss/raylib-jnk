# Contributing

Thanks for taking an interest. This repo is the documentation for porting
[raylib](https://github.com/raysan5/raylib) examples to
[jank](https://jank-lang.org), a native Clojure dialect that compiles to native
code through C++/LLVM. The guide lives in [`docs/guide/`](docs/guide/index.md)
and is published as a site.

The example programs, their helper namespaces, the vendored raylib assets and
the demo GIFs moved to
[b12n-oss/raylib-jank-demo](https://github.com/b12n-oss/raylib-jank-demo). Anything about running, adding,
fixing or recording an example belongs there, including the setup notes
(`jank`, `lein-jank`, a C++ compiler, CMake), the headless smoke test and the
conventions for new ports. Please open those issues and pull requests in that
repo.

## Changing the guide

Edit the Markdown under `docs/guide/`. Keep these habits:

- Link to an example's source in raylib-jank-demo rather than copying large
  stretches of it into a page, for instance
  `https://github.com/b12n-oss/raylib-jank-demo/blob/main/basic-lighting/src/net/b12n/raylib_jnk/scenes/basic_lighting.jank`.
- Verify a claim about jank behavior against a real run before you write it.
  The guide records measured results, and a recalled number tends to be wrong.
- If your change is user-facing, such as a new page or a corrected claim, add a
  line to [`CHANGELOG.md`](CHANGELOG.md) under *Unreleased* in the same commit.
  This project does not do separate doc-sync commits.

To preview the site locally, build it from a
[docs-engine](https://github.com/jlt-commons/docs-engine) checkout:

```sh
cd ~/dev/jlt-commons/docs-engine && jolt run build /path/to/raylib-jnk
```

CI builds the site on every pull request, so a broken link or page fails there
too.

## Licensing

This project is released under the Eclipse Public License 2.0. See
[`LICENSE`](LICENSE). By contributing, you agree your contribution is licensed
under those terms. Third-party notices are in [`NOTICE`](NOTICE).
