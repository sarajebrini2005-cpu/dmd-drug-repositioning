# Computational and Pharmacological Approaches to Drug Repositioning and Peptide Binder Design in Duchenne Muscular Dystrophy

Senior capstone project, B.Sc. Genetics and Bioengineering, Istanbul Bilgi University (Spring 2025–2026).
**Best Senior Design Project Award**, Faculty of Engineering and Natural Sciences, 2026.
Presented at the 4th Conference on Student Research Projects (CSRP 2026).

Team: Abdulah Gamal, Sara Jibreini, Mustafa Ramadhan
Supervisor: Asst. Prof. Dr. Sesil Cinar

## Overview

Duchenne muscular dystrophy (DMD) is an X-linked neuromuscular disease with no curative
treatment. This project used two complementary in silico strategies to identify HDAC2-targeted
therapeutic candidates:

1. **Structure-based drug repurposing** — docking a focused library of existing/FDA-approved
   compounds against the HDAC2 catalytic pocket, followed by molecular dynamics to validate
   binding stability.
2. **De novo peptide binder design** — generating a novel HDAC2-targeted peptide binder from
   scratch using deep learning-based protein design, validated computationally.

## Part 1 — Drug repurposing

**Library construction:** ~100 candidate compounds were screened from PubChem, PubMed,
FDA sources, and ClinicalTrials.gov, narrowed to a focused library of 78 compounds (73 ligand
states successfully docked). Givinostat and vorinostat (established HDAC inhibitors) were
included as reference controls.

**Docking (AutoDock Vina):** grid centered on the catalytic Zn²⁺ ion of HDAC2 (PDB 5IX0) at
(X=6.510, Y=4.511, Z=−1.607 Å), 30×30×30 Å box, exhaustiveness 32, 9 poses/ligand.

| Rank | Compound | Vina score | Existing use |
|---|---|---|---|
| 1 | Mocetinostat | −10.6 kcal/mol | Investigational HDAC inhibitor (docking positive control) |
| 2 | CXD-101 | −9.9 kcal/mol | Investigational HDAC inhibitor |
| 3 | Montelukast | −9.9 kcal/mol | Asthma/allergy |
| 4 | **Nebivolol** | −9.8 kcal/mol | Hypertension |
| 5 | **Dantrolene** | −9.2 kcal/mol | Malignant hyperthermia, muscle spasticity |
| — | Givinostat (control) | −9.2 kcal/mol | DMD-related HDAC inhibitor |
| — | **Aripiprazole** | −8.3 kcal/mol | Antipsychotic (exploratory/novel candidate) |
| — | Vorinostat (control) | −8.6 kcal/mol | HDAC inhibitor (cutaneous T-cell lymphoma) |

Nebivolol, Dantrolene, and Aripiprazole were carried forward to molecular dynamics as
non-HDAC-labeled repurposing candidates; Mocetinostat/CXD-101 validated the docking
protocol against known inhibitors rather than being pursued further.

**Molecular dynamics (GROMACS 2026.2):** Amber99SB-ILDN (protein) + GAFF2 (ligand,
AM1-BCC charges), explicit TIP3P water, dodecahedron box (1.2 nm solute–wall buffer),
~0.15 M NaCl, 300 K / 1 bar, PME electrostatics, catalytic Zn²⁺ modeled as a restrained
nonbonded ion, ~45,500 atoms/system. Workflow: energy minimization → 500 ps NVT →
1 ns NPT → 50 ns production.

**Key MD results (final 10 ns):**

| Metric | Nebivolol | Dantrolene | Aripiprazole |
|---|---|---|---|
| HDAC backbone RMSD (nm) | 0.188 | 0.183 | 0.196 |
| Ligand heavy-atom RMSD (nm) | 0.463 | **0.282 (most stable pose)** | 0.677 (drift) |
| Mean interface contacts | 316 | 385 | 259 |
| Zn²⁺ minimum distance (nm) | **0.211 (99.98% of frames <0.35 nm)** | 0.473 | 0.662 |
| Mean H-bonds | 1.51 | 1.74 | 0.34 (declining) |

Structural stability alone ranked Dantrolene > Nebivolol > Aripiprazole. However, **Nebivolol
was selected as the principal repurposing candidate** based on the combination of strong
docking affinity, sustained catalytic-pocket/Zn²⁺ occupancy, FDA approval, and clinical
relevance (nebivolol is already explored for cardiac complications in DMD). Dantrolene was
retained as the strongest mechanistically-relevant comparator (calcium-handling via RyR1) and
Aripiprazole as an exploratory, ultimately deprioritized candidate.

## Part 2 — De novo peptide binder design

**Backbone generation (RFdiffusion):** 100 candidate 50-residue peptide backbones generated
against the HDAC2 catalytic pocket, conditioned on four hotspot residues (A146, A210, A275,
A276). Structural screening (hotspot distance, interface residue density, chain breaks, steric
clash) narrowed this to 3 top designs (28, 86, 89); a focused re-diffusion round then produced
10 refined variants.

**Sequence design (ProteinMPNN)** and **structural validation (AlphaFold/ColabFold):**

| Design | pLDDT | pTM | ipTM | Outcome |
|---|---|---|---|---|
| Design 86 (focused) | 87.7 | 0.865 | 0.244 | Deprioritized — low interface confidence |
| **Design 28** | **91.6** | **0.930** | **0.710** | Selected — strong interface confidence (ipTM > 0.6 threshold) |

Design 28 sequence (49 aa): `AEEELEERRRRARESRARLRAALAEAKALAAEIDPELGAELILQVLREMY`

**Molecular dynamics:** a 10 ns GROMACS simulation of the Design 28–HDAC2 complex showed
stable association (mean HDAC RMSD 0.139 nm, complex RMSD 0.191 nm, ~16 average
H-bonds, rising to 17 by the end). The catalytic Zn²⁺ was then structurally aligned into the
complex (alignment RMSD 0.681 Å) to build a Zn-containing model for a further 20 ns
fully-solvated simulation. The binder sits 5.82 Å from Zn²⁺ — close to the catalytic center but not
directly chelating it, consistent with a pocket-blocking mechanism rather than metal chelation.

## Key takeaways

- **Nebivolol** — best-supported repurposing candidate (docking + FDA approval + sustained Zn²⁺ proximity + clinical plausibility for DMD cardiac complications)
- **Dantrolene** — most structurally stable pose; retained as a DMD-relevant mechanistic comparator (calcium handling)
- **Aripiprazole** — novel but structurally deprioritized (progressive ligand drift, declining H-bonds)
- **Design 28** — computationally validated de novo peptide binder (ipTM 0.710), a distinct therapeutic modality from the small-molecule track
- All results are **computational hypotheses**, not experimental proof — enzyme inhibition assays and cell-based validation are the proposed next steps

## Repository structure
