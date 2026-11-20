# Kulaichev's Śrī Yantra is unique (Lean 4 + Mathlib)

Author: DICTO.

## The theorem

Draw a circle of diameter 1 and nine isosceles triangles `t1, …, t9` on one vertical axis, `t1–t5` pointing down and
`t6–t9` pointing up. Ask for the classical conditions of the Śrī Yantra in Huet's form, as given by Chiodo: the two great
triangles are inscribed, seven apexes lie on bases, and the 18 marmas (points where three lines meet) and the 6 corner
touches hold. Add A. P. Kulaichev's four conditions: the two great triangles are congruent, the corners of `t1` and of
`t9` lie on the circle, and the centre of the circle is the incentre of the innermost triangle. Finally ask that the
figure be well formed (admissible, as in DICTO's paper #103): the bases in order, every half-width positive, every apex
on the correct side of its base, and every corner in the disc. Then **exactly one figure exists**, and its slope `w7/b7`
lies in the proved interval [0.7504382776…, 0.7504387544…] (width 2⁻²¹). The value 0.75043851602221402440… given in
#103 (Theorem 10) was computed outside Lean; the Lean proof certifies only the interval, which contains it.

Kulaichev asked the uniqueness question in 1984 (without the admissibility of #103; the result settles it for
admissible figures) (*Indian Journal of History of Science* 19.3, pp. 284–285). A. Chiodo
(*Comptes Rendus Mathématique* 359, 2021, pp. 381, 387) described it as still open.

- `Challenge.lean`: the statement, with every proof `sorry`.
- `Solution.lean`: the same statement, word for word, with proofs.
- `YantraCert/`: the library behind the proofs:
  - a kernel-evaluated box checker, proved sound, and a certificate of 64,613 rational boxes in 10 charts, checked by
    `decide +kernel`;
  - a Krawczyk box, giving uniqueness by a contraction and existence by Banach's fixed-point theorem from Mathlib;
  - a verified sparse-polynomial normaliser for the identities between the geometry and the certificate;
  - the chart coverings, the case split, and the rebuilt figure.
- The modules under `YantraCert/Data` and `YantraCert/Deriv`, `YantraCert/KBoxData.lean` and a few top-level ones are
  generated. Their generators are in the study linked below.

Toolchain `leanprover/lean4:v4.35.0-rc2`, Mathlib `v4.35.0-rc2`. The two theorems depend only on the axioms `propext`,
`Classical.choice` and `Quot.sound`. Neither `sorry` nor `native_decide` is used. Every Lean file uses the module system.

## How it was made

DICTO set the question and the order of the work. A Claude Code agent (Claude Opus, Anthropic) wrote the statements, the
proofs and the generators, and ran the checks. `formalization.yaml` (section `automation`) records this in full.

## Related work

- C. S. Rao, *Śrīyantra: A Study of Spherical and Plane Forms*, *IJHS* 33.3 (1998) 203–227. It computes the same figure
  numerically, as Rao's constraints 1, 2, 8, 9, 20, and makes no claim of uniqueness.
- S.-E. Gherbi, *sri-yantra-constraint-engine* (doi:10.5281/zenodo.20617730). It reimplements Rao's constraint system and
  corrects four transcription errata in Rao's printed equations. It makes no uniqueness claim. The statement here uses
  Huet's and Kulaichev's conditions, not Rao's equations.
- `formalization.yaml` lists every source checked, with pages.

## The study

The certificate, its generators, the controls and the full check record are in the study
[studies/2026-11-15-yantra-box-certificate](https://github.com/DICTOR-Master/kaleidohedra/tree/research-fig3/studies/2026-11-15-yantra-box-certificate)
of DICTOR-Master/kaleidohedra (branch `research-fig3`). This repository holds only the Lean project, as assembled by that
study's `scripts/c14_palomar_entry.py`.

`scripts/check-lean-sources.py` and `scripts/verify-comparator.sh` come from Palomar's entry template
(PalomarRegistry). They check the source rules and run `lake comparator` the way Palomar does.

## How to cite and reuse

### How to cite

- **The paper.** DICTO, *The DICTO Yantra: Unique and Traditional, Exact but Not Constructible* (paper #103), Zenodo,
  2026. DICTO DOI: [10.5281/zenodo.24052633](https://doi.org/10.5281/zenodo.24052633) (all versions).
- **The Palomar entry.** PALOMAR-2026-11-17-000006, version 1:
  https://palomar-registry.org/entry?id=PALOMAR-2026-11-17-000006&version=1
- **The repository commit.** github.com/DICTOR-Master/kulaichev-yantra-lean, commit
  `c167cf4ed81f9e7df921ed7ce0113db874b1391b` (the commit registered on Palomar). Later commits change only metadata and
  documentation, not the Lean files.

`CITATION.cff` holds the same data in machine-readable form (GitHub shows it under "Cite this repository").

### What the library offers

The main theorems (namespace `KulaichevYantra` in `Challenge.lean` and `Solution.lean`; the same statements as
`YantraCert.Y.kulaichev_unique` and `YantraCert.Y.kulaichev_slope` in `YantraCert/Main.lean`):

- `kulaichev_unique`: `∃! y : Yantra, y.Huet ∧ y.Kulaichev ∧ y.Admissible`;
- `kulaichev_slope`: for that figure, the slope `w 6 / b 6` (`w7/b7` above) lies in [3379673547380287 / 2⁵², 3379675694863935 / 2⁵²].

Definitions (`YantraCert/Defs.lean`, namespace `YantraCert.Y`): `Yantra` (nine triangles by base depth, half-width and
apex depth), `lineThrough`, `Concurrent`, `OnCircle`, `InDisc`, `Yantra.Huet`, `Yantra.Kulaichev`, `Yantra.Admissible`.

Parts that do not depend on the yantra (namespace `YantraCert`):

- `YantraCert/Krawczyk.lean`: Krawczyk's test in slope form for `F : ι → (ι → ℝ) → ℝ` on a box `InBox c r`, any finite
  index type: `krawczyk_unique` (at most one zero in the box) and `krawczyk_exists` (a zero exists, by Mathlib's
  `ContractingWith.fixedPoint`).
- `YantraCert/Check.lean` (Lean core only), `YantraCert/Sound.lean`, `YantraCert/Cover.lean`: a branch-and-bound
  checker for sign conditions of three-variable polynomials (`NP`, `SP`) on dyadic cells (`Cell`) of the unit cube,
  evaluated by the kernel with natural numbers; soundness `SP.signMono_sound`, `checkTree_sound` and `Chart.good_of_root`.
- `YantraCert/ZPoly.lean`, `YantraCert/ZPolySound.lean`: a sparse polynomial normaliser in three variables with integer
  coefficients (`ZP`), with `ZP.eq_of_eqv`: an identity checked by the kernel holds at every real point.

### How to import it

The library builds with `leanprover/lean4:v4.35.0-rc2` and Mathlib `v4.35.0-rc2`; a project that requires it should use
the same toolchain and Mathlib. In `lakefile.toml`:

```toml
[[require]]
name = "KulaichevYantra"
git = "https://github.com/DICTOR-Master/kulaichev-yantra-lean.git"
rev = "c167cf4ed81f9e7df921ed7ce0113db874b1391b"
```

Then import the modules you need, for example:

```lean
import YantraCert.Krawczyk
import YantraCert.Main
```

### Licence

The Lean code: Apache-2.0 (see `LICENSE`). Copyright 2026 DICTO. The paper #103: CC BY-SA 4.0.

## Other work by DICTO

- ORCID (all works): https://orcid.org/0009-0001-2620-2036
- Website: https://dictospheres.com
- *The DICTO Yantra* (#103), the companion paper on this figure. DICTO DOI: [10.5281/zenodo.24052633](https://doi.org/10.5281/zenodo.24052633)
- *Silent Running: Twisted Silence* (#109). DICTO DOI: [10.5281/zenodo.24272473](https://doi.org/10.5281/zenodo.24272473)
- *Walls of Silence: The Walls of the Six Hundred* (#110). DICTO DOI: [10.5281/zenodo.24272477](https://doi.org/10.5281/zenodo.24272477)
- *DICTO: Collected Geometric Works*. DICTO DOI: [10.5281/zenodo.23345699](https://doi.org/10.5281/zenodo.23345699)
- OEIS [A400949](https://oeis.org/A400949): closed non-backtracking walks on the diamond network.
