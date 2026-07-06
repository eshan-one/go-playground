# Chapter 1 — Hello, World!

A hands-on exploration of the simplest Go program and the tools that come with it.

## The Program

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, world!")
}
```

Every Go source file starts with a `package` declaration. The `main` package is special — it tells the Go toolchain that this is an executable program rather than a library. The `main()` function is the entry point: execution begins here.

`fmt` is part of the standard library and provides formatted I/O functions. `fmt.Println` writes a line to stdout with a trailing newline. `fmt.Printf` gives printf-style formatting via verbs like `%s` (string) and `%d` (integer).

## Project Layout

| File | Purpose |
|------|---------|
| `hello.go` | Source code |
| `go.mod` | Module definition (module name + Go version) |
| `Makefile` | `fmt` → `vet` → `build` pipeline |

## Running the Code

```bash
# Using make (runs fmt + vet first, then builds)
make

# Or directly
go run hello.go

# Build a binary then run it
go build
./hello_world
```

---

## Wrap-Up: What We Learnt Today

### `go fmt` — opinionated, automatic formatting

Go ships with a canonical formatter. There is no debate about style; `go fmt` enforces one universal style across every Go project.

We ran several experiments to see exactly what it does:

**Experiment 1 — broken indentation and cramped braces**

We deliberately wrote this:
```go
func main(){
fmt.Printf("Hello, %s!\n",    "world")
    fmt.Println(   "This line has weird spacing"   )
}
```

After `go fmt`:
```go
func main() {
    fmt.Printf("Hello, %s!\n", "world")
    fmt.Println("This line has weird spacing")
}
```

`go fmt` fixed all of it automatically:
- Added the required space between `main()` and `{`
- Replaced all indentation with a single tab per level (Go uses tabs, not spaces)
- Stripped extra spaces inside function call arguments

**Experiment 2 — blank lines inside a function**

We added single blank lines and double blank lines between statements:

```go
func main() {
    fmt.Println("Hello, world!")

    fmt.Println("Single blank line above")


    fmt.Println("Two blank lines above")
}
```

After `go fmt`:
- **Single blank lines are preserved** — they're a legitimate way to group related statements visually
- **Double (or more) blank lines are collapsed to one** — `go fmt` won't allow more than one consecutive blank line

**Experiment 3 — `\n` inside strings**

Embedding `\n` escape sequences inside string literals is valid and entirely untouched by `go fmt`. The formatter only cares about whitespace in the source code structure, not inside string values.

### Key takeaways

| Behaviour | What `go fmt` does |
|-----------|-------------------|
| Missing space before `{` | Fixes it |
| Wrong indentation (spaces instead of tabs, wrong depth) | Fixes it |
| Extra spaces inside function calls | Removes them |
| Single blank lines between statements | **Leaves them alone** |
| Multiple consecutive blank lines | Collapses to one |
| Content inside strings | **Never touched** |
| Compileability | Never broken — `go fmt` only reformats, never changes logic |

### `go build` — always compiles regardless of formatting

Even with badly formatted source, `go build` compiles the code. Formatting is a human-readability concern, not a compiler concern. `go fmt` is a separate tool that you run before (or as part of) the build step.

The `Makefile` in this project chains them together so formatting and vetting always happen before a build:

```
make  →  go fmt  →  go vet  →  go build
```

### `fmt.Println` vs `fmt.Printf`

| Function | Use for |
|----------|---------|
| `fmt.Println(...)` | Simple output, adds a newline automatically |
| `fmt.Printf(format, args...)` | Formatted output using verbs (`%s`, `%d`, `%f`, etc.) |

`fmt.Printf` does **not** add a newline automatically — you must include `\n` in the format string yourself.
