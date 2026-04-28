# Human Gut Microbiome Knowledge Base — Schema

A personal knowledge base focused on **human gut microbiome** research, following the [Karpathy LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). The LLM maintains the wiki; the human curates raw sources and asks questions.

## Architecture

```
raw/
  ├── papers/             ← academic papers, preprints (PDFs, markdown, text)
  └── articles/           ← reviews, perspectives, blog posts, news pieces

wiki/
  ├── index.md            ← master index of all wiki pages (auto-maintained)
  ├── log.md              ← append-only record of all operations
  ├── concepts/           ← core biological concepts (e.g., dysbiosis, colonization resistance)
  ├── organisms/          ← taxa, species, strains (e.g., akkermansia-muciniphila.md)
  ├── pathways/           ← metabolic & signaling pathways (e.g., scfa-production.md)
  ├── diseases/           ← disease associations (e.g., ibd.md, crc.md)
  ├── methods/            ← experimental & computational methods (e.g., 16s-rrna-sequencing.md)
  ├── entities/           ← labs, researchers, consortia, cohorts
  ├── sources/            ← one-page summaries of each raw source
  ├── comparisons/        ← side-by-side analyses across studies
  ├── synthesis/          ← synthesized overviews of topics or themes
  ├── changelog/          ← daily fetches, daily digests, weekly digests
  └── _templates/         ← templates for each page type

CLAUDE.md                 ← this file: schema & workflow rules
```

## Conventions

### File naming

- Lowercase, hyphen-separated: `bacteroides-fragilis.md`, `bile-acid-metabolism.md`
- Source summaries: `wiki/sources/<slug>.md`
- Organisms: `wiki/organisms/<genus-species>.md` or `wiki/organisms/<phylum-or-family>.md`
- Pathways: `wiki/pathways/<pathway-name>.md`
- Diseases: `wiki/diseases/<disease>.md`
- Methods: `wiki/methods/<method>.md`
- Concepts: `wiki/concepts/<name>.md`
- Entities: `wiki/entities/<name>.md`
- Daily fetches: `wiki/changelog/fetch-YYYY-MM-DD.md`
- Daily digests: `wiki/changelog/daily-YYYY-MM-DD.md`
- Weekly digests: `wiki/changelog/week-YYYY-MM-DD.md` (date = Monday of that week)

### Page structure

Every wiki page MUST include YAML frontmatter:

```yaml
---
title: "<Page Title>"
type: concept | organism | pathway | disease | method | entity | source | comparison | synthesis | fetch | digest-daily | digest-weekly
tags: [tag1, tag2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: [raw/papers/<file>, raw/articles/<file>]
related: [wiki/organisms/<page>, wiki/pathways/<page>]
---
```

### Cross-linking

- Use standard markdown links: `[Akkermansia](../organisms/akkermansia-muciniphila.md)`
- Every page must link to at least one related page
- When creating a new page, update `wiki/index.md` and all related pages

### Source attribution

- Every factual claim must trace back to a file in `raw/`
- Use inline citations: `(source: raw/papers/lloyd-price-2019-multi-omics.pdf)`

---

## Operations

### INGEST — when a new source is added to `raw/`

1. Read the new source completely
2. Identify: organisms mentioned, pathways involved, disease associations, methods used, key findings
3. Create a source summary at `wiki/sources/<slug>.md`
4. For each organism, pathway, disease, method, or concept:
   - If a wiki page exists → update with new findings, note contradictions
   - If no page exists → create one using the appropriate template
5. Update `wiki/index.md` with any new pages
6. Update relevant `wiki/synthesis/` pages if the big picture shifts
7. Add cross-links between new and existing pages
8. Append to `wiki/log.md`:
   `## [YYYY-MM-DD] INGEST raw/papers/<file> → created/updated N pages`

### QUERY — when the user asks a question

1. Read `wiki/index.md` to find relevant pages
2. Read those pages and follow cross-links
3. Synthesize an answer grounded in wiki content
4. If the answer reveals a gap, add an `## Open Questions` entry
5. If warranted, create a new `wiki/synthesis/<topic>.md`

### FETCH — daily scan of high-impact journals

Search the web for new gut microbiome publications from the past 24 hours.

**Target journals (impact factor > 5):**

| Journal | ~IF |
|---------|-----|
| Nature | 64 |
| Science | 56 |
| Cell | 64 |
| Nature Medicine | 82 |
| Nature Microbiology | 28 |
| Nature Reviews Microbiology | 69 |
| Nature Reviews Gastroenterology & Hepatology | 45 |
| Cell Host & Microbe | 30 |
| Gut | 24 |
| Gastroenterology | 29 |
| The Lancet Gastroenterology & Hepatology | 35 |
| Microbiome | 15 |
| ISME Journal | 11 |
| Genome Biology | 12 |
| Nature Communications | 17 |
| PNAS | 12 |
| Cell Reports | 8 |
| mBio | 6 |
| Nucleic Acids Research | 14 |
| eLife | 7 |
| Gut Microbes | 12 |
| mSystems | 6 |

**Procedure:**

1. Search PubMed / Google Scholar / journal RSS for gut-microbiome-related papers published in the past 24 hours from the journals above
2. For each relevant paper found, record:
   - **Title** — exact paper title
   - **Journal** — journal name
   - **Highlights** — 2–3 sentence summary of the key finding
   - **Link** — DOI or URL to the paper
3. Write results to `wiki/changelog/fetch-YYYY-MM-DD.md` using the fetch template
4. Do NOT auto-ingest. The human decides which papers to add to `raw/`

### LINT — periodic health check

1. Scan all wiki pages for:
   - Broken cross-links
   - Pages missing `sources` frontmatter
   - Stale pages (updated >30 days ago on active topics)
   - Contradictions between pages (e.g., conflicting abundance data for a species)
2. Report findings; auto-fix where possible
3. Append results to `wiki/log.md`

### DAILY DIGEST — end of each active day

1. Collect all pages created or updated today (including any fetches)
2. Summarize: what was ingested, concepts refined, open questions added
3. Note contradictions found between sources
4. Write to `wiki/changelog/daily-YYYY-MM-DD.md`
5. Update `wiki/index.md`

### WEEKLY DIGEST — every Monday (covering prior Mon–Sun)

1. Collect all daily digests and fetches from the past 7 days
2. Identify recurring themes: organisms, pathways, disease areas
3. Summarize key learnings, changed views, unresolved tensions
4. List top open questions and suggested next reads
5. Write to `wiki/changelog/week-YYYY-MM-DD.md`
6. Update `wiki/index.md`

---

## Scope

This knowledge base covers:

- **Gut microbiome composition**: taxa profiling, enterotypes, core vs. variable microbiome, strain-level variation
- **Metabolomics & metabolic pathways**: short-chain fatty acids (SCFAs), bile acid metabolism, tryptophan metabolism, amino acid fermentation
- **Host–microbe interactions**: immune modulation, epithelial barrier, gut-brain axis, gut-liver axis
- **Disease associations**: IBD (Crohn's, UC), colorectal cancer, obesity & metabolic syndrome, diabetes (T1D/T2D), IBS, C. difficile infection, liver disease (NAFLD/NASH)
- **Therapeutics & interventions**: probiotics, prebiotics, synbiotics, FMT, phage therapy, postbiotics, dietary interventions
- **Methods & technologies**: 16S rRNA sequencing, shotgun metagenomics, metatranscriptomics, metabolomics, culturomics, gnotobiotic models, organoids, spatial transcriptomics
- **Computational methods**: microbiome bioinformatics (QIIME2, MetaPhlAn, HUMAnN), statistical analysis, machine learning on microbiome data, multi-omics integration
- **Key organisms**: Bacteroides, Firmicutes, Akkermansia, Faecalibacterium, Prevotella, Bifidobacterium, Lactobacillus, Clostridioides difficile, and others
- **Cohorts & databases**: Human Microbiome Project (HMP), MetaHIT, American Gut Project, TEDDY, curatedMetagenomicData

## Templates

See `wiki/_templates/` for page templates.
