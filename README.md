# eider-command

A program's command line for [Eider](https://github.com/klxdev/eider-lang), derived from a record type (clap/cobra-style, with comptime reflection). It was `std/cli` in Eider 0.7 and earlier; from 0.7.3 it is this package (eider-lang #217).

- Each field of a record is a flag: `--name value`, `--name=value`, `-n value` (with a `short`), a bool is `--verbose` or `--no-verbose`, an `Option[T]` may be left out, a `List[T]` is repeated or comma-separated, anything else without a default is required. `--help` prints the generated usage.
- Subcommands are an enum whose variants each carry one record of flags; `command.subcommands` takes the first word as the variant (kebab case) and parses the rest against its record. `tool help serve` prints the usage of `serve`, like `tool serve --help`.
- Every problem is reported at once: an unknown flag, a missing value, a value that does not parse, a missing required flag.
- **Shell completions**: `command.completion[Serve]("tool", command.Bash)` (or `Zsh`, `Fish`) is a completion script for a record's flags, `command.subcommand_completion[Cmd](...)` one for subcommands (names with their help, `help <command>`, each command's flags). `command.parse_shell("zsh")` reads a shell name, so a `completions` subcommand is a few lines.
- Pure Eider; the only capability is `io` for `command.arguments()` and `command.report`.

```eider
import command
import std.io
import std.env

type Serve = {
    @[command(help = "address to listen on", default = ":8080")] addr: String,
    @[command(short = "w", help = "worker tasks", default = "4")] workers: i64,
    @[command(short = "v", help = "log every request")] verbose: bool,
}

fn main() uses io {
    let r: Result[command.Parsed[Serve], command.Error] = command.parse("serve", "run the server", command.arguments())
    match r {
        Ok(p) => run(p.opts, p.rest)
        Err(e) => env.exit(command.report(e))
    }
}
```

The module documentation (`//!` at the top of command.ei) has the details.

Differences from `std/cli`:

- the attribute is `@[command(name, short, help, default)]`, not `@[cli(...)]`: an attribute is found by the name of the package that declares it (`import command`, then `@[command(...)]`, like `@[json(...)]`);
- the function that parses an enum of subcommands is `command.subcommands` (`command.command` would be the package's name twice, and the attribute `command` takes that name).

## Using it

In your package's `eider.toml`:

```toml
[deps.command]
git = "https://github.com/klxdev/eider-command.git"
version = "0.7.0"
```

## Not done yet

- Nested subcommands (an enum inside an enum, `tool remote add`) and help for them.
- Man page generation.
- A reflective typed configuration record (defaults, TOML, environment, flags; it would depend on eider-toml).
- Completion of values (file names, a fixed set of choices), and `--flag value` suggestions for `Option` enums.

## Development

`sh scripts/test.sh` (it fetches the dependencies and runs every `tests/*.ei` against its `.out`); `sh scripts/version.sh` checks the version files. Tested with the installed toolchain of the same MAJOR.MINOR (`eider`).
