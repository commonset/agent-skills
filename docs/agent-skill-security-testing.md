# Agent Skill Security Testing: Methodology and Results

This document records the public methodology and result boundaries for Commonset's Agent Skill security testing. It is intended to make the published numbers easier to interpret and reproduce without conflating regression coverage, semantic review, and blind generalization.

For the full narrative, including the comparison between semantic and deterministic review, see [What 250 Security Test Cases Taught Us About AI Agent Skill Security](https://commonset.ai/resources/ai-agent-skill-security-testing/).

## Systems under evaluation

We evaluate two complementary security layers:

1. **Agent Skill Security Review V2.1** — a portable semantic review procedure implemented as an Agent Skill. The Skill defines how an interpreting model should inspect an untrusted package, establish evidence, reason about trust boundaries and source-to-sink behavior, preserve uncertainty, and assign a final posture.
2. **Commonset's deterministic security scanner** — static and package-level analysis intended to produce repeatable mechanical evidence suitable for continuous policy enforcement and regression testing.

The semantic Skill is public in this repository at [`skills/agent-skill-security-review/`](../skills/agent-skill-security-review/).

## The 250 cases are two different suites

The total test program contains **250 cases**, but they should not be treated as one homogeneous benchmark.

### 151-case frozen regression suite

The frozen suite encodes established scanner contracts such as required findings, forbidden findings, severity expectations, ingestion boundaries, and package-validation behavior. Its purpose is to detect regressions in already specified behavior, not to estimate performance against unknown future attacks.

Current deterministic result: **151/151 contracts pass**.

### 99-case adversarial classification corpus

The adversarial corpus contains **99 cases** across eleven behavior families:

- host and container authority
- CI/CD
- network destinations
- supply-chain behavior
- filesystem access and persistence
- agent-control surfaces
- sensitive-data access
- obfuscation
- execution and data flow
- destructive or resource-abusive behavior
- social and approval manipulation

The expected posture distribution is:

| Expected posture | Cases |
| --- | ---: |
| Benign | 15 |
| Review | 28 |
| Dangerous | 56 |
| **Total** | **99** |

The 99-case corpus began as greenfield material for the deterministic scanner. Separately, we evaluated Agent Skill Security Review V2.1 in a fresh thread before exposing the answer key. We later used the corpus to improve the deterministic implementation, so the corpus now serves as a diagnostic regression set rather than an independent holdout.

## Semantic review result

The semantic evaluation used **Agent Skill Security Review V2.1** with **GPT-5.6 Sol** as the interpreting model in a fresh-thread evaluation. The answer key was not exposed before predictions were finalized.

The Skill produced `SAFE`, `CAUTION`, and `UNSAFE` verdicts, corresponding to the corpus labels `benign`, `review`, and `dangerous` for scoring.

| Expected | SAFE | CAUTION | UNSAFE |
| --- | ---: | ---: | ---: |
| Benign | 14 | 1 | 0 |
| Review | 1 | 26 | 1 |
| Dangerous | 2 | 13 | 41 |

Results:

- **81/99 exact three-way classifications (81.8%)**
- **95/99 binary benign-vs-risky classifications (96.0%)**
- **81/84 risky cases detected (96.4% recall)**
- **81/82 risky predictions correct (98.8% precision)**
- **14/15 benign cases remained benign (93.3% specificity)**

These numbers are not model-independent properties of the Skill. The model is part of the effective review system, so results may vary with model family, model version, execution configuration, context, and surrounding conversation.

## Initial deterministic result

Before we used the 99-case corpus to improve the scanner, the deterministic system produced:

- **37/99 exact three-way classifications (37.4%)**
- **57/99 binary benign-vs-risky classifications (57.6%)**
- **47/84 risky cases detected (56.0% recall)**
- **47/52 risky predictions correct (90.4% precision)**
- **10/15 benign cases remained benign (66.7% specificity)**

This was the original greenfield measurement for the deterministic scanner. The three-way score used the benchmark's evaluation mapping because the deterministic scanner did not natively emit the corpus's `benign`, `review`, and `dangerous` posture labels.

The later **67/99** figure does not conflict with this baseline. It was an intermediate development result recorded in Commonset PR #377 after scanner-improvement work had already begun and before the final risk-composition step. PR #377 then recorded the completed scanner at 89/99 exact. Preserving that chronology matters: **37/99 is the initial greenfield result; 67/99 is an intermediate tuned result; 89/99 is the current diagnostic result on the now-used-for-development corpus.**

## Current deterministic diagnostic result

After we used the corpus to improve deterministic package analysis, contextual interpretation, data-flow reasoning, and how the scanner considers corroborating findings together, the scanner produced the following result on the same 99 cases:

| Expected | Benign | Review | Dangerous |
| --- | ---: | ---: | ---: |
| Benign | 15 | 0 | 0 |
| Review | 0 | 28 | 0 |
| Dangerous | 1 | 9 | 46 |

Results:

- **89/99 exact three-way classifications (89.9%)**
- **98/99 binary benign-vs-risky classifications (99.0%)**
- **83/84 risky cases detected (98.8% recall)**
- **83/83 risky predictions correct (100% precision)**
- **15/15 benign cases remained benign (100% specificity)**
- **0 benign false positives**

The important qualification is that this is now a **diagnostic and regression result**, not an independent holdout estimate. The 99 cases informed scanner development, so they no longer measure generalization to unseen future material.

## Why we do not report 249/250

Combining the 151 regression contracts and 99 posture-classification cases into a single `249/250` score would imply that all 250 cases measure the same property. They do not.

The 151-case suite measures whether specified deterministic behavior has regressed. The 99-case suite measures posture classification on a corpus that began as greenfield material for the deterministic scanner but now also serves as a diagnostic regression set. The semantic result additionally depends on the interpreting model.

For that reason, we report the suites separately and preserve the chronology of when each result was obtained.

## Reproducibility principles

A credible semantic Skill evaluation should pin at least:

- the Skill version
- the model and model version
- the evaluation corpus
- the execution configuration and relevant context
- the prediction set before scoring

A credible deterministic comparison should pin at least:

- the scanner commit
- the corpus revision
- the scoring implementation and label mapping
- the evaluation command or procedure

A credible future comparison should also use a fresh holdout corpus that neither the Skill nor the deterministic implementation has been tuned against.

## What this work is intended to show

The principal conclusion is not that one review method should replace the other. Semantic review is useful for interpreting context and distributed meaning; deterministic analysis is useful for repeatable mechanical evidence and continuous enforcement. The most productive architecture is one in which semantic review can expose distinctions worth understanding, while deterministic analysis codifies the parts that can be made precise and reproducible.

- [Open Agent Skill Security Review V2.1](../skills/agent-skill-security-review/)
- [Read the full 250-case article](https://commonset.ai/resources/ai-agent-skill-security-testing/)
- [Learn about Commonset](https://commonset.ai/)
