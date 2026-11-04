# Bible Lexicon — Handoff (Phase 2 BUILT and signed off — 3 Nov 2026)

Read `bible-lexicon-handoff-phase1-2026-11-03.md` first for state, locked decisions, architecture and gotchas. This brief adds the Phase 2 design, agreed by Ryan in Invention + Discernment on 3 Nov 2026.

## The interaction (agreed)
- **One tap on anything opens a four-button chooser** — never a hidden path. Tap a word *or* a verse number → chooser: **Original · Version · Commentary · Study**. Pick one → the study drawer opens on that tab.
- Context changes only the **order** of the four, never what's offered: word tap puts Original first; verse-number tap puts Version first.
- **Drawer** = right column on desktop, bottom sheet on phone. Stays open; each new tap replaces its contents. Keep a short "recent words" strip (last ~5) so you can bounce between words in a verse.
- **Original tab** (Phase 2, build now): Strong's number, lemma, gloss, BDB/Thayer entry from `https://bolls.life/dictionary-definition/BDBT/{G26|H430}/`. Visible tag: **Sourced**. **Echo highlight**: every word in the chapter carrying the same Strong's number lights up while this tab is open — in the compare column too, if it's a tagged text. Small **interlinear toggle** in this tab shows the number as a tiny label under every tagged word.
- **Version tab** (Phase 2, build now, verse-level): the tapped verse across the picked translations on the spectrum — current translation centred, word-for-word stacked above, thought-for-thought below (spectrum order = the existing picker groupings; still labelled as publisher descriptions, not measurements). Highlight the tapped word in the tagged texts; show the whole verse in the untagged ones. Honest limit: bolls only tags KJV, ASV, WLCa, TISCH, so word-level alignment across translations is not possible — do not fake it.
- **Commentary / Study tabs**: build as placeholders ("coming in Phase 4") so the shape is right now. Commentary later = side/bottom panel with last-used commentary + picker; Study later = devotion/study options for the passage.
- Only KJV / ASV / WLCa / TISCH words are tappable for Original (they carry `<S>n</S>`). Untagged texts (ESV, NIV…) — park the wrinkle; a tap there can still open Version and the placeholders.

## Discernment record
- Door: **two-way** (one HTML file; git revert undoes it). Short pass only, no full rubric, no premortem.
- Parked for a later Discernment pass: Ryan wants "history of when/how the word was converted into English" on the Original tab — no source feeds that from bolls. Either find a source or ship it tagged **Claude judgement**. Not in Phase 2.

## Definition of done (read aloud at the end, don't fill a form)
Tap any word in KJV/ASV/WLCa → chooser → Original shows number, lemma, gloss, BDB/Thayer, tagged Sourced, echo-highlighted in the chapter; Version shows the verse across picked translations on the spectrum; Commentary/Study tabs exist as placeholders; works on a phone; existing deep links still work.

## Build notes
- Reverse `clean()` for Strong's: keep `<S>n</S>` tags, convert to `<span data-s="G26">word</span>` (number applies to the word *before* the tag in bolls markup — verify in `plumbing.html` first). Keep stripping `<e>`/`<f>` for now (notes/footnotes are step 2 of the roadmap).
- Cache dictionary lookups in a `Map` like chapters.
- No framework, no build step, single file. Commit with `-c user.name=… -c user.email=…`. Fetch `origin main` before pushing.

## Next session setup
- Working Genius phase: **Enablement** (build), then Tenacity (read the done-list back).
- Model/effort: **Opus high**. Fable not needed unless Phase 3 (amalgamation engine) is bundled in.
- Open the new chat with: "continue from the Phase 2 design handoff in ryan-scholar/bible-lexicon docs/".

## Phase 2 outcome (3 Nov 2026, evening)
- Built and live. Done-list read back and met: word tap → chooser → Original (Sourced, echo-highlight, interlinear toggle), Version (verse-level spectrum, translation picker), Commentary/Study placeholders, phone bottom sheet, deep links intact.
- Learned on the way: **SBLGNT carries Strong's numbers** on bolls (now tappable; TAGGED = KJV, ASV, WLCa, SBLGNT). SBLGNT also carries `<m>` morphology tags — stripped in `clean()`. **NIV2011 has been pulled from bolls** (verse text replaced by a notice from the site owner) — removed from the picker. Both nudge the licensing review up the list.
- Phone header now collapses to one summary line (passage + translations); tap to expand the pickers.
- localStorage keys: `lex.state`, `lex.tab`, `lex.recent`, `lex.versions`, `lex.inter`.

## Phase 2b — notes (4 Nov 2026, built)
- Surveyed every picker translation on bolls (John 3, Gen 1, Ps 23 and others). **bolls carries no NET translator notes** and no `<f>`/`<e>` notes in any picker text. The only notes are in `<sup>` tags, which hold three different kinds of thing:
  - KJV: 1611 margin notes, always shaped `keyword: Heb./Gr./or, …` → shown as a † at verse end; tap to open the note inline, tagged Sourced.
  - ASV: a `<sup>` opening verse 1 = Psalm title → shown as an italic heading.
  - CSB17 (ⓒ reference letters), SBLGNT/NA28 (variant signs, `[*]`): nothing behind them in bolls → dropped.
- Sorting is by shape (`notesOf()` in index.html). Version tab shows the text without markers.
- Parked: NET notes need a second source (e.g. NET's own site/API), which would break the bolls-only decision — Ryan's call, not built.

## Next
1. Phase 3: amalgamation engine. Opus high; Fable only if bundling with Phase 4.
2. Licensing review of bolls' copyrighted texts (now more pressing given NIV removal).
3. Parked: NET translator notes (second source needed). "Translation history" on Original — needs a source or a Claude-judgement tag.
