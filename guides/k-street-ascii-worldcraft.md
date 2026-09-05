# K Street ASCII Worldcraft

**Status:** operational (load this before painting, generating terrain, or restyling HUD)  
**Proving ground:** SCORCHED MARS (2026-09)  
**Applies to:** SCORCHED MARS, **Titan’s Edge**, any K-St ASCII / constrained-glyph / planetary sim  
**Canonical copy:** `K-St-Games/kst_promptware` → `guides/k-street-ascii-worldcraft.md`  
**Game copy:** `K-St-Games/scorched-mars` → `docs/k-street-ascii-worldcraft.md`

This is what we learned the expensive way. Other agents: **read the Don’ts first.**

---

## 0. Don’ts (paid for in playtests)

| Don’t | Why it failed | Do instead |
|---|---|---|
| Sample Doom’s palette 1:1 | Muddy rust-on-rust, no hierarchy | Gold / teal / violet on rust-black. Doom *feel*, not Doom *swatches* |
| Gradient-blend dirt into sky | Terrain “leaks”; ridge unreadable | Solid `fillRect` under every dirt glyph. Sky is a separate pass |
| Soft gradient chrome | Buttons vanish after FIRE; dock “disappears” | Flat chips, gold edge on the *active* control, dock stays mounted |
| Extra lockup copy on OG cards | Brand check + “happy birthday” leftovers | Title only. No taglines unless the user asked |
| Unbounded ridge height | Mobile: tanks clipped, no pan/zoom | Clamp `h[x]` to `[7, rows-2]`; measure the battlefield to the dock |
| Click-only angle/power | Desktop felt broken vs mobile sliders | Sliders + press-and-hold + drag-to-aim on both |
| Licensed Doom marks (BFG, logo, named demons) | Legal + brand | Original hellspawn / pit fighters |

---

## 1. Color — locked system

Research used: complementary, analogous, triadic, 60-30-10. **Winner: complementary gold ↔ teal, with violet as the 10% enemy/signal, on a rust-black ground.**

### 60-30-10

- **60% ground** — rust-black sky `#070b12` / `#081018`, cocoa dirt `#8a3016` → `#c45622`
- **30% structure** — bone / parchment `#f3ead8` / `#f2d991` (type, tanks, shells)
- **10% signal** — gold `#e0b44b` (active, moon, FIRE), teal `#4a9e96` (wind, stars, “ok”), violet `#b45ac8` (foe), ember `#f35c2e` (boom / danger)

Gold is **money and aim**, not fill. If everything is gold, nothing is gold.

### Locked hex (SCORCHED MARS `PALETTE` + CSS tokens)

```
bg / sky          #070b12  #081018  #102030
horizon ember     #e07030
dirt              #3a140c  #8a3016  #c45622  #e8893a
gold              #e0b44b  #f2d991
teal              #4a9e96
violet (p2)       #b45ac8
bone / shell      #f3ead8
danger            #f35c2e
ok                #4a9e96
```

CSS twins live in `src/styles.css` (`--color-accent`, `--color-p1`, `--color-p2`, `--color-danger`). **Keep canvas `PALETTE` and CSS tokens in lockstep.** If you change one, change the other.

### Why not the other schemes

- **Analogous rust-only** — rich in stills, unreadable in motion. Sky and dirt collapsed.
- **Triadic RGB arcade** — too toy, fights the pit.
- **Direct Doom** — brown-on-brown, muddy, “chunky.” User rejected it twice.
- **Gradient styling** — user: “I don’t like the gradient color style.” Flat fills + one gold edge.

### Contrast rules

- Dirt glyph color must be **lighter than its `fillRect`**, never the same.
- Sky glyphs (stars, moon, dust) never use dirt hex.
- Active HUD chip: gold fill **or** 2px gold edge on rust-black. Inactive: muted `#c49a72` on `#101820`.
- Body text `#f3ead8` on `#070b12` or `#101820` only. Never bone-on-gold except the FIRE hero.

---

## 2. ASCII — how a cell is drawn

The battlefield is a **monospace cell grid**, not a sprite sheet.

1. Measure `cellW` / `cellH` from the canvas so the **whole field + dock** fits. No pan, no zoom. High terrain must still show both tanks.
2. **Sky pass** — vertical gradient is allowed *only* in empty sky. Stars / moon / dust as glyphs.
3. **Dirt pass** — for each column `x`, for each row `y >= surfaceY`:
   - `fillRect(cell, dirtFill)` first (this is the contrast)
   - then glyph `# % & H M W` (crest) / `:` `.` `` ` `` (deep) in a *lighter* dirt or gold fleck
4. **Actors** — tanks, shells, explosions on top. Tank shadow is a darker cell, not a blur.
5. **Alignment** — glyph origin = cell origin. If sky and dirt disagree by one row, it reads as a “misaligned background.” Re-measure; don’t offset the sky gradient independently.

Do **not** anti-alias glyphs into the sky. Do **not** alpha-blend dirt. Do **not** use bitmap fonts that hint differently per DPR — stick to IBM Plex Mono / a known mono and integer cell sizes.

### Glyph vocabulary (pit)

| Thing | Glyphs |
|---|---|
| Dirt crest | `# % & H M W` |
| Dirt deep | `: . \`` |
| Moon | `( O )` gold |
| Stars | `*` `.` teal / bone |
| Dust / clouds | `~` |
| Tank | block + barrel, bone / gold |
| Shell | `*` bone |
| Boom | `*` `#` ember |

---

## 3. HUD hierarchy (what must be obvious)

Rank, high → low:

1. **FIRE** — hero, gold, biggest hit target. Never hide after a shot.
2. **AIM / MOVE** segmented control — two states, gold when on.
3. **Weapon chip** — name + ammo (`INF` / count) + one-line blurb. Active = gold edge.
4. **Angle / power** — labeled sliders **and** steppers with press-and-hold. `X` / `Z` (or Q / E) cycle weapons; say so on the dock.
5. **HP bars** — thick, named (callsign vs fighter), pulse only when low.
6. **Dust needle** — wind. Teal. Small but always present while aiming.
7. **Mute / pause / help** — chrome, not the story.

Dock is `position: overlay / shrink-0` under the canvas, **not** inside the canvas bitmap. If the HUD is drawn on the canvas it will vanish during `firing`. That bug already shipped once.

Help overlay should include **mini visual examples** of AIM vs MOVE, weapon glyph, and dust.

---

## 4. Procedural terrain (1D proving ground)

SCORCHED MARS is a **1D heightfield** (`Float64Array` of length `cols`). `h[x]` is **y-down** (canvas): **larger = lower ground.**

### Pipeline (`src/game/terrain.ts`)

1. **Bedrock** — midpoint displacement. Roughness `0.46–0.54`. Connected ridges, not Perlin mush.
2. **One biome stamp** — dunes / craters / mesa / valles / shield.
3. **Extra impact bowls** with rims (every map).
4. **Regional tilt**.
5. **Mass wasting** — steep scarps slump to talus.
6. **Aeolian** — saltation hops, stoss abrasion, lag armor, lee dump.
7. **Yardangs, lee shadows, blowouts, repose slip, TAR ripples**.
8. **Smooth + clamp** to `[7, rows-2]` so tanks stay on screen.

Combat mutates the same array: `applyCrater`, `applyQuake`, `flattenPad`.

### Families (use the mix, not one)

| Family | Role here |
|---|---|
| Fractal | Bedrock |
| Stamps | Biomes, craters |
| Simulation | Erosion, avalanches, wind |
| Grammar | “A mesa, then a dune sea” — optional next |

Skip for side-view artillery: full hydraulic rivers, 3D voxels, ML terrain. Keep it seeded and deterministic.

### Playable-ridge law

If the highest dirt still clips a tank after clamp, **lower the whole field**, don’t flatten the character of the biome. Mobile cannot pan/zoom.

---

## 5. Titan’s Edge — how to use this

Titan’s Edge is New Macondo on Titan, Buendía family, Mars departure → ice moon. **Zero code in `K_St_Engineering` as of 2026-07** (incubator gap). This bible is the visual and worldcraft seed.

### Two palettes, one system

Same 60-30-10. **Shift the 60%, keep the 10% gold.**

**Mars chapter (departure, memory, SCORCHED MARS cousin)**  
Use the locked hex above. Rust hills, bone tanks, gold moon.

**Titan chapter (New Macondo)**  
Tholin orange ground, ice-cyan structure, gold still the 10% signal.

```
titan sky           #070b12  #120c08  #1a140c   (orange-black, not rust-red)
ice / methane       #3d6b66  #7ec8c0  #c8f0ea   (teal family — already in the system)
tholin dunes        #4a2010  #8a4a22  #c47a3a
gold signal         #e0b44b  (UNCHANGED — colony UI, Andromeda status, FIRE-equivalents)
bone type           #f3ead8
danger / ice-quake  #f35c2e
andromeda / foe     #b45ac8  (violet = other, A.I., the pit’s cousin)
```

Do **not** invent a third accent. Gold stays the only “this is the action” color across Mars and Titan so the player’s muscle memory survives the jump.

### Terrain on Titan

Titan has **real hydrocarbon dune seas** (Belet, Shangri-La). The aeolian stack is more true here than on Mars.

- Overworld: 2D heightfield is allowed (exploration). Side / weather slices: keep 1D + the same erosion functions.
- **Ice quakes** = `applyQuake` (wide sag, then smooth). Colony buildings take fall damage like tanks.
- **Kraken storms** = raise `uStar` in `aeolianErode`; lee shadows bury greenhouses.
- **Craters / wreckage of the first colony** = `stampImpact` with a *metal* rim glyph, not just dirt.
- Playable-ridge law still holds for any side-view setpiece (lander, trench, valles).

### ASCII / terminal layer (Andromeda)

Even if the overworld is painted, **ship computers, Andromeda, Melquíades radio, and colony status should stay glyph.** Same cell rules, same palette. That is the magical-realism seam: the world is lush; the *machine* is mono.

Andromeda’s voice UI: violet `#b45ac8` on rust-black, gold only when she is *armed* or *asking a decision*. Never rainbow LLM chrome.

### HUD / colony chrome

Reuse the SCORCHED MARS hierarchy: one hero action, one mode switch, one resource cluster, chrome that **does not unmount** during storms or quakes. If you draw weather on the canvas, keep the dock in the DOM.

### Tone

Tribute, not pastiche. No “happy birthday.” No Doom logo. Buendía names stay. Mars is the wound they left; Titan is the dune sea they inherit. Color does that work if you keep gold constant and move only the ground.

---

## 6. Where the pixels live

| Thing | Path |
|---|---|
| Palette + ASCII draw | `scorched-mars/src/game/render.ts` |
| CSS tokens / dock | `scorched-mars/src/styles.css`, `src/components/game/chrome.tsx` |
| Terrain pipeline | `scorched-mars/src/game/terrain.ts` |
| Weapons / quake | `scorched-mars/src/game/weapons.ts`, `engine.ts` |
| Live pit | https://cliff-brick-eagle-plum.grok.me/ |
| Source | https://github.com/K-St-Games/scorched-mars |
| Titan’s Edge brief | Drive folder `Titans Edge` — `Titan's Edge` Google Doc |
| This file (agents) | `kst_promptware/guides/k-street-ascii-worldcraft.md` |

When you restyle either game, change **this file first** if the rule is new, then the pixels.

---

## 7. Agent checklist

Before a visual or terrain PR:

- [ ] Dirt has a solid fill under the glyph
- [ ] Sky and dirt do not share a hex
- [ ] Gold is ≤10% of the frame
- [ ] Active control has a gold edge; FIRE never unmounts
- [ ] Heights clamped; both actors visible at spawn without pan
- [ ] No Doom marks, no extra OG taglines
- [ ] Titan chapter: gold unchanged, ground shifted to tholin/ice
- [ ] `PALETTE` and CSS tokens still match
