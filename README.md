<h1 align="center">VC-OPD</h1>

<h3 align="center">Visual Counterfactual On-Policy Distillation<br>for Grounded Vision-Language Reasoning</h3>

<p align="center">
  <strong>Jiahe Feng</strong><br>
  College of Intelligence and Computing, Tianjin University
</p>

<p align="center">
  <a href="#overview">Overview</a> &bull;
  <a href="#method">Method</a> &bull;
  <a href="#results">Results</a> &bull;
  <a href="#release-status">Release Status</a>
</p>

> **Code coming soon.** This initial release contains the project introduction and framework overview. Implementation code will be uploaded in a future update.

## Overview

**VC-OPD** is a grounding-aware on-policy distillation method for vision-language reasoning. It preserves dense teacher supervision on student-generated trajectories while prioritizing tokens whose correctness depends on image-specific evidence.

Standard on-policy distillation measures where a student disagrees with a teacher, but does not distinguish visually grounded content from generic reasoning phrases or language-prior continuations. VC-OPD addresses this by scoring the same student trajectory with a teacher under both the original image and a counterfactual image with weakened instance-specific visual evidence.

<p align="center">
  <img src="assets/framework.png" alt="VC-OPD framework: teacher-supervised warm-start followed by visual counterfactual on-policy distillation, separating factual teacher-guided update direction from grounding-aware update magnitude." width="100%">
</p>

<p align="center">
  <em>The factual teacher determines the update direction; counterfactual visual grounding controls the update strength.</em>
</p>

## Method

VC-OPD follows a two-stage training pipeline:

1. **Teacher-supervised warm-start.** Initialize the student using correctness-filtered teacher responses to stabilize response formats and reduce noisy rollouts.
2. **Student on-policy rollout.** Generate reasoning trajectories with the student under the original image.
3. **Aligned factual and counterfactual scoring.** Score the same generated tokens with the teacher under the original and perturbed images. The factual teacher-student discrepancy sets the update direction, while the factual-counterfactual teacher gap estimates token-level visual dependence.
4. **Grounding-aware optimization.** Convert visual dependence into soft token weights and apply mass-preserving normalization to stabilize the total effective weight per response. Combine these weights with the direction signal to optimize the student.

The counterfactual branch is a grounding signal, not a separate distillation target. This direction-magnitude separation emphasizes image-dependent reasoning tokens without discarding dense supervision.

## Results

The paper evaluates Qwen2.5-VL students and teachers trained on ViRL39K across seven multimodal reasoning benchmarks: Geometry3K, MathVerse, MathVista, We-Math, MMMU-Pro, LogicVista, and Super-CLEVR Counting.

| Teacher | Student | RKL Baseline | VC-OPD |
| --- | --- | ---: | ---: |
| Qwen2.5-VL-7B | Qwen2.5-VL-3B-Instruct | 46.64 | **48.87** |
| Qwen2.5-VL-32B | Qwen2.5-VL-3B-Instruct | 50.99 | **53.87** |
| Qwen2.5-VL-32B | Qwen2.5-VL-7B-Instruct | 54.76 | **57.41** |

Values are the paper-reported unweighted average exact-match accuracy (%) across the seven benchmarks under the @8 setting with inference temperature 1.0. RKL denotes reverse-KL on-policy distillation.

VC-OPD improves average accuracy in all three student-teacher settings. The paper also reports more stable training dynamics and stronger alignment between factual-counterfactual token scores and human-annotated visual grounding.

## Release Status

This repository currently contains only the project introduction and framework figure. **The implementation is not yet available** and will be added here in a future update.
