# OddEgg Changelog

All notable changes to the game will be documented here.

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
