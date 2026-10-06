# Getting started

The examples these notes are about live in
[raylib-jank-demo](https://github.com/b12n-oss/raylib-jank-demo). This page covers what you need to build and run them,
and the environment traps that are easy to hit. The repo's README is the
authority on its exact commands.

## Requirements

- Recent install of the [`jank`](https://jank-lang.org) compiler and the
  `lein-jank` Leiningen plugin (`2026.06-1` or newer; older versions lack
  the native-build middleware)
- A C++ compiler
- CMake
- [Babashka](https://babashka.org), for raylib-jank-demo's task runner

Verified on macOS with jank `0.1-alpha` and `lein-jank 2026.06-1`.

## Clone raylib-jank-demo with its submodule

`raylib` is vendored there as a git submodule for the example assets, so
clone with it:

```sh
git clone --recurse-submodules https://github.com/b12n-oss/raylib-jank-demo
# or, after a plain clone:
git submodule update --init --recursive
```

## Running an example

From the root of raylib-jank-demo:

```sh
bb doctor            # checks lein, a jank that starts, the asset submodule
bb info              # grouped cheat-sheet of every demo
bb starfield         # run one demo by name
bb run-all 4         # every demo for 4 seconds each
```

If the `lein` on your `PATH` can't bootstrap, set `LEIN=/path/to/lein`.

## Manual usage (without `bb`)

```sh
cd starfield && lein run --disable-sandbox
```

## macOS

There is no `bwrap` on macOS, so the native build must run with sandboxing
disabled. Every `bb`/`lein` invocation in raylib-jank-demo already passes
`--disable-sandbox` for you.

**Known limitation:** `rlgl-compute` needs OpenGL 4.3 compute-shader
support that macOS's native GL backend cannot provide (capped at 4.1). This
official `raylib-sys` package builds raylib at OpenGL 3.3, so every
other example works; `rlgl-compute` does not run out of the box on any
platform against this build. See the root README's "Known limitations"
section for the manual override recipe (which still won't work on macOS).

## Troubleshooting

### A handful of examples fail to compile, naming a raylib header you don't recognise

If a compile error cites a header outside the checkout (most often
`/opt/homebrew/include/raylib.h` or `/usr/local/include/raylib.h`), you have
a system-wide raylib installed that is shadowing the vendored one, and it is
an older version than the 6.0 raylib-jank-demo pins.

The tell is the compiler's own diagnostic pointing at the wrong file:

```
/opt/homebrew/include/raylib.h:1155:21: note: 'ComputeSHA1' declared here
```

...while compiling a call to `ComputeSHA256`, which only exists in 6.0.

**Fix:** unlink the system package for the duration of the build.

```sh
brew unlink raylib      # macOS; on Linux, remove or unlink the distro package
bb clean && bb basic-window
brew link raylib        # restore it afterwards if you want it back
```

**Why it happens, and why the project can't fix it from its build config:**
jank's compiler has at least two C++ resolution pathways. The main one
correctly honours the project's own `-I` flags (which the `raylib-sys`
package emits as `jank-build::include-dir=` directives pointing at its
raylib 6.0 headers). A secondary pathway, used for certain overload-resolution and
diagnostic scenarios rather than for every call, does not inherit those
flags and falls back to clang's default system include search, which finds
the Homebrew header instead. jank exposes no flag or environment variable to
control that second pathway's search order.

This is why the failure looks so arbitrary: only examples that call a
function which is new in 6.0 or changed signature since 5.5 can trip it. On
one machine it hit 8 of 209: `basic-shapes`, `top-down-lights` and
`shapes-textures-shader` (all call `DrawCircleGradient`, whose signature
changed), `math-sine-cosine` (`DrawLineDashed`), `compute-hash`
(`ComputeSHA256`), `strings-management`, `font-sdf` (`LoadFontData`), and
`point-rendering` (`rlDisablePointMode`). The other 201 compiled fine with
the same stray header present, which makes this very easy to misdiagnose as
a bug in one example.

Verified as a closed loop on jank `0.1-alpha` against Homebrew raylib 5.5:
unlink → all 8 compile clean; re-link → all 8 fail again, identically.
