# Indonesian A1-B1 vocab pack

Static data pack for a language-agnostic vocab trainer (`key: "id"`). It has
2000 words spanning A1-B1. Each word has a short English gloss. Example
sentences come with English translations. There is no recorded audio: the
trainer speaks every word and sentence with the browser's `id-ID` voice
(Apple devices ship no Indonesian voice, so the speaker buttons are silent
there). The Read tab adds 60 short reading passages with comprehension
questions (see "Reading passages" below).

**Live:** https://bannerless-studio.github.io/indonesian/

This repo holds the Indonesian data pack and the Indonesian data files its
build reads. It includes [`vocab-engine`](https://github.com/Bannerless-Studio/vocab-engine)
as a git submodule at `engine/`. The engine holds the shared UI, the drill
logic and the shared pack builder, `engine/tools/packbuilder`. The builder's
Indonesian rules live in `engine/tools/packbuilder/langs/id.py`.

**Scope note:** this app gives the vocabulary base for B1. A B1 exam (for
example UKBI or a BIPA level test) also needs grammar, writing and speaking
practice, which this app does not teach.

**Data quality:** Hand QA used a stratified sample of 180 words (60 per
level, seed 51) and 90 sentences (seed 52). 177 of 180 words had the right
primary sense, and the three misses now have gloss overrides. 498 of 507
sentence links were right. A second QA round found wrong-link classes, and
each was fixed by rule rather than word by word. A synonym linked in place
of the word (tiba "to arrive" counted as datang) is gone: an audit of all
17,393 links finds no synonym links; residual wrong-sense links are listed in TODO.md. A classifier after
a number (tiga ekor ayam) links the classifier sense, the parts of fixed
compounds (sama sekali "at all", rumah sakit "hospital") link nothing, and
one verb no longer has two headwords (tonton and menonton). A check of 20
sentences with me-/di-/ber-/ter- forms found every affixed verb linked to its
lemma (memakan and dimakan to makan). The top 300 words have no wrong part of
speech. The frequency list is built from film subtitles and is
colloquial (gue, lo, nggak, banget, udah). Colloquial spellings count toward
their formal word (udah toward sudah). nggak, gimana and the particles sih,
nih, tuh, kok and dong are words of their own, glossed "(colloquial)" and
kept to A2 or higher. Sentences with the Jakarta pronouns gue/lo are left
out, because the pack does not teach them. A1 examples prefer formal or
neutral sentences. Tatoeba had fewer than two usable sentences for several
hundred words, so 461 simple sentences were written for this pack; each is marked
`"src": "gen"` in `pack/sentences.json`, and the exact count is in
`pack/attribution.json`. They are machine-written and reviewed, but not by a
native Indonesian speaker. Sexual content and violence are kept out of A1/A2
sentences, and rape or abuse sentences are left out at every level. Levels
are frequency bands, not CEFR. Rules, counts and seeds are in
`tools/REPORT.md`, and residuals are in `TODO.md`.

## Reading passages (Read tab)

`pack/passages.json` holds 60 short reading texts, 20 each at A1, A2 and B1,
with comprehension questions each. The format is in the engine's
`docs/PACK_SCHEMA.md`. The texts were written for this pack (`"src": "gen"`)
and their source is `tools/passages_src.json`. Rebuild from that source with:

```
PYTHONPATH=engine/tools python3 -m packbuilder passages --lang id .   # --check: report only
python3 engine/tools/jsonify_pack.py pack                             # passages go into sentences.js
```

The builder links word ids the same way it does for the example sentences.
It enforces in-pack coverage of at least 95% at A1 and A2, and at least 93%
at B1. It also enforces a level budget: an A1 passage may use at most 3 A2
words (and no B1 words) and an A2 passage at most 3 B1 words. Words per
passage (by whitespace count) run 66-78 at A1, 92-112 at A2 and 121-136 at
B1; coverage is 1.000 at every level's median and never drops below 0.989.
The 60 passages carry 89 questions at A1, 97 at A2 and 100 at B1 (a mix of
multiple-choice and true/false). Per-passage numbers and the QA notes are in
`tools/REPORT_passages.md`.

A level's 20 passages unlock once the learner has learned 70% of that
level's words. Tapping any word in a passage shows its gloss, including
affixed and reduplicated forms and multiword compounds, which read as a
single tap. Comprehension questions feed missed words back into the review
queue as weak words.

`tools/gloss_display.json` holds 41 display-only glosses (senses shown to
the learner that don't feed word linking or example choice), added while
writing the passages; see `engine/tools/packbuilder/README.md` under
"gloss_display" for the format.

The passages and questions are machine-written by Claude, checked by an
automated QA pass; they have not had a native-speaker review.

## Layout

```
pack/
  pack.json         trainer config (levels, placement test, function words, typing rules)
  words.json        2000 word entries
  sentences.json    example sentences, each tagged with the word ids it covers
  attribution.json  per-source licence + contributor attribution
  pack.js words.js sentences.js   generated by engine/tools/jsonify_pack.py (committed, never hand-edited)
engine/             git submodule -> vocab-engine (UI, drill logic, build/validate tools,
                    tools/packbuilder = the shared pack builder, langs/id.py = Indonesian rules)
tools/
  build_pack.py     shim: runs `python3 -m packbuilder build --lang id --repo .` from engine/tools
  gloss_overrides.json  hand gloss fixes ("lemma|pos")
  gloss_display.json    display-only glosses ("lemma|pos"), merged into words.json after linking
  forced_a1.txt     A1 core list, forced into A1 (the closed sets are in langs/id.py)
  generated_sentences.tsv  sentences written for this pack (word, Indonesian, English)
  id_map_v1.json    frozen "lemma|pos" -> word id (keeps learner progress across rebuilds)
  requirements.txt  engine/tools/packbuilder/requirements.txt + Stanza + Sastrawi
  REPORT.md         generated coverage report from the last build (manual section kept)
build.sh            builds index.html (the self-contained trainer) from pack/ + engine/
check.sh            packbuilder check + engine validator + stale-build guard, all in one
index.html          built trainer, served by GitHub Pages at the repo root
```

## Rebuilding

```
git clone --recurse-submodules <this repo>
# or, if already cloned: git submodule update --init

cd indonesian
python3 -m venv .venv
source .venv/bin/activate
pip install -r tools/requirements.txt
python -c "import stanza; stanza.download('id', package='gsd', processors='tokenize,mwt,pos,lemma')"

python3 tools/build_pack.py          # rebuild pack/{pack,words,sentences,attribution}.json + tools/REPORT.md
python3 engine/tools/jsonify_pack.py pack   # regenerate pack/*.js from the .json
./build.sh                           # build index.html
./check.sh                           # pack checks + engine validation + stale-build guard
```

Sources are downloaded once into `.cache/`, which is gitignored. The build is
deterministic, so re-running from cache reproduces byte-identical
`pack/*.json`. Stanza tags all 28k Tatoeba sentences once (about 3-5 minutes
on a laptop CPU) and the result is cached under `.cache/derived/`. Editing
`tools/generated_sentences.tsv` changes the corpus, so the next build tags
again.

To build against a vocab-engine checkout other than the submodule, set
`PACKBUILDER_PATH=../vocab-engine/tools` for `tools/build_pack.py` and
`./check.sh`. QA helpers run with
`PYTHONPATH=engine/tools python3 -m packbuilder {scan,sample} --lang id --repo .`.

## Indonesian rules

The builder's Indonesian module handles what the shared pipeline cannot guess.

- **One verb, one word.** me- and di- forms and the bare object-voice
  spelling are inflections of one verb. The root is the word when it is used
  on its own about as often as the me- verb: memakan, dimakan and makan are
  makan; memakai is pakai, menyimpan is simpan. When the root is rare on its
  own, the me- verb is the word and the root counts toward it: menangis
  (tangis), menonton (tonton), mengajar (ajar), menulis (tulis). An
  object-voice -kan or -i spelling always counts toward its me- verb:
  lakukan is melakukan, katakan is mengatakan, bayangkan is membayangkan,
  hindari is menghindari; typing it is accepted. ber- verbs are lemmas of
  their own (bekerja, bermain, bertemu), and a bare root with the same
  meaning counts toward them (main toward bermain). Derived verbs with a
  meaning of their own stay apart (mendengarkan "to listen" / dengar "to
  hear", melahirkan "to give birth" / lahir "to be born").
- **Derived nouns are words of their own.** makanan, pekerjaan, kebersihan
  and pembelian are separate lemmas. A derived word Wiktionary does not list
  (persahabatan, terserah) links nothing in a sentence rather than being
  counted as its root.
- **Enclitics are split off.** rumahku, pekerjaannya, siapakah and itulah are
  the host word plus -ku/-nya/-kah/-lah, and only the host is linked. A
  spelling Wiktionary lists as its own word stays whole (sekolah, masalah,
  bangku, apakah, akhirnya, biasanya).
- **Fixed compounds.** When a two-word Wiktionary headword means something
  its parts do not (sama sekali "at all", bulu babi "sea urchin"), its parts
  link nothing in that sentence. A hand list adds compounds the gloss test
  misses: orang tua "parents", salah satu "one of", rumah sakit "hospital",
  makan siang "lunch", kamar mandi "bathroom", memberi tahu "to inform" and
  others link no part at all; in ulang tahun, sumber daya, latar belakang,
  masuk akal and the rest a part whose gloss shares a word with the compound
  keeps its link (sumber "source" in sumber daya "resource"). A part whose
  hand gloss teaches the compound also keeps its link (berterima in
  berterima kasih). Compounds built from their parts' meanings keep both
  links (hari ini "today").
- **Inflected forms are alternatives.** Every written form of a word seen in
  the corpus with me-/di-/ber-/ter-/per-, -kan/-i, an enclitic (-nya, -lah,
  -ku ...) or reduplication is listed as an alt: typing memakan for makan is
  accepted, example sentences bold dimakan, and a gap blanks the whole
  anak-anaknya. A form two words share, or one spelled like another word, is
  nobody's alt. A verb root seen bare in under 20% of its uses is shown as
  its me- verb, the root kept as the first alt (memeriksa, menghubungi,
  mengemudi, menyanyi, menghapus).
- **Object voice and other readings.** "yang pernah kamu alami" links
  mengalami (not alami "natural"); kena + a word links its verb reading
  (kena pukul "get hit", not pukul "o'clock"). A word the tagger reads with a
  POS the pack has no entry for links the pack entry only when that reading
  means the same (semua, seperti, tidur, cokelat); akan "about" is not akan
  "will". The sentence-initial fallback that links a word by its spelling
  applies only to tokens tagged as names (Bagilah is bagi "to divide", not
  the preposition bagi "for").
- **Classifiers.** seorang, seekor and a number + classifier (tiga ekor
  ayam) link the classifier word, whose gloss carries the classifier sense:
  orang, ekor, buah, batang, lembar.
- **Spelling variants.** disini, dimana and kemana (written as one word) link
  sini and mana; silahkan links silakan; ku- and kau- + verb (kulakukan)
  link the verb.
- **Reduplication is a plural.** anak-anak and buku-buku count as anak and
  buku. Reduplicated words with their own meaning stay words (laki-laki,
  hati-hati "careful", tiba-tiba, kupu-kupu); one whose Wiktionary glosses
  share nothing with its root's (rata-rata "average", mata-mata "spy",
  satu-satunya "the only") links nothing.
- **Capitalisation.** Days, months and formal Anda are written with a
  capital letter and are linked mid-sentence. Minggu "Sunday" and minggu
  "week" are one entry. Titles and forms of address (Pak, Ibu, Tuan, Dokter)
  are common nouns, linked before a name too. A capitalised word
  mid-sentence that the corpus writes lowercase at least as often (cerita
  Ayah, Bahasa Indonesia) is the common word; other capitalised words are
  names and never linked.
- **Numbers** are taught as their parts: nol to sepuluh, sebelas, belas
  (-teen), puluh (tens), seratus/ratus, seribu/ribu and juta.
- **Typing** has no diacritics to relax, so it is strict from A1 and not
  case-sensitive.

## Sources and licences

| Data | Source | Licence | Used for |
|---|---|---|---|
| Spoken/subtitle frequency | [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (`id_full.txt`, 2018 OpenSubtitles) | CC-BY-SA 4.0 | word ranking |
| Written/general frequency | [`wordfreq`](https://github.com/rspeer/wordfreq) Python package (`small_id`) | CC-BY-SA 4.0 | word ranking |
| Glosses, part of speech, affix and plural links | [kaikki.org](https://kaikki.org) Indonesian Wiktionary extract | CC-BY-SA 3.0 / GFDL (Wiktionary) | English glosses, POS, inflection map |
| POS tagging / lemmatisation (build time only) | [Stanza](https://stanfordnlp.github.io/stanza/) (Apache-2.0) with its Indonesian `gsd` model, trained on UD_Indonesian-GSD | CC BY-SA 4.0 (model training data) | corpus POS, lemma and sense choice; clitic splitting; sentence word links. The pack ships no model files. |
| Affix roots (build time only) | [Sastrawi](https://github.com/har07/PySastrawi) stemmer | MIT | root of me-/di- verbs |
| Example sentences | [Tatoeba](https://tatoeba.org) `ind_sentences_detailed.tsv` | CC-BY 2.0 FR | sentence text (contributor usernames in `pack/attribution.json`) |
| Sentence translations | Tatoeba `eng_sentences.tsv` + `ind-eng_links.tsv` | CC-BY 2.0 FR | English translations |
| Generated sentences | written for this pack, `tools/generated_sentences.tsv` | CC-BY-SA 4.0 | sentences for words Tatoeba covers with fewer than 2 usable sentences, marked `"src": "gen"` |

Licence: code MIT, pack data CC BY-SA 4.0, see LICENSE.

Tatoeba has only 18 permissively licensed Indonesian audio clips, so the pack
links none and relies on TTS. No licence is non-commercial. No graded
Indonesian word list is used or shipped.

## Level bands

Candidate (lemma, POS) pairs are ranked by a blended frequency score: the
weighted mean of log subtitle rank, log `wordfreq` rank and log rank in the
tagged Tatoeba corpus. The Tatoeba rank has weight 1.5 against 1 each for
the other two, because the subtitle list is colloquial.

- **A1** (600 words): every forced item, then the highest-ranked remaining
  words. Forced items are days, months, numbers, colours, pronouns, question
  words, core prepositions and conjunctions, time words, greetings and set
  phrases (terima kasih, selamat pagi, sampai jumpa), the core function words,
  and the A1 core list in `tools/forced_a1.txt`.
- **A2**: the next 700 by rank. Colloquial words start at A2.
- **B1**: the next 700 by rank.

A word no example sentence can illustrate gives its place to the next word by
rank. This is a simple, reproducible proxy for CEFR level. It is not an
official CEFR classification.

To take an engine update, run `git submodule update --remote engine`, then
rebuild with `./build.sh`.
