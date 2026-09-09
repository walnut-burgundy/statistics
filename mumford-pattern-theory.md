# Mumford / pattern theory working note

Keep David Mumford and Agnes Desolneux's *Pattern Theory: The Stochastic Analysis of
Real-World Signals* in the reference set when a statistical problem is really about
**organized structure** rather than a scalar summary of observations.

This note was prompted by an image-motion problem: two frames could differ at most
pixels while the large visible forms appeared nearly stationary. A raw difference
statistic was therefore measuring local churn, not structural change.

## Ideas worth reusing

### Separate structure from texture

A signal can have coarse/geometric organization and fine oscillatory texture. A
statistic dominated by the latter may say very little about the former.

Before choosing a distance or acceptance statistic, ask what level of organization
is supposed to have changed.

### Work at several scales

Use Gaussian pyramids, wavelets, or another explicit scale decomposition when a
phenomenon has meaningful large and small structures. A result that exists only at
full resolution may be texture or noise; a result that survives coarse scales is
stronger evidence of large organization.

### Treat deformation as evidence

When the question is whether a pattern has *moved* or *changed shape*, compare
geometric organization rather than only corresponding sample values. A useful test
can estimate a displacement/deformation field and then ask:

- is displacement nontrivial?
- is it coherent across neighboring regions?
- does applying the inferred deformation make the two observations substantially
  more alike?

This is often more faithful than a global L1/L2/RGB difference.

### Do not mistake spectra for shape

Power spectra, autocorrelation, or another global frequency statistic may describe
texture while discarding important spatial organization. Use them where they answer
the question, not as automatic substitutes for geometry.

## Practical pattern for tests

For image-like or spatial signals, a simple inspectable first pass is often enough:

1. mask known singular/local nuisance regions;
2. build a small multiscale pyramid;
3. derive palette/value-insensitive coarse features such as gradients when suitable;
4. use deterministic block matching at coarse levels;
5. record displacement magnitude and neighbor coherence;
6. measure improvement after warping/alignment;
7. require the effect at more than one scale.

Prefer a test whose failure can be explained in ordinary terms ("the coarse blocks
did not move coherently") over a large opaque model when the simpler test is adequate.

## General warning

A large change statistic does not imply a large change in pattern. Keep separate
measurements for:

- state/value change;
- fine texture/statistical change;
- geometric/structural change.

When these disagree, the disagreement is information rather than something to average
away.

## Concrete calibration example

A useful first implementation came from an animated complex-field renderer. The
question was not merely whether two frames differed, but whether the visible
large-scale "soup" moved.

The test compared raw RGB change with grayscale motion remaining after several
low-pass/downsample steps. Define **structure retention** as coarse motion divided by
raw motion, summarized by the median over coarse scales.

A threshold of `0.30` separated two real running-APK cases:

| case | raw RGB mean change | median structure retention | interpretation |
| --- | ---: | ---: | --- |
| ordinary moving field | `2.822` | `0.520` | large-scale structure survives; PASS |
| eight nearly coincident repeated roots | `32.197` | `0.187` | mostly fine color/texture churn; FAIL |

The repeated-root case changed about `92.6%` of eligible pixels and had more than ten
times the raw RGB motion of the ordinary case, yet retained far less coarse structure.
This is a compact example of why "more change" and "more pattern motion" are different
claims.

Also note the time-window issue: one ordinary six-second capture happened to fail the
old raw RGB smoke test (`mean_abs_rgb=1.152`, `changed_fraction=0.032`), while another
passed (`2.822`, `0.186`) and also passed the structural gate. A single short raw
window can therefore be a brittle oracle. Prefer several windows or a sequence-level
summary rather than lowering a structurally meaningful threshold.

The low-pass retention test is still only a first approximation. A stronger follow-up
is the deformation formulation above: coarse block matching, neighboring displacement
coherence, and improvement after warping/alignment.

## Reference

David Mumford and Agnes Desolneux, *Pattern Theory: The Stochastic Analysis of
Real-World Signals*, A K Peters/CRC Press, 2010. The image material on
cartoon/texture separation, texture statistics, deformation, and multiscale analysis
is the place to revisit first.
