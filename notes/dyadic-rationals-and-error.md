# Dyadic rationals and statistical error

A statistical calculation does not need decimal notation as its mathematical foundation.

Use the structure of the quantity first; choose a machine approximation only when computation requires one.

## Exact quantities

Keep exact when the input/model gives exact structure:

- counts as integers;
- proportions and design ratios as rationals;
- binary fractions such as 1/2, 1/4, 1/8 as dyadics;
- thirds and other small ratios as rationals rather than decimal expansions.

Decimal rendering is a presentation choice.

## Approximate quantities

Not every statistical result is rational. Distribution functions, likelihoods, roots, integrals, and fitted parameters may require approximation.

Represent that fact explicitly:

```
mathematical quantity
    -> chosen numerical approximation
    -> numerical error bound / tolerance
    -> reported presentation
```

Do not merge numerical roundoff into sampling error, measurement error, model misspecification, or other inferential uncertainty.

## Existing implementation precedent

- The HoTT Book (`HoTT/book@578b85cc8d586b1677ec4335148adeb443057d24`) identifies dyadics `n / 2^k` as an approximate field suitable for constructive computer implementation.
- Lean 4 core (`leanprover/lean4@2c2bdd9630a7a6c51d7620d5efefcdba104f38f3`) uses a canonical odd-numerator/binary-exponent dyadic representation, exact ring operations, and explicit precision-bounded inverse/division.
- ConwayHs (`ming-t18/ConwayHs@d80a4ced80527c28306c781b60ae560975ab394a`) independently uses an arbitrary-precision `n / 2^p` representation. Its repository has no declared license at the pinned revision, so it is cited rather than copied.

## Consequence for error propagation

The error budget should have separate terms for:

- data/measurement uncertainty;
- model/identification uncertainty;
- algorithmic approximation;
- machine rounding;
- display/reporting resolution.

A wider machine float can reduce only the machine-rounding term. It cannot repair a wrong model or create information absent from the measurements.

Applied versions of this rule are recorded in `bl4ckb4ll/econometrician#61` and `#62`. Compiler/backend implications are recorded in `isomorphisms/idric-arm-thumb#83` and `#84`.
