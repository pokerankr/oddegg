# OddEgg Changelog

All notable changes to the game will be documented here.

---

## [0.7.7] — 2026-04-15
### Bug Fix

#### Double-Hatch on Concurrent Spam Tapping
- Fixed: two devices hammering steps simultaneously could hatch two different Pokémon back-to-back at the same timestamp
- When a new egg arrives via realtime after a claim, accumulated pending steps are now immediately drained — they no longer overflow into the fresh egg

---

## [0.7.6] — 2026-04-15
### Cleanup

#### Removed Ditto Mode
- Dev-only "🔮 Ditto" button removed from the UI

---

## [0.7.5] — 2026-04-15
### UI Polish + Flame Body Charm

#### Step Counter Repositioned
- Step counter moved to the top-right corner of the egg box — no longer floats in the middle of the tapping area

#### Recently Found Delayed Until After Reveal
- Hatched Pokémon (and any evolution entries) no longer appear in Recently Found during the 5s reveal countdown — they are held and flushed all at once when the countdown ends
- Fixed: evolution entries (e.g. Wartortle when Squirtle triggers a merge) were bypassing the delay and appearing as spoilers during the reveal — all log entries are now deferred together in the correct order

#### Flame Body Charm (Secret)
- New hidden charm using the Volcano Badge — unlocks by hatching any Fire-type Pokémon with the Flame Body ability (Ponyta, Magmar, Litwick line, Volcarona line, and more)
- Effect: all future eggs hatch 10% faster
- Charm is invisible until earned — no locked placeholder shown
- Ditto morphing into a Fire-type also counts

#### Bug Fixes
- Fixed Ditto morphing into a fire-type not triggering the Flame Body charm unlock
- Fixed recently found not populating at all after the reveal delay was introduced

---

## [0.7.4] — 2026-04-13
### Step Counter Accuracy + Auto-Claim

#### Server-Authoritative Global Step Counter
- Global step total is now tracked server-side inside `increment_egg_steps` — increments by the actual steps consumed (capped at the egg's target), not the raw amount sent
- Concurrent taps from multiple devices no longer overcount: the RPC serializes flushes with a row lock, so only steps that actually mattered are credited
- Removed client-side click accumulation (`_pendingClicks`) — the counter is now always accurate regardless of how many devices are tapping simultaneously

#### Claim Race Removed
- The "Claim It!" button is gone — whoever's step caused the hatch is auto-credited immediately
- `increment_egg_steps` now returns the egg row, so the flushing client detects the hatch and calls `try_claim_egg` in the same round-trip (no more 3s fallback delay for the claimer)
- 3s fallback auto-claim still fires as a safety net if the claiming client crashes mid-flush

#### Reveal Screen
- After a hatch, the caught Pokémon is shown for 5 seconds with a "New egg in X…" countdown before the next egg generates

#### Ditto Picker (Claimer Only)
- Second+ Ditto: the claimer gets a 30s window to pick which Pokémon Ditto transforms into
- If no selection is made within 30s, a random available Pokémon is auto-picked

#### Bug Fixes
- Fixed "Anonymous Trainer" appearing in the log — trainer names that weren't set now get a fun NPC name
- Fixed Ditto double-pick: stuck-claim timer no longer fires while the claimer is in the picker
- Fixed spam-click dex loss: click-only syncs now echo the server's last-known dex state instead of the potentially-stale local copy
- Fixed taps past the egg threshold counting toward the global total

---

## [0.6.0] — 2026-04-12
### Mobile UI + Trainer ID + Ho-Oh + Dex Overhaul

#### Mobile UI Overhaul
- Tab bar at the bottom on mobile (< 768px): **Egg** tab and **Dex** tab switch between panels
- Only one panel is visible at a time on phones — no more scrolling through a stacked layout

#### Trainer ID
- On first visit, a modal asks for your trainer name — stored locally, persists across sessions
- Your name appears in the topbar; tap it any time to change it
- All egg claims now use your saved trainer name — no more name form after every hatch
- Claims are instant: win the race, Pokémon goes to your dex immediately

#### Ho-Oh Easter Egg (desktop only)
- Ho-Oh flies across the screen at random 2–5 minute intervals
- Click it while it's flying to earn a 10% step bonus toward the current egg
- Desktop-only (requires a mouse pointer)

#### Dex Panel Restructure
- **Recently Found** always shows all activity — shinies and normals combined
- Replaced the Base/Shiny toggle tabs with two separate buttons: **Open Base Dex** and **✨ Open Shiny Dex**
- Charm icons moved to the dex header next to "OddEgg Record / Living Dex" — hover for status

#### Profanity Filter
- Added: peen, genital, genitals, vulva, phallus, dong, schlong, shlong, taint, gooch, fisting, fingering, spunk, labia, clitoris, cockring, and more

#### Restored Normal Odds
- Steps restored to 1–1,000,000 (were temporarily 1–100 for testing)
- Shiny odds restored to 1 in 512

---

## [0.5.0] — 2026-04-12
### Shared Egg
- All players now hatch the **same egg** together — no more separate per-player eggs
- Egg state lives in Supabase and is broadcast in real time to every connected player
- When the egg hatches, everyone sees a **Claim It!** button simultaneously
- The first player to tap wins the claim — they get the name form; everyone else sees "Another trainer is claiming this egg…"
- After the winner confirms, the next egg is immediately generated and shared with all players
- Ditto morph picker only appears for the player who claimed the egg
- Dev skip button now forces a server-side hatch instead of advancing the local counter

---

## [0.4.3] — 2026-04-12
### Merged Sync + Autoclicker Detection
- All Supabase writes now happen in a single round-trip via `sync_game_state()` — halves API usage compared to the previous two-call approach
- Step counter now updates on all screens at a steady 2-second cadence even during nonstop tapping, not just when you pause
- Autoclicker detection: more than 10 clicks per second triggers a warning toast and pauses stepping for 5 seconds

---

## [0.4.2] — 2026-04-12
### Atomic Step Counter
- Fixed race condition where clicks from two devices would overwrite each other
- Clicks are now batched locally and flushed to Supabase atomically every 1.5 seconds — the database adds them rather than replacing the count, so no clicks are lost regardless of timing

---

## [0.4.1] — 2026-04-12
### Realtime Sync
- Dex panel, recent log, charm bar, and step counter now update live across all connected devices — no refresh needed
- When another player catches a Pokémon, it appears in your dex instantly
- Your current egg is never interrupted by another player's activity

---

## [0.4.0] — 2026-04-12
### Supabase Integration (Shared State)
- Dex, recent log, charms, and global step count now save to Supabase
- All players share the same living dex — catches are visible to everyone
- Each player still has their own in-progress egg (shared egg coming in a future update)
- Existing localStorage data is automatically migrated to Supabase on first load
- Writes are debounced during rapid clicking; hatches and resets save immediately

### Found Counts — Shiny and Normal Now Separate
- Recent log entries each show their own count based on type: shiny entries show the shiny find count, normal entries show the normal find count
- Full dex slots now display both counts independently ("No. found: 3" and "✨ Shiny: 1") — shiny count only appears if greater than zero
- Fixed bug where shiny Ditto transforms showed "No. found: 0" in the recent log

---

## [0.3.0] — 2026-04-11
### Ditto Mechanic
- Ditto has a 1 in 1,025 chance of appearing instead of a normal egg
- First Ditto hatched is added to the dex normally
- Every subsequent Ditto opens a morph picker — choose any base-form Pokémon for it to transform into
- Transformed Pokémon is added to the dex and shown in Recently Found with a purple "Transformed from Ditto" label
- Morph picker only shows hatchable (base-form) Pokémon — no evolved forms
- Catching Charm unlocks on your first Ditto (shiny or normal), improving Ditto odds to 1 in 256
- Ditto egg uses a unique egg image; shiny Ditto uses its own shiny egg image
- Dev: "Ditto Mode" button forces every egg to be a Ditto for testing

### Bug Fixes
- Fixed morph picker appearing on the first Ditto (should only appear from the second onwards)
- Fixed Catching Charm not unlocking when the first Ditto was shiny
- Fixed second shiny Ditto not triggering the morph picker

---

## [0.2.0] — 2026-04-10
### Charm System
- **Oval Charm** — unlocks at 100 Pokémon caught; completed evolution lines become 15× less likely to appear in eggs
- **Shiny Charm** — unlocks at 512 Pokémon caught; improves shiny odds from 1 in 512 to 1 in 128
- Charm bar displayed in the dex panel showing unlock progress
- Toast notification pops up when a charm is unlocked
- Shiny Pokémon count toward the total for charm thresholds

### Hatching Dialogue
- Egg now shows dialogue stages as it progresses:
  - Start: "An Odd Egg is waiting…"
  - First step: "It looks as though this Egg will take a long time yet to hatch."
  - 15–44% through: "This Egg doesn't seem close to hatching yet."
  - 45–74% through: "What's this? The Egg is moving!"
  - Final steps: "The Egg is about to hatch! Keep going!"

### Shiny Visual Polish
- Shiny Pokémon display a blue sparkly aura (distinct from the gold aura used for Legendaries/Mythicals)
- Blue glow on egg, sparkles, and reveal orb for shiny hatches

### Profanity Filter
- Trainer name input filters common profanity and slurs
- Handles leet-speak and symbol substitutions (e.g. @ for a, $ for s)
- Word list loaded from external `profanity.json` file

---

## [0.1.0] — 2026-04-09
### Initial Release
- Egg hatching clicker game — take steps to hatch Pokémon eggs
- Living dex: one slot per evolution stage; duplicates automatically trigger evolutions
- Evolution logging: each evolution step (e.g. Charmander → Charmeleon → Charizard) appears as its own entry in Recently Found
- Shiny Pokémon with 1 in 512 odds
- Legendary and Mythical Pokémon with gold aura
- Dex panel with normal and shiny tabs, recently found log
- Full dex overlay with search and pagination (40 per page)
- "Seen" vs "Caught" status — Seen means encountered but since evolved away; Caught means currently in the dex
- Global step counter
- Dev mode: Charmander-only eggs for testing
- Trainer name entry when claiming a hatched egg
- State saved to browser localStorage
