# TODO (v2 candidates)

Residuals from the v1 QA rounds and the fix round. The rules already in place
are described in `engine/tools/packbuilder/langs/id.py` and summarised in the
README.

## Levels
- Levels are frequency bands. The subtitle list is colloquial and film
  dialogue skews toward crime and drama, so words such as bunuh, polisi and
  penjara rank early. bunuh, tembak, darah, bom, meledak and mayat are moved
  to B1 by the violent-gloss rule. A written-register frequency list would
  give a better A1/A2 split.
- Colloquial particles (kok, dong, deh) sit near the 2000-word cut, so small
  source changes can push them in or out of the pack (deh is out in this
  build: no usable example sentence).
- The QA broad sensitive scan still shows 47 A1/A2 sentences about war,
  prison, death from illness, drinking or kissing. They are outside the
  agreed A1/A2 tiers; decide whether any of them should join.

## Lemmas
- Which form of a verb is the word follows frequency: the root when it is
  used on its own about as often as the me- verb (zipf within 0.5), else the
  me- verb. A root seen bare in under 20% of its corpus uses (5+ uses) is
  displayed as its me- verb (periksa -> memeriksa, hubung -> menghubungi);
  bela (2 of 5 uses bare) stays bela. The id key stays the root.
- 8 words changed at the B1 cut in the polish round (in: asam, bandar,
  baterai, kabel, museum, pas, paus, selam; out: beroperasi, kebencian,
  menyambut, sabun, sari, selimut, terjun), from the re-tag and the
  air terjun compound (terjun lost its sentences). kini "present, current"
  (adj) is merged into kini "now, nowadays" (adv) by a drop_keys redirect.
- Seven derived verbs share a gloss with their root and stay separate words:
  menyukai/suka, mempunyai/punya, memerlukan/perlu, mengikuti/ikut,
  memasuki/masuk, membangunkan/bangun, mengembalikan/kembali. Their glosses
  say how they differ.
- Transitive me- verbs whose root folds into a ber- verb count toward it:
  memainkan -> bermain, memburu -> berburu, meniup -> bertiup, mengumpulkan
  -> berkumpul. mengubah is its own word (its root ubah is rare).
- A few hand tables remain: `FORM_OF` (berikan = beri + -kan, not ber- +
  ikan), `BER_EXCEPT` (berikut is not the ber- verb of ikut),
  `LEXICAL_CLITIC` (apakah, akhirnya, biasanya ... stay whole), the
  bound-root `drop_keys` (alih, tuju, kejar ...) and the second-entry merges
  (perlu, pasti, segala, menarik, kasih, mendengar).
- Words Wiktionary does not list and Stanza misspells stay unlinked:
  kenakan (to wear), dijalan (di jalan written as one word), beritahu.

## Glosses
- Glosses beyond the ~620 hand overrides in `tools/gloss_overrides.json`
  come straight from the Wiktionary sense ranking. kaikki's Indonesian senses
  are a short translation followed by an English definition; the rule keeps
  the translation. Lower B1 glosses can still lead with a secondary sense.
- Some glosses use a definitional form for words with no single English
  equivalent (particles, classifiers, secara, para).
- menarik is glossed "interesting; to pull, to attract" and takes every use,
  since the tagger calls both readings a verb.

## Sentences
- The parts of non-compositional compounds link nothing (sama sekali, rumah
  sakit, orang tua, makan siang, musim panas, tempat tidur): 206 visible
  pack-word tokens in the shipped sentences. A phrase entry per compound
  (PHRASE words like terima kasih) would teach them instead of hiding them.
  kaus kaki, sarung tangan, kata sandi and harta benda are left linked: kaus,
  sarung, sandi and harta occur almost only in them and would drop out.
- Residual over-unlinking: 55 visible pack-word tokens (27 words) outside
  compounds and phrases link nothing. Most are right (akan "about", kata
  "said", pukul in "kena pukul", saat and other words with two pack entries
  the tagger reads with a third POS); a few are tagger misses on
  sentence-initial words (Lebar "width", Putar tagged as a noun).
- Residual wrong-sense links, about 1% of links in the QA sample: homographs
  the tagger reads with the POS of the pack entry (terkenal "famous" linked
  to kenal "to know" in one sentence, benar-benar and tanda-tanda left
  unlinked as their Wiktionary glosses share no word with the root's).
- 13 of 4000 example slots (2 per word) show neither the headword nor an
  alt; 59 words have no candidate sentence with the bare headword (mostly
  verbs used only affixed, shown through their alts).
- 421 sentences were written for the pack (`src: "gen"`), mostly for B1
  words. They are simple and have no audio. A native-speaker review would
  improve their naturalness.
- Tatoeba has no permissive audio worth linking (18 clips); the pack relies
  on the browser's id-ID TTS voice, which has not been checked on every
  platform.
- Derived words Wiktionary does not list (persahabatan, terserah, penyebab,
  pemanas) link nothing, so a sentence using one does not illustrate its
  root.

## Reading passages
- Pack gaps: words the passages wanted and the pack lacks. They were
  rewritten with pack words, or kept as a declared out-of-pack word where the
  text needs them. daun, tiba-tiba, padahal, misalnya, layar, dosen, warung,
  helm, pelabuhan, rupiah, menu, penelitian, rata-rata ("on average"; the
  pack's rata is "even"), karyawan, pengumuman, ojek, sambal, kursus, panen,
  pinggir, lowongan, disiplin, adat, doa, rekaman, mencatat, mewah, ulasan,
  pemandu, penghuni; also kerupuk, rendang, nyenyak, bising, keseimbangan,
  justru, pembeli, pejalan (kaki), bambu, tur, pemutaran.
- Alt defect in the word build: 17 -kan/-i forms are alts of a root verb
  while their me- verb is itself a pack word. masuk: masukkan, dimasukkan
  (memasukkan, B1). turun: diturunkan, turunkan (menurunkan). perlu:
  diperlukan (memerlukan). lahir: dilahirkan (melahirkan). menemui: temukan
  (menemukan). habis: dihabiskan, habiskan (menghabiskan). ganti: digantikan
  (menggantikan). tunjuk: tunjukkan (menunjukkan). hadir: dihadiri
  (menghadiri). putus: putuskan, diputuskan (memutuskan). sembuh:
  disembuhkan (menyembuhkan). The corpus links these forms to the root, so
  fixing the alts changes sentences.json. Passages already link the me- verb
  (`passage_retag`: dikembalikan -> mengembalikan).
- Display-only glosses: warga, hidangan, jalan, dasar, bakar and the
  compound senses on first words (orang "(orang tua) parents", rumah "(rumah sakit) hospital"...) are
  in `tools/gloss_display.json`, merged into words.json after linking and
  example choice. In `tools/gloss_overrides.json` they changed the corpus
  build: `_idiom_pairs` reads hand glosses ("Tata Surya" linked tata), and
  gloss words steer example ranking and word rank (warga swapped an example,
  hidangan reordered words.json). Candidate: move a sense into
  gloss_overrides only when its effect on links and examples is wanted.
- Declared names split into words: a word of a multiword name is a name
  wherever it appears capitalised in that passage. p0039 was rephrased
  ("Pada hari itu") because of "Hari Kemerdekaan".
- The level budget does not count titles or numeral-like option words
  ("Setengah jam", p0018); REPORT_passages.md lists them as a note.
- luar negeri (p0043) keeps two links (outside + country), which read as a
  transparent compound.
