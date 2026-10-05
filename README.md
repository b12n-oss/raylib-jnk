# raylib-jnk

A guide to calling C and C++ from **[jank](https://jank-lang.org)**, a native
Clojure dialect that compiles to native code via C++/LLVM rather than the JVM,
written while porting the official [raylib](https://www.raylib.com/examples.html)
examples. It covers the rules that shape every jank/C++ interop pattern, from
native-value lifetimes to coercion, numeric performance and which raylib APIs
are reachable today.

The examples themselves live in
**[b12n-oss/raylib-jank-demo](https://github.com/b12n-oss/raylib-jank-demo)**: the ported example programs, the
helper namespaces, the vendored raylib assets, the demo GIFs and the build
tooling. This repo keeps only the documentation.

## Where things are

- The guide: [`docs/guide/index.md`](docs/guide/index.md), published as a site
  at <https://b12n-oss.github.io/raylib-jnk/>.
- Running an example: clone [raylib-jank-demo](https://github.com/b12n-oss/raylib-jank-demo) and follow its README.
- Adding or fixing an example: open the issue or PR in raylib-jank-demo.

## Known limitations

- **`rlgl-compute` does not run out of the box.** It needs OpenGL 4.3
  compute-shader support (`rlLoadShaderProgramCompute`, SSBOs,
  `rlComputeShaderDispatch`). raylib is built by the official `raylib-sys`
  package at OpenGL 3.3, which is what raylib's own Desktop default
  resolves to, and 3.3 is the practical ceiling on macOS anyway (Apple
  caps out at OpenGL 4.1; GLFW rejects a 4.3 context request
  unconditionally there, regardless of any compatibility hint). Running it
  on a platform where 4.3 is real would mean building raylib yourself with
  `OPENGL_VERSION "4.3"` instead of taking the published package. The
  example is in raylib-jank-demo.

## Documentation

The guide covers the native-value-lifetime rule that shapes every jank/C++
interop pattern, a C-interop toolbox, why a hot numeric loop wants `cpp/`
operators rather than ordinary jank arithmetic, raylib API coverage notes and
the porting workflow. Where a page cites an example, it links to the source in
raylib-jank-demo.

CI rebuilds the site on every pull request. Publishing from `main` is gated
by the workflow, see `.github/workflows/site.yml`.

## Credits

The overall `lein-jank`-based project layout the examples started from
originates from Kyle Cesare's
[`kylc/lein-jank-playground`](https://github.com/kylc/lein-jank-playground).
[raylib](https://www.raylib.com) itself is by Ramon Santamaria
([@raysan5](https://github.com/raysan5)) and contributors, under its own zlib
license.

## Changelog

[`CHANGELOG.md`](CHANGELOG.md) records what has changed since the repo went
public.

## Contributing

Corrections and additions to the guide are welcome. See
[`CONTRIBUTING.md`](CONTRIBUTING.md).

## License

[EPL 2.0](LICENSE), matching jolt and the rest of the fleet. It was zlib until
2026-09-05. EPL 2.0 relicenses nothing that arrived under another licence:
raylib and its examples remain zlib, and the ports stay derived works of them,
so those terms carry through. [`NOTICE`](NOTICE) records every attribution and
what was altered in each.
