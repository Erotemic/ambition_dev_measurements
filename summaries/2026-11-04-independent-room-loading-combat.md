# Independent room loading and combat — 2026-11-04

The main-game host keeps one participant active while the other waits for a
room. Both participant orders pass with no window and with offscreen wgpu.
Each case moves a body, emits a melee strike, deals one point of damage, keeps
the two rooms separate, and releases exactly one arrival reset after readiness.
Both GPU captures show the strike beside the other participant's loading cover.

Source: `dc303e611`. Renderer: llvmpipe (LLVM 20.1.2, 256 bits), Linux VM,
1280×720, Cargo test debug profile. These samples are correctness witnesses.
They do not establish release, Android, or Steam Deck performance budgets.

The fixture injects raw gamepad events. It does not measure physical device
latency. Motion takes two simulation steps; contact takes four and includes
the move's authored startup. The host clock advances by 1/60 s per update.
Wall times use `Instant` and include debug CPU and renderer work.

| Renderer | Loading slot | Motion wall ms | Contact wall ms | Preflight wall ms | Manifest wall ms | Commit call wall ms | Commit observed to retirement wall ms |
| --- | --- | --- | --- | --- | --- | --- | --- |
| offscreen-wgpu | 0 | 57.59 | 129.26 | 55.86 | 7.04 | 13.26 | 128.52 |
| no-window | 0 | 47.30 | 125.99 | 50.46 | 5.76 | 11.90 | 130.03 |
| offscreen-wgpu | 1 | 53.90 | 140.73 | 50.64 | 6.87 | 14.82 | 130.98 |
| no-window | 1 | 36.05 | 102.55 | 48.33 | 5.93 | 9.45 | 99.26 |

Preparation and presentation use the manual host clock below. The injected
hold includes movement, combat, and capture work before release. It is a
fixture delay, not asset-load latency. Asset readiness takes one host update;
the test barrier then keeps overall readiness pending. The observation of a
commit occurs at the end of its host update; its wall interval to retirement
is not an exact first-presented-frame timestamp.

| Renderer | Injected hold host ms | Request to ready host ms | Ready to commit host ms | Commit to retirement host ms |
| --- | --- | --- | --- | --- |
| no-window | 2366.67 | 2383.33 | 16.67 | 16.67 |
| offscreen-wgpu | 2416.67 | 2433.33 | 16.67 | 16.67 |

Reproduce from the superproject:

```sh
cargo test -p ambition_app --test app_it another_rooms_loading_does_not_block_gamepad_combat
cargo test -p ambition_app --test app_it another_rooms_loading_keeps_combat_visible -- --ignored --nocapture
```

Each test writes `ambition-loading-combat-p{0,1}-{no-window,gpu}.json` in the
system temporary directory. The GPU test also writes
`ambition-loading-combat-p{0,1}.png`. This run's raw files are kept locally in
`profiles/room-loading-combat-20261104/`; generated files remain ignored.
The delayed-peer arrival and replay witnesses remain separate gates.
