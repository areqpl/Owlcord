<div align="center">
  <h1>🦉 Owlcord</h1>
  <p><strong>A self-maintaining Vesktop fork with Wayland idle inhibition</strong></p>

  <p>
    <img src="https://img.shields.io/github/v/release/areqpl/Owlcord?label=release&style=flat-square&color=7c5cbf" />
    <img src="https://img.shields.io/github/actions/workflow/status/areqpl/Owlcord/sync-upstream.yml?label=upstream+sync&style=flat-square" />
    <img src="https://img.shields.io/github/actions/workflow/status/areqpl/Owlcord/wayland-health.yml?label=wayland+health&style=flat-square&color=3fb950" />
    <img src="https://img.shields.io/badge/platform-linux--wayland-blue?style=flat-square" />
  </p>
</div>

---

## What is Owlcord?

Owlcord is a personal fork of [Vencord/Vesktop](https://github.com/Vencord/Vesktop) that:

1. **Tracks upstream automatically** — a GitHub Action syncs `main` with `Vencord/Vesktop` every day at 04:00 UTC and reapplies the wayland patch on top.
2. **Adds Wayland idle inhibition** — cherry-picks the [`feat(waylandIdle)`](https://github.com/T1mbits/Vesktop/tree/wayland-idle) patch from [@T1mbits](https://github.com/T1mbits), which prevents your screen from dimming or locking mid-call on Wayland compositors.
3. **Is resilient to upstream changes** — if the patch is ever merged into Vesktop proper, the health-check workflow detects it and opens an issue to clean up. If T1mbits' fork disappears, Owlcord remains self-contained.
4. **Keeps dependencies fresh** — Dependabot opens weekly PRs for npm dependency updates (Electron major bumps excluded to protect the native wayland module).

---

## Features

| Feature | Status |
|---------|--------|
| [Vencord](https://github.com/Vendicated/Vencord) preinstalled | ✅ |
| Linux screenshare with sound & Wayland | ✅ |
| **Wayland idle inhibition** (no screen lock during calls) | ✅ Owlcord exclusive |
| Daily upstream sync | ✅ |
| Weekly dep updates (Dependabot) | ✅ |
| Weekly patch health checks | ✅ |
| Lightweight & privacy-respecting | ✅ (upstream feature) |

---

## Installation

### Arch / CachyOS (recommended)

Download the latest `.pacman` from [Releases](https://github.com/areqpl/Owlcord/releases) and install:

```bash
sudo pacman -U Owlcord-*.pacman
```

### Debian / Ubuntu

```bash
sudo dpkg -i Owlcord-*.deb
```

### Local auto-updating install (personal setup)

If you used the agent-managed install:

```bash
~/.local/bin/update-vesktop-wayland.sh
```

### Manual build from source

```bash
git clone https://github.com/areqpl/Owlcord.git
cd Owlcord
pnpm i
pnpm package:dir   # unpacked to dist/linux-unpacked/
# or
pnpm package --linux pacman
```

---

## Wayland Launch Flags

For best Wayland experience, launch with:

```bash
vesktop \
  --ozone-platform=wayland \
  --enable-features=WaylandWindowDecorations,UseOzonePlatform,WebRTCPipeWireCapturer \
  --enable-wayland-ime \
  --use-gl=egl
```

These are automatically applied by the agent-managed wrapper at `~/.local/bin/vesktop`.

---

## Automation Overview

```
                    ┌─────────────────────────────────────┐
                    │         GitHub Actions               │
                    │                                      │
  Vencord/Vesktop ──► sync-upstream.yml (daily 04:00 UTC) │
                    │   └─ FF-merge upstream/main          │
  T1mbits wayland ──►    └─ cherry-pick waylandIdle patch  │
                    │       └─ push → origin/main           │
                    │                                      │
                    │ build-release.yml (on push to main)  │
                    │   └─ pnpm package → .pacman + .deb  │
                    │       └─ GitHub Release              │
                    │                                      │
                    │ wayland-health.yml (weekly Mon)      │
                    │   └─ T1mbits fork alive?             │
                    │   └─ Patch merged upstream?          │
                    │   └─ Cherry-pick still clean?        │
                    │       └─ Open issue if problems       │
                    │                                      │
                    │ Dependabot (weekly Mon)              │
                    │   └─ npm updates → PRs               │
                    └─────────────────────────────────────┘
```

---

## Conflict handling

| Scenario | Behaviour |
|----------|-----------|
| Wayland patch already in upstream | Sync skips cherry-pick, health workflow opens cleanup issue |
| Cherry-pick conflicts on sync | Upstream base still pushed; issue opened for manual rebase |
| T1mbits fork goes offline | Owlcord is self-contained — no impact on builds |
| Electron major bump | Dependabot PR created but not auto-merged — requires manual validation of native wayland module |

---

## Credits

- [Vencord/Vesktop](https://github.com/Vencord/Vesktop) — upstream project by [@Vendicated](https://github.com/Vendicated)
- [T1mbits/Vesktop@wayland-idle](https://github.com/T1mbits/Vesktop/tree/wayland-idle) — Wayland idle inhibition patch by [@T1mbits](https://github.com/T1mbits)
- Owlcord maintained by [@areqpl](https://github.com/areqpl)

---

<div align="center">
  <sub>Owlcord is not affiliated with Discord Inc.</sub>
</div>