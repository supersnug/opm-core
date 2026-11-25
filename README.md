# OPM Core

OPM Core is the Linux CLI, terminal UI, and background service source for
[OpenCode Plugin Manager](https://github.com/supersnug/opencode-plugin-manager).
The `core-2.4.1` branch is based on [OpenCode v2.4.1](https://github.com/anomalyco/opencode/tree/v2.4.1).

The planned `opm` executable will load patches at startup without modifying the installed `opencode` binary. This branch currently contains the upstream implementation trimmed for development; the OPM bootstrap and `opm install` are not implemented yet.

## Working on the core

Use the Bun version declared in [`package.json`](package.json). From the repository root:

```sh
bun install --frozen-lockfile
bun run check
```

`packages/cli` contains the CLI entry point, `packages/tui` the terminal interface, and `packages/server` and `packages/core` the local service and session logic. Run tests from the relevant package directory. See [`AGENTS.md`](AGENTS.md) for development and testing conventions.

OpenCode's upstream copyright and [MIT license](LICENSE) apply.
