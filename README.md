# rule30-constant-column-lean

A complete Lean 4 proof, using only core Lean (no Mathlib), that **no nonzero finite Rule 30 configuration can keep its
center column constant for more than `A + 2` steps**, where the configuration is supported in `[-A, A]`.

```lean
theorem no_const_column (x : Int → Bool) (A : Nat)
    (hz : ∀ i : Int, i < -(A : Int) ∨ (A : Int) < i → x i = false)
    (hne : ∃ i, x i = true) (c : Bool) (hc : ∀ t, t ≤ A + 2 → evo x t 0 = c) : False
```

The update rule is `x'(i) = x(i-1) xor (x(i) or x(i+1))`. The bound is sharp: the single black cell (`A = 0`) holds a
constant center column on times `0..1` and not on `0..2`, and a SAT search finds the first impossible horizon is exactly `A + 2`
for every `A` up to 24. Sanity examples in the file check that the definitions reproduce the known center column `1 1 0 1 1 1 0 0`.

## What this is, and is not

- It is an **independent re-derivation** of the horizon bound in D. L. Condrey, *Finite Configurations Cannot Generate a Constant Trace in Rule 30*,
  [arXiv:2609.09431](https://arxiv.org/abs/2609.09431) (8 September 2026), which proves that no column of a nonzero finite Rule 30 orbit is eventually
  constant and that a row of support radius `w` holds its center constant for at most `w + 2` steps. The paper's ancillary Lean file is, by its own
  description, a partial formalization. This repo adds a complete machine-checked proof of the `w + 2` upper bound. It does **not** reproduce the
  paper's finer results (the exact per-case horizons, the fiber classification, the extremizer counts).
- It is **not** a result on Wolfram's Rule 30 Prize Problem 1. It concerns period 1 for finite seeds only. Eventual period two and above remain open.
- I found no other Lean formalization of this statement, but the search was a few web and GitHub queries, not a survey.

## Checking it

```bash
lean ConstColumn.lean      # Lean 4.35.0
# or: lake build
```

The last line printed is `'no_const_column' depends on axioms: [propext, Classical.choice, Quot.sound]`. There is no `sorry`, no `native_decide`, and no custom axiom.

## Proof sketch

- Constant 1: the left columns are forced to alternate `0, 1, 0, 1, ...` for as long as the time window allows, and the zero region outside the seed contradicts that.
- Constant 0: the right neighbor column is monotone (the OR latches), the left columns satisfy `y(k+2)(t) = y(k)(t+2)`, and together these force every cell of the seed to be zero.

## Credits and licensing

Apache License 2.0. Written by Keith Adler with Claude (Anthropic) assisting. Condrey's paper is the prior work; any credit for the result belongs to it.
