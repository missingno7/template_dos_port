# Post-endgame playbook — enhancements, hosts, and the taste loop

> **GATE: do not read this during recovery.** This playbook applies only after
> the flip — a complete faithful VM-less game with the tick-demo corpus green
> (`verify_ticks` on every recording). If you are here earlier, close this and
> go back to the checklist; pitfall #24 is how projects drown. The *rules*
> (sequencing, the read-only boundary, the parity gate) are
> [`enhancements.md`](enhancements.md) — this is the workflow and the
> technique catalog on top of them.

## The workflow changes here: agent-executed, human-steered

Recovery had one judge (the oracle) and needed almost no human input. The
post-endgame is different — **uncharted territory, per game**: which comforts
matter, what looks right, what a port *should* feel like on a modern screen.
The oracle still judges accuracy (the parity gate); the **human judges
taste**, and taste cannot be traced from the binary. So the loop becomes:

1. **Propose** — a short pitch per enhancement: what it does, the accuracy
   boundary, the cost. Let the human pick and order; do not build the whole
   catalog unprompted.
2. **Prototype behind a toggle** — faithful default OFF (or neutral),
   persisted in the settings file, switchable live from the in-game menu.
3. **Show, don't describe** — screenshots / short clips / before-after pairs
   (F10-menu toggling makes A/B trivial). Ask concrete taste questions:
   "mirror or black for the widescreen margins?", "4:3 or square pixels?".
4. **Iterate on feedback**, then run the parity gate (enhanced-at-neutral ≡
   faithful, pixel- and state-exact) and the full demo corpus before the
   commit. Ledgers stay current — this is still the loop protocol.

Expect much more human interaction per slice than recovery ever needed.
That is not a process failure; it is what "presentation" means.

## The two standing principles

**Everything is a toggle with a faithful default.** One settings dict,
persisted next to the game data, edited live through the in-game menu.
Enhanced-at-neutral must be provably identical to the faithful game — that is
the parity gate, and it is what lets the whole layer exist without risk.

**FPS first, interpolation second.** Interpolation is the *fallback*, not the
feature. The decision ladder for smoothness:

1. **Never raise the game tick.** The simulation rate is gameplay (RNG,
   physics, timers) — running it faster is a different game, not a smoother
   one.
2. **Re-render natively at display rate where presentation allows it.** If a
   motion is driven by presentation-side values the renderer derives per
   frame (camera pan, scroll position, palette ramps) and the renderer is a
   pure function of state, present MORE REAL FRAMES: sample the presentation
   parameters at display rate between ticks and re-render. No state mutation,
   no made-up frames.
3. **Only where motion is welded to simulation state**, interpolate
   presentation between two captured tick snapshots (the rolling two-snapshot
   window — pre2's `frame_capture.py`). Lerp presentation, never simulation;
   exclude UI/HUD elements that must step discretely.

State the choice per moving element in the port's enhancement notes: which
tier it uses and why.

## Technique catalog (worked examples: pre2_port, the completed port)

| Technique | The idea + the boundary | Worked example |
|---|---|---|
| **In-game settings menu (the "F10 menu")** | A modal overlay with a structural determinism firewall: while open, the game tick is FROZEN and every key routes to the menu (nothing reaches the game's input cells — demos can't be perturbed); items are data (`label/value/activate/adjust` closures over host settings); the module imports nothing from the game and never touches game state; the cheats tab exists only behind `--debug`. | `pre2_port/scripts/overlay_menu.py` (module docstring = the design contract) |
| **Frame interpolation** | Tier 3 of the ladder above: rolling two-snapshot window, lerp camera/pan/sprite presentation between ticks, present at display rate. | `pre2/bridge/frame_capture.py`, `play_native.py`'s `present_interpolated` |
| **Smooth transitions** | Replace discrete DAC-fade staircases with a continuous ramp that still converges to the exact final palette; byte-exact OFF path preserved. | `pre2/enhanced/transition_controller.py` |
| **Widescreen / true widescreen** | Decompose into safely-widenable layers vs untouchable simulation — the full lesson is in [`enhancements.md`](enhancements.md); never advance producers/spawners for the margins. | `pre2/enhanced/` compositor + extract |
| **Pixel aspect (4:3 vs square)** | Both are legitimate; user-selectable; verification compares pre-presentation. | `dos_re.display` `par` |
| **Stereo SFX** | Pan effects by on-screen position; event-exact, mixer-flexible — audio may never feed back into gameplay. | pre2 `stereo_sfx` setting |
| **Responsive-controls opt-ins** | Input-layer comforts (e.g. a jump buffer) that write only what a keyboard could — label EXPERIMENTAL, default OFF, document the exact key-table effect. | pre2 `responsive_controls` |
| **A new host (mobile/touch, web, …)** | A whole host layer is post-endgame work of the same shape: pure input resolvers + host glue over the unchanged core, every mapping writing only what the original input path writes. | pre2_port's Android port (`pre2/native/touch.py`, `scripts/android_host.py`) |

## What to ask the human (and what not to)

Ask: which enhancements to build and in what order; taste calls (aspect,
margin treatment, HUD placement, menu style); device/host targets; "does this
feel right?" on a build they can run. Don't ask: whether something is
*accurate* (the parity gate + corpus answer that), or how to implement it.

## Promotion path

These techniques live game-side today. Per the parameterize-and-promote rule,
when a **second** port reaches Stage 6: the overlay-menu widget (already
game-import-free by design) and the two-snapshot interpolation presenter are
the first candidates to move into `dos_re` — see `roadmap.md`.
