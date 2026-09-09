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

## Papers actually consulted for this note

This is intentionally not an exhaustive Mumford bibliography. These are the papers or
manuscripts actually consulted while building the note and the renderer test. Direct
Brown/DAM copies are used where available.

- David Mumford and Jayant Shah, **"Optimal Approximations by Piecewise Smooth
  Functions and Associated Variational Problems,"** *Communications on Pure and
  Applied Mathematics* 42 (1989), 577-685.
  https://www.dam.brown.edu/people/mumford/vision/papers/1989c--Mumford-Shah-Wiley.pdf
  Useful for: separating coarse piecewise organization and edges from fine image
  detail; segmentation as a structural rather than pointwise problem.

- David Mumford, **"Mathematical Theories of Shape: Do They Model Perception?"**
  SPIE *Geometric Methods in Computer Vision* 1570 (1991), 2-10.
  https://www.dam.brown.edu/people/mumford/vision/papers/1991d--MathThShape-DAM.pdf
  Useful for: multiscale shape/signal analysis and the problem of defining similarity
  so it reflects perceived shape rather than raw sample equality.

- David Mumford, **"Pattern Theory: A Unifying Perspective,"** first European
  Congress of Mathematics (1994), revised in *Perception as Bayesian Inference*
  (1996), 25-62.
  https://www.dam.brown.edu/people/mumford/vision/papers/1994c-96--PattThUnifyingPersp-NC.pdf
  Useful for: pattern variation through domain warping/deformation and the inadequacy
  of simplistic pointwise/Gaussian models for naturally varying signals.

- Song Chun Zhu and David Mumford, **"Learning Generic Prior Models for Visual
  Computation,"** CVPR (1997), 463-469.
  https://www.dam.brown.edu/people/mumford/vision/papers/1997a--LearningPriors-Zhu-IEEE.pdf
  Useful for: empirical natural-image statistics, learned filters, and explicit
  concern with scale invariance.

- Song Chun Zhu and David Mumford, **"Prior Learning and Gibbs Reaction-Diffusion,"**
  *IEEE Transactions on Pattern Analysis and Machine Intelligence* 19(11) (1997),
  1236-1250.
  https://www.dam.brown.edu/people/mumford/vision/papers/1997b--PriorLGibbsR-D-Zhu-IEEE.pdf
  Useful for: connecting learned image statistics to multiscale image-processing
  dynamics.

- Song Chun Zhu, Yingnian Wu, and David Mumford, **"Filters, Random Fields and
  Maximum Entropy (FRAME): Towards a Unified Theory for Texture Modeling,"**
  *International Journal of Computer Vision* 27 (1998).
  https://www.dam.brown.edu/people/mumford/vision/papers/1998b--Frame-ZhuWu-journal.pdf
  Useful for: understanding what filter-response statistics can say about texture,
  while keeping texture statistics conceptually separate from geometry.

- Jinggang Huang and David Mumford, **"Statistics of Natural Images and Models,"**
  CVPR (1999), 541-547.
  https://www.dam.brown.edu/people/mumford/vision/papers/1999c--ImageStats-Huang-IEEE.pdf
  Useful for: empirical multiscale/near-scale-invariant statistics and Haar/wavelet
  response distributions.

- David Mumford, **"Pattern Theory: The Mathematics of Perception,"** ICM 2002.
  https://www.dam.brown.edu/ptg/REPORTS/02-10.pdf
  Useful for: a compact mathematical account of pattern theory, noisy/incomplete
  signals, repeated structure with variation, and analysis by generative models.

- Ann B. Lee, Kim S. Pedersen, and David Mumford, **"The Nonlinear Statistics of
  High-Contrast Patches in Natural Images,"** *International Journal of Computer
  Vision* 54 (2003), 83-103.
  https://www.dam.brown.edu/people/mumford/vision/papers/2003a--Stats-ALeePedersen-journal.pdf
  Useful for: local image patches concentrating near nonlinear low-dimensional
  geometric structures, and the failure of marginal summaries to capture the full
  organization of image data.

- David Mumford, **"Empirical Statistics and Stochastic Models for Visual Signals,"**
  in *Brain and Systems: New Directions in Statistical Signal Processing* (2006).
  https://www.dam.brown.edu/people/mumford/vision/papers/2006d--SurveyStochModels-PrfShts.pdf
  Useful for: the broadest supporting survey of natural-image statistics, filters,
  wavelets, local primitives, scale, and stochastic image models.

### Archive indexes for further reading

These are discovery indexes rather than paper citations:

- image statistics: https://www.dam.brown.edu/people/mumford/vision/stats.html
- pattern theory: https://www.dam.brown.edu/people/mumford/vision/pattern.html
- shape: https://www.dam.brown.edu/people/mumford/vision/shape.html
- segmentation/parsing: https://www.dam.brown.edu/people/mumford/vision/segment.html

Keep future additions honest: mark a paper as **consulted** only after actually reading
it for the problem. Put promising-but-unread papers in a separate reading list.

## Reference

David Mumford and Agnes Desolneux, *Pattern Theory: The Stochastic Analysis of
Real-World Signals*, A K Peters/CRC Press, 2010. The image material on
cartoon/texture separation, texture statistics, deformation, and multiscale analysis
is the place to revisit first.
