# Be The GM v2 - Difficulty Levels Plan

## Current State (v1)
Single difficulty: pick 8+ players, 1+ star, 50+ quality, stay under $136M cap. Generous constraints mean many valid solutions. Contextual hints guide every step. One hardcoded claim code for everyone.

---

## V2 Vision: Three Difficulty Tiers

The landing page gets a **difficulty selector** after the rules. All three levels use the same 16-player pool and the same core mechanic (click to draft), but the constraints tighten and new rules layer on as difficulty increases.

---

### LEVEL 1 — ROOKIE GM (current game, essentially unchanged)
> *For students still building confidence with addition and budgeting.*

| Constraint | Value |
|---|---|
| Salary cap | $136M |
| Min players | 8 |
| Min stars | 1 |
| Quality target | 50 |
| Position requirement | None |
| Hints | Full contextual hints (same as v1) |
| Claim code | `L1-101-M1-67` (unchanged) |

**What changes from v1:** Nothing gameplay-wise — this preserves the accessible entry point.

---

### LEVEL 2 — ALL-STAR GM
> *For students who breezed through Level 1 and want a real puzzle.*

| Constraint | Value |
|---|---|
| Salary cap | **$120M** (tighter — forces real trade-offs) |
| Min players | 8 |
| Min stars | **2** (must balance two expensive stars) |
| Quality target | **55** |
| Position requirement | **NEW: Must have at least 1 Guard, 1 Forward, and 1 Center** |
| Hints | Reduced hints — only show what's wrong when they click CHECK, no live coach tip guiding every step. The hint box shows a static "You're on your own, All-Star!" message instead of dynamic guidance. |
| Claim code | `L2-101-M1-83` |

**Why this is harder:**
- $120M with 2 stars means picking Towns ($53M) + Brunson ($35M) = $88M on two players alone, leaving only $32M for 6+ more players. Students must do real arithmetic.
- The position requirement forces them to think about roster composition, not just numbers.
- Fewer hints means they must track constraints themselves.

---

### LEVEL 3 — CHAMPIONSHIP GM
> *For students who want maximum challenge. Real NBA-style constraints.*

| Constraint | Value |
|---|---|
| Salary cap | **$110M** (very tight) |
| Min players | **10** (deeper roster) |
| Min stars | **2** |
| Quality target | **60** |
| Position requirement | **Must have at least 2 Guards, 2 Forwards, and 1 Center** |
| **NEW: Luxury tax penalty** | Every $1M over $100M costs 1 quality point (so going from $100M to $110M costs 10 quality points — students must weigh whether expensive players are "worth it" after the tax) |
| Hints | **No hints at all** — the hint box is hidden entirely. Students must read the rules on the landing page and remember them. |
| Claim code | `L3-101-M1-99` |

**Why this is harder:**
- The luxury tax creates a genuine optimization problem: spending $110M means losing 10 quality points, so the effective quality target is really 70 pre-tax.
- 10 players at $110M with 2 stars is a very tight budget puzzle.
- 2 Guards + 2 Forwards + 1 Center forces careful position management.
- No hints at all — students must internalize the rules and self-monitor.

---

## UI/UX Changes

### 1. Difficulty Selector (Landing Page)
After the rules box and before the "LET'S GO!" button, add a **three-card difficulty picker**:

```
┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   ROOKIE    │  │  ALL-STAR   │  │ CHAMPIONSHIP│
│     GM      │  │     GM      │  │     GM      │
│             │  │             │  │             │
│  "Start     │  │ "Ready for  │  │ "Think you  │
│   here!"    │  │  a real     │  │  can handle │
│             │  │  challenge" │  │  the pros?" │
│   ⭐        │  │   ⭐⭐      │  │   ⭐⭐⭐    │
└─────────────┘  └─────────────┘  └─────────────┘
```

- Cards are styled to match the existing Knicks aesthetic (blue/orange gradients).
- Selected card gets a highlighted border + "SELECTED" badge.
- Default selection: Rookie GM (so if they just click "LET'S GO!" they get the easy mode).

### 2. Game Page Adjustments
- **Header badge** shows current difficulty level (e.g., "ALL-STAR GM" in orange).
- **Dashboard** dynamically shows the correct targets for the selected level:
  - Star count target, player count target, quality target, salary cap all update.
  - Level 2+: A new "Positions" stat card appears showing Guard/Forward/Center count.
  - Level 3: A "Luxury Tax" stat card shows the tax penalty in real time.
- **Hint box**: visible with dynamic tips (Level 1), static message (Level 2), hidden entirely (Level 3).
- **Player cards**: On Level 2+, the position badge becomes more prominent since positions matter.

### 3. Victory Modal Adjustments
- Shows the difficulty level completed.
- Level-specific claim code.
- Level 3 victory shows luxury tax breakdown (salary used, tax paid, net quality).

### 4. Rules Page Adjustments
- Rules dynamically update based on selected difficulty before starting:
  - Level 1: Shows current 4 rules.
  - Level 2: Shows 5 rules (adds position requirement).
  - Level 3: Shows 6 rules (adds position requirement + luxury tax explanation).

---

## Implementation Plan (all in index.html)

### Step 1: Add difficulty state and config
- Add a `DIFFICULTY_LEVELS` config object mapping each level to its constraints.
- Add a `currentDifficulty` variable (default: 1).

### Step 2: Build difficulty selector UI
- Add three clickable cards between the rules box and the start button on the landing page.
- Style them to match the existing design language.
- Wire up click handlers to set `currentDifficulty`.

### Step 3: Make rules dynamic
- Update the rules box to render rules based on `currentDifficulty`.
- Level 2 adds the position rule. Level 3 adds position + luxury tax rules.

### Step 4: Update game logic
- `updateDashboard()`: read constraints from the current difficulty config.
- Add position tracking (count guards, forwards, centers from selected players).
- Add luxury tax calculation for Level 3.
- `updateHint()`: full hints (L1), static message (L2), hidden (L3).
- `checkTeam()`: validate against current difficulty's constraints.

### Step 5: Update dashboard UI
- Dynamically show/hide position stat card and luxury tax card based on difficulty.
- Update all target labels ("Need 50+" becomes "Need 55+", etc.).

### Step 6: Update victory modal
- Show difficulty badge.
- Use level-specific claim code.
- Show luxury tax breakdown on Level 3.

### Step 7: Position data cleanup
- Normalize player positions to enable reliable Guard/Forward/Center checks.
  - "Point Guard" → Guard, "Guard" → Guard, "Guard/Forward" → counts as both Guard and Forward.
  - "Forward" → Forward, "Center" → Center.
