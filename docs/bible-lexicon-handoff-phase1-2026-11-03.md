# Bible Lexicon — Handoff (Phase 1 complete, 3 Nov 2026)

## State
- Live at https://ryan-scholar.github.io/bible-lexicon/ (GitHub Pages, branch `main`, root folder). Repo: `ryan-scholar/bible-lexicon` (public; Claude GitHub App installed, pushes work from Claude sessions).
- `index.html` — the reader. Book/chapter/verse-range pickers (verse dropdowns sized to the loaded chapter), prev/next chapter, single view or A+B compare aligned verse-by-verse, 22 translations incl. Hebrew (RTL) and Greek. Deep links via hash, e.g. `#John.3.16-18.WEB.ESV`. Last passage/picks remembered in localStorage only.
- `plumbing.html` — API test bench; rerun it any time an API misbehaves.
- Ryan has reviewed Phase 1 and signed it off.

## Locked decisions
- Personal use only; link not shared. Live-fetch only; no scripture stored anywhere.
- Host is GitHub Pages, not an artifact (artifacts block all outbound fetch) and not Supabase/Drive.
- Single source: bolls.life for all text, originals and Strong's ("option 1"); licensing of the copyrighted translations there to be reviewed later, not now.
- Every blended reading or application note must carry a visible tag: Sourced / Algorithmic (method shown) / Claude judgement. Unsupported claims noted as such.
- Bible Project and Practicing the Way: link out, never reproduce.
- Translation groupings in the picker are labels from publisher descriptions, not measurements (stated in footer).
- Working style: brief, stepped, one question at a time; end each update with the single next step.

## Architecture
- One static HTML file, no build step, no framework. Fonts from Google Fonts.
- Endpoints: `https://bolls.life/get-text/{CODE}/{book}/{chapter}/` → `[{pk, verse, text}]`; Strong's: `https://bolls.life/dictionary-definition/BDBT/{G26|H430}/`; translation catalogue: `https://bolls.life/static/bolls/app/views/languages.json`.
- Book ids 1–66 Protestant order, chapter counts hardcoded in `BOOKS`. Translation codes in `TRANSLATIONS` (bolls slugs: NIV2011, CSB17, WLC, WLCa, SBLGNT, NA28, TR, LXX, LXXE…).
- `clean()` strips `<S>n</S>` Strong's tags, `<e>` notes and `<f>` footnotes — Phase 2 reverses this.
- In-memory chapter cache per tab (`Map`).

## Gotchas
- bolls has no single-verse endpoint (404); fetch the chapter and filter.
- Strong's-tagged texts are KJV, ASV, WLCa, TISCH — WLC (plain) has no numbers.
- STEPBible blocked from the browser; dropped.
- GitHub Pages settings and public-repo creation can't be done from a Claude session; Ryan does those.
- Commits from Claude need `-c user.name/-c user.email` on the git command.

## Next steps
1. → Phase 2: Strong's word lookup — tap a word in KJV/ASV/WLCa to show number, lemma, gloss, BDB/Thayer entry from the bolls dictionary endpoint; tag Sourced.
2. Surface NET translator notes and footnotes (currently stripped).
3. Phase 3: amalgamation engine (word-for-word and thought-for-thought) with convergence/branching view and the three tags.
4. Phase 4: commentaries (public domain first) + Bible Project / Practicing the Way links.
5. Later: licensing review of bolls' copyrighted texts; optional Crossway/Tyndale APIs.

## Next session setup
- Working Genius phase: Invention (designing the word-lookup interaction), then Enablement to build.
- Model/effort: Opus high (single-file app with a new data shape). Fable only if Phase 2 and 3 are done together.
- Nothing to download: source and this brief live in the repo (`docs/`). Open the new chat with: "continue from the handoff in ryan-scholar/bible-lexicon docs/".
