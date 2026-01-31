# Tibanne Thecat: The Mt. Gox Heist - Game Reference

## Overview
A top-down stealth game where you play as Tibanne, Mark Karpeles' cat, sneaking through the Mt. Gox offices at night to "hack" (sit on) keyboards and accidentally send 850,000 BTC to random addresses. Based on the r/Buttcoin meme that Tibanne was the real hacker behind the Mt. Gox collapse.

**Tech Stack:** Single-file HTML5 Canvas game, vanilla JavaScript, Web Audio API. Uses external PNG assets for title screen and background decoration.

**$CHIEFPUSSY:** Contract address `DRtvTCzfiKGhCVREmBbZdN9sB8PHeq9KdRZ3VmFhpump` — live market cap displayed via DexScreener API.

**Created By:** [@thejpegjunkie](https://x.com/thejpegjunkie)

---

## Lore

- **Mt. Gox** was the world's largest Bitcoin exchange (2010-2014), handling ~70% of all BTC transactions
- **Mark Karpeles** was the French CEO who ran it from Tokyo
- **Tibanne** was Mark's orange/white calico cat, after whom the parent company (Tibanne Co., Ltd.) was named
- The exchange collapsed in Feb 2014 after ~850,000 BTC went missing
- r/Buttcoin joked that Tibanne sitting on keyboards was the real cause of the "hack"
- The cat was jokingly called "Chief Pussy Officer"

---

## Game Mechanics

### Controls

#### Desktop
- **WASD / Arrow Keys:** 8-directional movement
- **Shift (hold):** Sneak mode (slow, quiet, harder to detect)
- **Space (hold):** Sprint mode (fast, loud, easy to detect)
- **E:** Interact (hack keyboard, hide under desk, knock shelf, toggle fuse box)

#### Mobile (Touch)
- **Virtual Joystick (left):** 8-directional movement with dead zone
- **SNEAK button (hold):** Sneak mode
- **RUN button (hold):** Sprint mode
- **E button (tap):** Interact

### Movement Speeds
| Mode    | Speed (px/s) | Noise Radius (tiles) | Detection Rate |
|---------|-------------|----------------------|----------------|
| Sneak   | 55          | 1                    | 35/sec (slow)  |
| Walk    | 100         | 2.5                  | 60/sec         |
| Sprint  | 185         | 4                    | 90/sec (fast)  |

### Stealth System

#### Vision Cones
- Guards and cameras have vision cones rendered as semi-transparent polygons
- Cones are raycasted against the tile grid (20 rays per cone)
- Walls block line of sight
- Color coding:
  - **Yellow:** Idle (not detecting)
  - **Orange:** Suspicious
  - **Red:** Alert / Chasing
  - **Blue:** Camera (normal)
  - **Red-orange:** Camera (detecting)

#### Detection Meter
- Fills when cat is inside a vision cone (not hidden)
- Fill rate depends on cat movement mode (sneak = slower fill)
- Drains at 25/sec when cat leaves cone
- At 25%: Guard becomes SUSPICIOUS
- At 100%: Guard becomes ALERT
- Camera at 100%: All guards immediately ALERT

#### Noise System
- Movement generates noise events with a tile radius
- Noise events appear as expanding yellow rings
- Guards within hearing range react to noise
- Noise does NOT go through walls (raycast check)
- Hacking generates noise every 0.5 seconds (radius: 3 tiles)
- Knocking shelves generates loud noise (radius: 6 tiles)

### Guard AI States

```
IDLE → SUSPICIOUS → ALERT → SEARCHING → IDLE
```

| State      | Behavior | Speed | Icon |
|------------|----------|-------|------|
| IDLE       | Follows patrol waypoints, waits at each | 1x | None |
| SUSPICIOUS | Moves toward last noise/sighting at 0.7x speed, investigates 3s | 0.7x | ? |
| ALERT      | Chases cat at 1.6x speed, tracks last known position | 1.6x | ! (blinking) |
| SEARCHING  | Stays at last known position, looks around for 4s | 0x | ? |

#### Guard Patrol
- Each guard has a list of waypoints with wait times
- Guards face toward the next waypoint while waiting
- Waypoints are defined in grid coordinates and converted to pixels

### Cat Abilities

#### Hiding (E near desk)
- Cat becomes invisible to all guards and cameras
- Cat cannot move while hidden
- Press E again to unhide
- Visual: Cat drawn at 35% opacity under semi-transparent desk top

#### Distraction (E near shelf)
- Knocks items off shelf, creating loud noise (6 tile radius)
- Lures guards toward the shelf location
- Each shelf can only be knocked once
- Visual: Shelf changes to scattered items on floor

#### Hacking (E near keyboard objective)
- Enters HACKING state with terminal overlay UI
- Progress bar fills over ~3.3 seconds
- Generates noise while hacking (radius 3 tiles every 0.5s)
- On completion: BTC amount added to total, keyboard marked as done
- Guards/cameras continue updating during hacking (you can be caught!)

#### Fuse Box (E near fuse box, Level 4+)
- Disables all cameras for 8 seconds
- Each fuse box is single-use
- Timer displayed in top-right corner

### Win/Lose Conditions

#### Level Complete
1. Hack all keyboard objectives in the level
2. Reach the EXIT tile (glows orange when all keyboards hacked)

#### Caught
- Guard reaches within 0.8 tiles of cat while in ALERT state
- Lose 1 life, restart current level
- 0 lives = Game Over

#### Lives
- Start with 9 lives (cats have 9 lives)
- Persist across levels

---

## Levels

### Level 1: "First Paws" (2011)
- **Guards:** 1 (slow, wide patrol)
- **Cameras:** 0
- **Keyboards:** 2 (15,000 + 10,000 BTC)
- **Shelves:** 1
- **Total BTC:** 25,000
- **New Mechanic:** Tutorial — basic movement, sneaking, hacking

### Level 2: "The Withdrawal" (2012)
- **Guards:** 2
- **Cameras:** 1
- **Keyboards:** 3 (30K + 30K + 40K BTC)
- **Shelves:** 2
- **Total BTC:** 100,000
- **New Mechanic:** Cameras, shelf distractions

### Level 3: "Transaction Malleability" (2013)
- **Guards:** 3
- **Cameras:** 2
- **Keyboards:** 4 (60K + 70K + 30K + 40K BTC)
- **Shelves:** 2
- **Total BTC:** 200,000
- **New Mechanic:** Vents (passable shortcuts through walls)

### Level 4: "Goxed" (2013 late)
- **Guards:** 3 + 1 fast guard (2x speed)
- **Cameras:** 3
- **Keyboards:** 4 (70K + 80K + 70K + 80K BTC)
- **Shelves:** 1
- **Fuse Boxes:** 1
- **Total BTC:** 300,000
- **New Mechanic:** Fuse box (disables cameras for 8s)

### Level 5: "The Final Meow" (2014)
- **Guards:** 4 (tighter patrols, faster)
- **Cameras:** 4
- **Keyboards:** 4 (50K + 55K + 60K + 60K BTC)
- **Shelves:** 2
- **Total BTC:** 225,000
- **Final Challenge:** Largest map, most guards + cameras

### Cumulative Total: 850,000 BTC

---

## Tile System

### Grid
- **Tile Size:** 32x32 pixels
- **Grid:** 25 wide x 18 tall (800x576 play area)
- **HUD Offset:** 24px from top

### Tile Types

| Char | Type     | Blocking | Description |
|------|----------|----------|-------------|
| `.`  | FLOOR    | No       | Walkable office carpet |
| `W`  | WALL     | Yes      | Impassable wall |
| `D`  | DESK     | Yes      | Furniture (can hide under) |
| `K`  | KEYBOARD | No       | Hackable objective (glows green) |
| `S`  | SERVER   | Yes      | Server rack with blinking LEDs |
| `H`  | SHELF    | No       | Knockable distraction |
| `C`  | CHAIR    | No       | Office chair (decorative) |
| `V`  | VENT     | No       | Air vent (passable shortcut) |
| `O`  | DOOR     | No       | Doorway |
| `X`  | EXIT     | No       | Level exit (glows when objectives complete) |
| `F`  | FUSE     | No       | Fuse box (disables cameras) |

### Level Layout Format
Levels are defined as arrays of 18 strings, each 25 characters wide:
```javascript
layout: [
    "WWWWWWWWWWWWWWWWWWWWWWWWW",
    "W.......W.............WW",
    // ... 16 more rows
    "WWWWWWWWWWWWWWWWWWWWWWWWW",
]
```

---

## Render Order (Back to Front)

1. **Tiles** — Floor, walls, furniture bases, keyboards, servers, shelves, vents, doors, exit
2. **Noise Rings** — Expanding yellow circles from movement/actions
3. **Camera Vision Cones** — Semi-transparent blue/red polygons
4. **Guard Vision Cones** — Semi-transparent yellow/orange/red polygons
5. **Guards** — Navy uniform figures with flashlights
6. **Cat (Tibanne)** — Orange/white calico cat
7. **Desk Tops** — Second-pass furniture rendering (covers hiding cat)
8. **Lighting Overlay** — Darkness layer with cutouts around light sources
9. **Interact Prompts** — `[E] HACK`, `[E] HIDE`, `[E] KNOCK`, `[E] FUSE`
10. **Camera Blackout Timer** — "CAMS OFF: Xs" text
11. **Objective Tracker** — "Keyboards: X/Y" text (bold 12px, 85% white)

### Two-Pass Furniture Rendering
Desks are drawn in two passes to create a depth illusion:
- **Pass 1 (with tiles):** Desk base/shadow drawn before entities
- **Pass 2 (after entities):** Desk top surface drawn over entities
- When cat hides under a desk, the top pass renders at 60% opacity

---

## Art Style

All game art is procedurally drawn with Canvas 2D primitives — no image files for gameplay.

External PNG assets:
- `titleimage.png` — Title screen image
- `tibanneback.png` — Cat facing away (background decoration)
- `tibannefront.png` — Cat facing forward (background decoration)

### Tibanne (Cat)
- Orange/white calico cat drawn from top-down perspective
- Body: Orange ellipse with white belly patch and darker orange patches
- Triangular ears with pink inner ears
- Green eyes with black pupils
- Pink nose, white whiskers
- Animated tail wag (sine wave)
- Sneaking: Flattened body (smaller ellipse)
- Sprinting: White motion lines behind
- Hiding: 35% opacity

### Guards
- Top-down security guard figures
- Dark navy uniform (rectangle body)
- Peach-colored circular head with navy cap
- Yellow flashlight in hand
- Alert indicators: `?` (yellow) or `!` (red, blinking)
- Detection meter bar above head

### Background Cat Decoration
- 24 cat images placed on a 6x4 grid across the browser window behind the game
- Alternating `tibanneback.png` and `tibannefront.png` in checkerboard pattern
- Random jitter within each grid cell to avoid rigid placement
- Sizes: 60–120px, opacity: 0.25–0.40, slight random rotation
- Filter: 10% grayscale

### Environment Colors
```
Body BG:     #121220 (dark blue-black)
Floor:       #222238 (dark blue-gray)
Floor Grid:  #2c2c52
Wall:        #3a3a5e (with #4d4d7e bevel)
Desk:        #5a4530 (dark brown)
Desk Top:    #6c5540 (lighter brown)
Keyboard:    #eee (with green glow for objectives)
Server:      #243848 (with colored LED dots)
Shelf:       #4a3518 (with colored book spines)
Exit:        #f7931a (Bitcoin orange, pulsing when active)
```

---

## Audio (Web Audio API)

All sounds are synthesized — no audio files.

### Sound Effects

| Sound | Frequency | Type | Duration | Volume | Trigger |
|-------|-----------|------|----------|--------|---------|
| Sneak Step | 60Hz | sine | 0.04s | 0.04 | Walking while sneaking |
| Walk Step | 90Hz | sine | 0.06s | 0.08 | Normal walking |
| Sprint Step | 120Hz | triangle | 0.08s | 0.12 | Sprinting |
| Suspicious | 300→450Hz | sine | 0.15s | 0.12 | Guard becomes suspicious |
| Detected | 400→800→1200Hz | square | 0.25s | 0.2 | Guard enters ALERT |
| Hack Typing | 800-1400Hz | square | 5x0.03s | 0.06 | During keyboard hacking |
| BTC Send | C5→E5→G5→C6 | sine | 0.5s | 0.2 | Hack complete, BTC stolen |
| Knock | 200→80Hz | sawtooth | 0.25s | 0.15 | Knocking shelf |
| Meow | 600→200Hz | sine | 0.2s | 0.15 | (Available) |
| Caught | 400→100→50Hz | sawtooth | 0.7s | 0.25 | Guard catches cat |

### Music System

#### Stealth Music
- **BPM:** 70
- **Key:** C minor
- **Instruments:** Triangle melody + sine sub-bass
- **Melody:** Eb4, D4, C4, G3, Ab3, G3, Eb4, D4
- **Bass:** C3, C3, Ab3, G3 (half notes)
- **Volume:** 0.35 gain
- **Loop:** 8-beat bars via setInterval

#### Alert Music
- **BPM:** 140
- **Key:** A minor
- **Instruments:** Square melody + sawtooth pulsing bass
- **Melody:** A4, C5, A4, E5, D5, C5, A4, G4
- **Bass:** C3 (sub-bass) pulsing on every beat
- **Volume:** 0.3 gain
- **Transitions:** Crossfades when alert state changes

---

## UI Elements

### DexScreener Market Cap Display
- **API:** `https://api.dexscreener.com/latest/dex/tokens/{CA}`
- **Contract:** `DRtvTCzfiKGhCVREmBbZdN9sB8PHeq9KdRZ3VmFhpump`
- **Refresh:** Every 30 seconds
- **Format:** `$412.76K` / `$1.23M` / `$1.23B` (2 decimal places with suffix)
- **Desktop:** Large bar (max 400px wide, 14–22px font) above game container
- **Mobile:** Stacked panel at top-right: `$CHIEFPUSSY` label + `MC $X.XXM` value

### Title Screen
- `titleimage.png` displayed as main title image
- Game title: "TIBANNE THECAT"
- Subtitle: "The Mt. Gox Heist"
- Lore text, control info, and start button

### HUD Bar (Top) — Desktop
- **Left:** BTC stolen counter (Bitcoin orange)
- **Center:** Level name + year
- **Right:** Lives remaining
- **Style:** Semi-transparent black, Courier New monospace
- Hidden on mobile (replaced by mobile HUD panels)

### Mobile HUD Panels
- **Top-left panel** (`#mobileHudLeft`): BTC stolen, level/year, lives, BUY $CHIEFPUSSY link
- **Top-right panel** (`#mobileHudRight`): $CHIEFPUSSY label, market cap value, @thejpegjunkie link
- Fixed position, semi-transparent black background, rounded corners

### Footer Bar — Desktop Only
- **Left:** "BUY $CHIEFPUSSY" link → pump.fun
- **Right:** "Created By: @thejpegjunkie" link → x.com
- Hidden on all touch/mobile devices

### Alert Bar
- **Position:** Below HUD, centered
- **Size:** 120x4px
- **Behavior:** Appears when any guard/camera is detecting
- **Colors:** Green → Yellow → Orange → Red (based on detection %)

### Objective Tracker
- **Position:** Below HUD bar on canvas (y = HUD_H + 30)
- **Style:** Bold 12px monospace, 85% white opacity
- **Content:** "Keyboards: X/Y" — tracks hacked vs total objectives

### Hack Overlay
- **Style:** Green-on-black terminal aesthetic
- **Content:** Progress bar, random BTC address, amount, flavor text
- **Flavor texts:**
  - "Tibanne is just sitting there"
  - "The cat appears to be napping on the keyboard"
  - "Purring intensifies"
  - "Cat butt on Enter key detected"
  - "Tibanne kneads the spacebar aggressively"
  - "This is good for Bitcoin"
  - "SFYL (Sorry For Your Loss)"

### Level Complete Screen
- r/Buttcoin-style post with subreddit header
- Dynamic title/body per level
- BTC stolen stats
- Continue button

### Game Over Screen
- "CAUGHT!" header in red
- Total BTC stolen
- Try Again button

### Game Complete Screen
- "HEIST COMPLETE" header
- Total: 850,000 BTC
- Dollar value at "today's price"
- "Tibanne did nothing wrong" quote
- Play Again button

### Dialog Box
- **Position:** Bottom-center of game area
- **Style:** Black with Bitcoin orange border
- **Usage:** Start-of-level flavor text, interaction feedback
- **Auto-dismiss:** After 2-4 seconds

### Mute Button
- **Position:** Bottom-right corner
- **Style:** Circular, Bitcoin orange border

### Landscape Hint (Mobile)
- Full-screen overlay shown in portrait on small screens
- Prompts user to rotate to landscape
- "Tibanne needs room to sneak"

---

## Collision System

### Tile-Based Collision
- Blocking tiles: WALL, DESK, SERVER
- All other tiles are walkable
- Cat hitbox: 20x20px (centered on position)
- Collision checks 4 corners of hitbox independently for X and Y axes
- Allows sliding along walls (X and Y tested separately)

### Vision Raycasting
- Step size: TILE/4 (8px) for ray marching
- Step size: TILE/3 (~10.7px) for line-of-sight checks
- Checks grid cell at each step for wall collision

---

## Game State Machine

```
MENU → PLAYING ↔ HACKING
         ↓           ↓
      CAUGHT    (guards still update)
         ↓
    GAME_OVER   or   LEVEL_COMPLETE → PLAYING (next level)
                                         ↓
                                   GAME_COMPLETE
```

### States
| State | Input | Rendering | Guards Active |
|-------|-------|-----------|---------------|
| MENU | Start button only | Dark background | No |
| PLAYING | Full movement + interact | Full game | Yes |
| HACKING | None (auto-progress) | Full game + overlay | Yes |
| LEVEL_COMPLETE | Continue button | Reddit post screen | No |
| GAME_OVER | Retry button | Game over screen | No |
| GAME_COMPLETE | Replay button | Completion screen | No |

---

## File Structure

```
tibanne-mtgox-heist/
├── index.html                    # Complete game (HTML + CSS + JS)
├── titleimage.png                # Title screen image
├── tibanneback.png               # Cat back view (background decoration)
├── tibannefront.png              # Cat front view (background decoration)
└── TIBANNE-GAME-REFERENCE.md     # This file
```

---

## Responsive Design

### Desktop
- Canvas: 800x600 pixels
- Centered with dark background (#121220)
- Market cap bar above game (max 400px wide)
- Footer bar below game with buy/credit links
- Box shadow glow around game container

### Mobile (Touch Devices)
- Game canvas fills full viewport height (`100dvh`) in landscape
- Width maintains 800/600 aspect ratio
- Desktop HUD, market cap bar, and footer hidden
- Mobile HUD panels fixed to top-left and top-right corners
- Touch controls fixed to bottom (joystick left, buttons right)
- Gradient overlay on touch control area for visibility
- Landscape hint overlay shown in portrait orientation
- Safe area insets respected for notch devices

### Font Scaling
- HUD: `clamp(10px, 2.5vw, 16px)`
- Buttons/titles: Various `clamp()` ranges
- All text uses `Courier New` monospace

---

## Touch Controls (Mobile)

### Virtual Joystick
- **Position:** Fixed bottom-left
- **Size:** 140x140px zone, 130px base circle, 50px draggable knob
- **Dead zone:** 8px (prevents accidental movement)
- **Max distance:** 40px from center
- **Input mapping:** Sets `keys['KeyW/A/S/D']` directly into the existing input system
- **Multi-touch aware:** Tracked by touch identifier

### Action Buttons
- **Position:** Fixed bottom-right
- **E button:** 70x70px, green border, tap to interact (maps to `interactPressed`)
- **SNEAK button:** 58x58px, hold to sneak (maps to `keys['ShiftLeft']`)
- **RUN button:** 58x58px, hold to sprint (maps to `keys['Space']`)
- **Visual feedback:** `.active` class adds brighter background on press

### Touch Event Handling
- All touch events use `{ passive: false }` and `preventDefault()` to avoid scroll/zoom
- `touchcancel` events properly clean up state
- Touch controls inject into the same `keys` object used by keyboard input — no game logic changes needed

---

## Technical Details

### Game Loop
- Uses `requestAnimationFrame` with delta time
- Delta time capped at 0.05s (prevents physics explosion on tab switch)
- Update and draw are separate functions

### DexScreener Integration
- Fetches `pairs[0].marketCap` from token endpoint
- Formats with `fmtMC()`: K/M/B suffixes with 2 decimal places
- Updates both desktop (`#mcValue`) and mobile (`#mcValueM`) elements
- Graceful fallback to "MC N/A" on error

### Performance
- 20 rays per vision cone × ~8 entities = ~160 raycasts/frame
- Tile grid collision: O(1) per check
- Noise events cleaned up after 1.5 seconds

### Key Constants
```javascript
TILE = 32          // Tile size in pixels
GRID_W = 25        // Grid width
GRID_H = 18        // Grid height
HUD_H = 24         // HUD offset from top
CANVAS_W = 800     // Canvas width
CANVAS_H = 600     // Canvas height
```

---

## Future Enhancements (Ideas)

- [x] ~~Touch controls (virtual joystick + action button for mobile)~~
- [ ] Mini-map in corner
- [ ] Screen shake on detection
- [ ] Particle effects (BTC flying out of computers)
- [ ] Easter eggs (Willy bot reference, "Where is the Bitcoin?")
- [ ] Share button (Twitter/X with stats)
- [ ] High score persistence (localStorage)
- [ ] Sprite-based art (replace procedural drawing with actual cat sprites)
- [ ] Additional levels (post-bankruptcy, courtroom, etc.)
- [ ] Guard pathfinding (A* for ALERT chase, currently direct line)
- [ ] Sound settings (separate music/SFX volume)
- [ ] Tutorial overlay with animated instructions
- [ ] Cat abilities: purring (slow nearby guard detection), hairball (temporary wall)
