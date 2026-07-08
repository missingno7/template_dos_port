# template_dos_port — entry point for a new DOS game source port

This is where a new oracle-driven DOS game recovery starts. It packages the
**porting methodology** — the operational boot sequence, the method, the
pitfalls already paid for, the task rituals, and the adapter template — around
the [`dos_re`](https://github.com/missingno7/dos_re) framework, which is
wired in here as a git submodule at `dos_re/`.

**Start at [`START_HERE.md`](START_HERE.md).** It is the boot sequence for an
AI agent (or human) handed this repo plus a game to port — everything else is
reachable from there.

## What lives here vs in the `dos_re` submodule

| Here (`template_dos_port`) | `dos_re/` submodule |
|---|---|
| The method: boot sequence, lifecycle, charter, pitfalls, cookbook, the porting checklist (`docs/`) | The framework's own reference docs: architecture, hooks/verification, demos/snapshots, state mirrors, hardware support (`dos_re/docs/`) |
| Task rituals for recurring work (`prompts/`) | The 8086/DOS VM, proof engines, and framework tests (`dos_re/dos_re/`, `dos_re/tests/`) |
| The adapter template you copy to start a game (`examples/adapter_skeleton/`) | Game-free runnable demos of the framework itself (`dos_re/examples/`) |
| `MIGRATION.md` — provenance ledger for the whole ecosystem's split | `dos_re/AGENTS.md` — contribution rules for the framework itself |
| Your game adapter (e.g. `mygame/`), `assets/` (gitignored), `tests/` — created as you port | `dos_re/nuked_opl3/` — its own submodule (Nuked-OPL3 FM synth) |

## Setup

```bash
git clone --recurse-submodules <this repo>
cd template_dos_port
python dos_re/examples/tiny_frame_game/walkthrough.py   # confirms the framework works here
python -m pytest -q                                       # framework suite (+ yours, once you add tests/)
```

If you already cloned without `--recurse-submodules`:
`git submodule update --init --recursive`.

No game code, assets, or executables are included or ever committed here
(`assets/` is gitignored) — bring your own legally owned copy of the game
you're porting.

## License

MIT ([LICENSE](LICENSE)) for the methodology in this repo. The `dos_re/`
submodule and its own `nuked_opl3/` submodule carry their own licenses (MIT,
and LGPL-2.1-or-later respectively) — see [LICENSE](LICENSE) for details.
