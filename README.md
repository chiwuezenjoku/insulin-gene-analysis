# Human INS Gene: Sequence-to-Protein Translation Verification

A first bioinformatics project verifying, computationally, that the human insulin (INS) gene's mRNA sequence translates into the correct, real preproinsulin protein — connecting DNA sequence data directly to a known, independently-documented biological result.

## Problem

Does the human INS gene's mRNA sequence actually translate into the known insulin-related protein — and can this be verified computationally, using real sequence data, rather than assumed from textbook description alone?

## Data

- **Source:** NCBI Nucleotide database, RefSeq accession **NM_000207.3** (Homo sapiens insulin (INS), transcript variant 1, mRNA)
- 465 nucleotides total, retrieved in both FASTA (raw sequence) and GenBank (annotated) formats

## Method

1. Loaded the mRNA sequence using Biopython (`Bio.SeqIO`)
2. Located the coding sequence (CDS) boundaries using NCBI's curated GenBank annotation: **positions 60–392** (1-indexed) / **59–392** (Python 0-indexed)
3. Cross-checked the CDS start position independently using a naive substring search (`.find("ATG")`), which returned a matching position (59) once indexing conventions were reconciled — confirming agreement between the curated annotation and the independent search for the start codon specifically
4. **Important caveat identified during this step:** a naive `.find("TGA")` search for the stop codon returned a position (115) that does *not* align to the correct reading frame ((115−59) is not evenly divisible by 3) — demonstrating that naive substring search is unreliable for finding stop codons, since it has no concept of reading frame. The NCBI-curated end position (392) was used instead.
5. Translated the correctly-sliced coding sequence (`record.seq[59:392]`) using Biopython's `.translate()` method
6. Verified the resulting protein sequence character-for-character against the independently-documented preproinsulin sequence on UniProt (accession **P01308**, INS_HUMAN)

## Result

**Exact match, all 110 amino acids:**
```
MALWMRLLPLLALLALWGPDPAAAFVNQHLCGSHLVEALYLVCGERGFFYTPKTRREAEDLQVGQVELGGGPGAGSLQPLALEGSLQKRGIVEQCCTSICSLYQLENYCN
```

This confirms that the NM_000207.3 mRNA sequence, translated from the correct start codon through the correct in-frame stop codon, exactly reproduces the real, independently-curated human preproinsulin sequence.

## Important Limitation

**This verifies preproinsulin — the raw, unprocessed translation product — not mature, biologically active insulin.** In real biology, preproinsulin undergoes two further processing steps before becoming active insulin: (1) removal of the N-terminal signal peptide (~24 residues) by signal peptidase, producing proinsulin, and (2) excision of the internal C-peptide by prohormone convertases, leaving the disulfide-linked A-chain and B-chain that constitute mature insulin. This project verifies the translation step only; it does not computationally model or verify the post-translational processing steps.

## Extension: Deriving Mature Insulin Chains

Building on the preproinsulin verification above, the mature insulin chains were derived by slicing the verified preproinsulin sequence at the real, curated processing boundaries (UniProt P01308, PTM/Processing section), rather than guessing or assuming boundaries.

**Curated boundaries used (UniProt, 1-indexed) → Python slice (0-indexed):**
- Signal peptide: 1–24 → `[0:24]`
- Insulin B chain: 25–54 → `[24:54]`
- C-peptide: 57–87 → `[56:87]`
- Insulin A chain: 90–110 → `[89:110]`

(Positions 55–56 and 88–89 are dibasic cleavage sites, Arg-Arg and Lys-Arg respectively, removed during real enzymatic processing and excluded from all four pieces above — consistent with known insulin biosynthesis.)

**Result — all four pieces match documented sequences exactly:**
```
Signal peptide (24 aa): MALWMRLLPLLALLALWGPDPAAA
Insulin B chain (30 aa): FVNQHLCGSHLVEALYLVCGERGFFYTPKT
C-peptide (31 aa): EAEDLQVGQVELGGGPGAGSLQPLALEGSLQ
Insulin A chain (21 aa): GIVEQCCTSICSLYQLENYCN
```

Mature, active insulin consists of the B chain and A chain, held together by disulfide bonds (documented at UniProt positions 31↔96, 43↔109, and 95↔100). This extension confirms, computationally, the full biosynthesis pathway from raw mRNA sequence through to the mature hormone's constituent chains — not just the initial translation product.

## Next Steps

- Extend the same verified workflow (sequence retrieval → annotation-guided slicing → translation → independent verification) to a second gene, to test its generalizability
- Explore known pathogenic INS gene variants and predict their effect on the translated protein or on processing (e.g., whether a mutation disrupts a cleavage site)

## Tools

Python, Biopython, NCBI Nucleotide/GenBank, UniProt
