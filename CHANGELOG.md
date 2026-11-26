# Changelog

eider-command's versions (semver). Before 1.0 the MAJOR.MINOR is the Eider toolchain's it goes
with (`eider` in eider.toml), the PATCH its own.

## 0.7.1

For Eider 0.7.10. No library change. The tests skip their ThreadSanitizer runs when TSan cannot follow `longjmp` (glibc 2.4x), as they do for a missing runtime; the 0.7.0 section of this file is corrected.

## 0.7.0

First release, for Eider 0.7 (`eider = "0.7"`): `std/cli` of Eider 0.7.2 moved out of std (eider-lang #217).

- The API is std/cli's under the package name: `import command`, `command.parse`, `command.arguments`, `command.report`, `command.Value`, `command.Parsed`, `command.Error`.
- Changed: the attribute is `@[command(...)]` (it was `@[cli(...)]`), and `cli.command` is `command.subcommands`.
- Added: `command.completion[T]` and `command.subcommand_completion[C]` write bash, zsh and fish completion scripts (`command.Shell`, `command.parse_shell`); `tool help <command>` prints a subcommand's usage.
