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

## Reference

David Mumford and Agnes Desolneux, *Pattern Theory: The Stochastic Analysis of
Real-World Signals*, A K Peters/CRC Press, 2010. The image material on
cartoon/texture separation, texture statistics, deformation, and multiscale analysis
is the place to revisit first.
