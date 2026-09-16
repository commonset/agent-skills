# Commonset Agent Skills

Open, reusable Agent Skills for secure software delivery and AI capability governance.

This repository contains portable skills that follow the open [Agent Skills specification](https://agentskills.io/specification). Each skill is a directory with a `SKILL.md` entry point and, where useful, supporting references. The skills are useful on their own; Commonset is not required to use them.

## Skills

| Skill | Purpose |
| --- | --- |
| [`agent-skill-security-review`](skills/agent-skill-security-review/) | Review an Agent Skills package for security-relevant behavior, trust boundaries, evidence, and review questions before installation, sharing, approval, or publication. |
| [`pull-request-review`](skills/pull-request-review/) | Review a code change for correctness, security, regressions, missing tests, maintainability, and material performance issues. |
| [`release-readiness`](skills/release-readiness/) | Assess whether a change is ready to ship across data, configuration, security, observability, rollout, rollback, and smoke coverage. |

## Agent Skill security research

The [`agent-skill-security-review`](skills/agent-skill-security-review/) skill is the portable semantic-review layer we use when examining untrusted Agent Skills packages. It is designed to distinguish security-relevant capability from evidence of an actual attack path, while treating the package under review as hostile evidence rather than as instructions to follow.

We evaluated **Agent Skill Security Review V2.1** with **GPT-5.6 Sol** against a 99-case greenfield adversarial corpus, then compared those results with Commonset's deterministic security scanner. On that initial corpus, V2.1 + GPT-5.6 Sol produced **81/99 exact benign/review/dangerous classifications** and **95/99 correct benign-vs-risky classifications**. The original deterministic scanner produced **37/99 exact** and **57/99 benign-vs-risky** classifications on the same material.

We subsequently used what those cases exposed to improve the deterministic scanner. Its current diagnostic result on the same 99 cases is **89/99 exact**, **98/99 benign-vs-risky**, with **0 benign false positives**, while the separate **151-case frozen regression suite remains 151/151**. Because those 99 cases informed scanner development, the current deterministic result is a regression/diagnostic measurement rather than a blind estimate of future performance. Model-driven Skill results may also vary by model, version, execution environment, and context.

- [Read the full 250-case write-up](https://commonset.ai/resources/ai-agent-skill-security-testing/)
- [Review the testing methodology and result boundaries](docs/agent-skill-security-testing.md)
- [Open Agent Skill Security Review V2.1](skills/agent-skill-security-review/)

## Design principles

These skills are intentionally small and provider-independent.

- **Evidence over intuition.** Material findings should point to the behavior and source that support them.
- **Precision over noisy warnings.** Retrieval is not disclosure, access is not execution, examples are not actions, and prohibitions are not requests.
- **Progressive disclosure.** Keep the main `SKILL.md` focused and move deeper guidance into references when useful.
- **No hidden dependencies.** A skill should say when it depends on repository context, external tools, credentials, or supporting files.
- **Useful without Commonset.** These are ordinary Agent Skills packages, not a Commonset-specific format.

## Using these skills

Install or copy a skill directory using the mechanism supported by your agent or development environment. Agent Skills clients use different discovery locations, so follow the client documentation for the environment where you want to use the skill.

The portable unit is the skill directory itself. For example:

```text
skills/
  pull-request-review/
    SKILL.md
```

## Commonset

A skill is one implementation of a broader organizational capability. [Commonset](https://commonset.ai/) provides the control plane around that capability: identity, ownership, versions, provenance, trust, review, access, and distribution across AI platforms and repositories.

- [AI Skills Repository & Registry](https://commonset.ai/ai-skills-registry/)
- [Security & Trust](https://commonset.ai/security/)
- [Resources](https://commonset.ai/resources/)

## License

MIT. See [LICENSE](LICENSE).
