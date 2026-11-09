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

## Phase 3 — Braid, Overlay, Blend (4 Nov 2026, built)
- **Decision (Ryan, 4 Nov):** labs.bible.org allowed as a second source, for NET only. It serves NET with a Strong's number on every word/phrase (CORS open). `chapter("NET",…)` now routes there; NET is tappable in the reader. Its note anchors carry no note text, so NET notes are still not shown.
- Lives in the **Version tab** as three modes — Spectrum · Braid · Overlay — plus a Blend toggle (Claude's placement call; Ryan to confirm). localStorage: `lex.vmode`, `lex.blend`.
- **Braid** (word for word): original word order (WLCa / SBLGNT); each box shows KJV/ASV/NET renderings; all agree / two of three / all differ. Tap a box → Original tab.
  - Hebrew numbering ≠ English (Ps titles, Mal 4, Joel 2–3): original verse found by Strong's overlap (±3 verses, then neighbouring chapters); shift shown on screen. Verified Ps 51:1 → 51:3, Mal 4:1 → 3:19.
  - A translation that uses a number a different number of times from the original shows "?" and doesn't vote (fixes KJV "should … perish", extra "his").
  - Agreement compares content words (small words ignored); archaic forms count as different.
- **Word-for-word blend:** NET's sentence as frame; a word swapped for KJV+ASV's shared wording only when they outvote NET, the swap is no longer than NET's wording, and it never adds/removes a negative. Caveat on screen: ASV revises KJV, so their agreement can be family likeness.
- **Overlay + median reading** (thought for thought): content-word overlap across the ticked English translations; median = highest average overlap. Caveat on screen: sees wording, not meaning.
- All tagged Algorithmic with the method shown. No Claude-judgement blend: the page is static and can't call Claude.
- Test verses: Gen 1:1, Ps 23:1, Ps 51:1, Mal 4:1, John 1:1, John 3:16, Rom 8:28.

## Next
1. Ryan road-tests Phase 3 on real reading; tune the blend rules from what feels off.
2. **Phase 4 (Ryan, 4 Nov): commentaries + "the why" between differing translations.** Ryan road-tested Phase 3 and signed it off ("works very well"). Ideas to design in Invention: (a) Sourced — public-domain word-study commentaries that explain translation choices (to research: which exist with a live, browser-reachable API); (b) Sourced — when a Braid box branches, show the lexicon's range of meanings so you can see each translation picked a different sense (data already fetched); (c) Claude judgement — not possible live (static page), park. Then the rest of Phase 4: commentaries (public domain first) + Bible Project / Practicing the Way links into the Commentary and Study tabs.
   - **Done 9 Nov (Phase 4a):** Commentary tab live — 7 commentaries fetched live from bible.helloao.org (Tyndale first as the modern voice; Adam Clarke, JFB, Calvin, Gill, Keil-Delitzsch, Matthew Henry), last-used remembered, section covering the verse shown with an Inferred tag when it spans verses. Plus a link to the verse's NET translators' notes on netbible.org. Ryan's call: HelloAO approved "for now"; modern commentaries planned alongside (below).
   - **Modern commentaries plan (Ryan, 9 Nov):** Ryan pays for no Bible service. Paid/licensed modern commentaries (NICNT, Word etc.) have no free API, so: (1) Tyndale now; (2) NET notes via link (done — embedding tested 9 Nov: labs.bible.org returns note markers only, no note text); (3) "Open in…" links only if Ryan ever subscribes to a service; (4) review periodically for a licensed API.
   - Still to do in Phase 4: "the why" layer (ideas a–c above); Bible Project / Practicing the Way links in the Study tab.
3. **Devotion tab (Ryan, 9 Nov):**
   - **Done 9 Nov:** new Devotion tab — My Utmost for His Highest (Oswald Chambers, classic edition), the day's reading title fetched live from utmost.org's public feed, prev/today/next day, "Read on utmost.org" link. Text stays on utmost.org (site marked ©).
   - **Ryan's call:** daily reading only (option 3). Matching a devotion to the passage was declined — utmost.org's feed doesn't expose each day's key verse, so matching would need a stored verse index (rejected, store-nothing rule) or loose text matching (~15 in 100 devotions quote a reference; too patchy).
   - Open ideas, not yet decided: edition choice (classic / updated / modern-classic — all on utmost.org); other devotionals with a live, reliable source.
4. **Future tabs (Ryan, 4 Nov) — not yet designed:**
   - **History tab:** the oldest known copy of the passage on screen (e.g. Dead Sea Scrolls, papyri, major codices), with date and where it's held.
   - **Context tab:** what was happening in Israel/the region at the time; how the language differs from today; the assumed knowledge the writers expected their audience to have; nuance lost over the years.
   - **Timeline:** where the passage fits, and which other books/passages were written around the same time.
   - Discernment flags for design time: manuscript data needs a reliable source (not memory); dates of writing are often contested (e.g. Daniel, Pentateuch, Gospels), so show the range of scholarly views, not one date; "assumed knowledge" and "lost nuance" are interpretation, so they need sources or a visible Claude judgement tag, and Claude judgement can't run live on a static page (would need pre-written content, which touches the store-nothing rule — scripture isn't stored, but this would be stored commentary; Ryan to decide).
5. Licensing review of bolls' copyrighted texts (now more pressing given NIV removal).
6. Parked: embedding NET translator notes (labs feed has anchors but no note text; linked out instead). "Translation history" on Original — needs a source or a Claude-judgement tag.
