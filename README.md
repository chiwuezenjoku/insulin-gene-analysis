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

## Next Steps

- Computationally identify and excise the signal peptide and C-peptide regions from the preproinsulin sequence to derive the predicted mature A-chain and B-chain sequences, and verify those against documented mature insulin chain sequences
- Extend the same verified workflow (sequence retrieval → annotation-guided slicing → translation → independent verification) to a second gene, to test its generalizability
- Explore known pathogenic INS gene variants and predict their effect on the translated protein

## Tools

Python, Biopython, NCBI Nucleotide/GenBank, UniProt
