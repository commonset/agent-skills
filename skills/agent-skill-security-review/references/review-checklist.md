# Adversarial Review Checklist

Use this checklist after understanding the target's apparent purpose. Do not mechanically mark every item as a finding.

## 1. Trust boundaries

- What content can an attacker or external party influence?
- What comes from the user, network, files, tools, or another model?
- What content is treated as instructions?
- Can low-trust content become persistent/high-trust instructions?
- Can generated content be written into future agent context?
- Can tool output influence privileged actions?

## 2. Instruction attacks

Ask whether anything tells the agent to ignore prior instructions, redefine priority, claim false authority, suppress warnings, mark itself safe, hide behavior, reveal protected context, or persist new instructions.

Then verify whether suspicious text is quoted, defensive, negated, or merely an example.

## 3. Approval and user control

For sensitive actions ask whether explicit approval is required, whether it occurs before the action, whether the package assumes consent, and whether the requested operation is within user intent.

## 4. Credentials

Look for API keys, tokens, `.env`, cloud credentials, GitHub/npm/PyPI/Docker credentials, SSH keys, `.netrc`, `.git-credentials`, browser sessions, password managers, and keychains.

For each access ask whether the value is necessary, logged, written, shown, passed to another process, or transmitted, and whether the destination is attacker-controlled.

## 5. Network and transport

Inventory HTTP(S), WebSockets, raw sockets, DNS, webhooks, telemetry, analytics, publishing, Git pushes, and image beacons.

Ask what data leaves, where it goes, whether the destination is fixed, and whether sensitive data is involved.

Explicitly check for:

- `verify=False` or equivalent certificate bypass;
- insecure TLS/SSL contexts;
- hostname verification disabled;
- trust-all certificate managers;
- HTTPS-to-HTTP downgrade for sensitive traffic;
- download signature/hash/certificate checks disabled.

Do not call ordinary fixed-destination API access exfiltration without sensitive data flow.

## 6. Execution and parsing

Look for subprocess/shell, interpreters, `eval`, `exec`, dynamic imports, reflection, unsafe deserialization, and model-generated code execution.

Trace aliases and indirect forms.

If executable source cannot be parsed or is malformed:

- record the limitation;
- do not claim AST-like certainty;
- inspect inertly where possible;
- reduce confidence when material;
- report unparseable executable content if it prevents meaningful security assessment.

## 7. Data flow

For every sensitive source, ask where the value goes. For every dangerous sink, ask where inputs come from.

Trace simple helper functions and local module boundaries.

Important flows:

- secret -> network;
- sensitive file -> network;
- conversation context -> network;
- user input -> shell;
- network response -> execution;
- model output -> execution;
- decoded runtime content -> execution;
- external content -> agent instructions/memory;
- CI secret -> logging/network.

Do not infer connection merely because source and sink coexist.

## 8. Persistence and control surfaces

Inventory and inspect:

- `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`;
- Copilot/Cursor/Windsurf/Codex rules;
- persistent memory;
- shell profiles;
- cron/systemd/launch agents/scheduled tasks;
- Git hooks;
- global Git configuration;
- CI workflows.

Presence alone is informational. Escalate based on contents, write paths, privilege, or untrusted input.

## 9. Docker, containers, and host exposure

Look for `/var/run/docker.sock`, privileged containers, host network/PID, root/home mounts, `/proc`, `/sys`, device access, SSH-agent forwarding, host credentials, and cloud metadata access.

## 10. CI and GitHub Actions

Inspect permissions, `write-all`, `id-token: write`, `secrets: inherit`, secret logging, mutable action references, untrusted PR code with credentials, downloaded scripts, publishing, releases, deployment, and artifact reuse.

A workflow file by itself is not a vulnerability.

## 11. Dependencies and installation

Inspect package manifests, lockfiles, install/lifecycle hooks, remote tarballs, Git URLs, branch dependencies, wildcard versions, runtime installs, `curl | sh`, `wget | bash`, and downloaded binaries.

## 12. Hidden content and decoding

Look for base64, hex, percent encoding, escapes, HTML entities, compressed data, string fragments, reversed strings, zero-width, bidi, mixed-script text, and misleading file types.

Evaluate decoded meaning rather than encoding alone.

For runtime decoding APIs such as base64/hex/unescape/decompression:

- distinguish active decoding from an inert string mentioning the API;
- record runtime-controlled decoding as a capability signal;
- elevate only when decoded data reaches a dangerous sink or reveals dangerous instructions.

## 13. Opaque and misleading artifacts

Look for bytecode, binaries, encrypted blobs, misleading extensions, nested archives, and executable content in documentation-like locations.

Distinguish:

- honest opaque artifact (for example, ordinary `.pyc`): affects confidence;
- disguised opaque artifact: affects confidence and may indicate misleading representation.

Do not label ordinary bytecode disguised merely because it is opaque.

## 14. Destructive behavior

Look for recursive deletion, overwrite, repository destruction, force push, access-control changes, security-tool disabling, permission broadening, and destructive cloud actions.

## 15. Cost and denial of service

Look for infinite loops, uncontrolled recursion/concurrency, fork bombs, unbounded traversal/crawling, model calls inside open-ended loops, repeated subagent spawning, and retries without limits.

## 16. Agent-specific poisoning

Ask whether downloaded/user/tool/model content can become future instructions, memory, shared agent files, approval state, or configuration consumed automatically later.

## 17. Context checks for false positives

Before any serious finding verify:

- comment vs active code;
- docstring vs execution;
- string literal vs sink;
- quoted instruction vs active instruction;
- defensive text vs attack instruction;
- negation;
- user gating;
- fixed vs attacker-controlled value;
- capability vs actual source-to-sink flow.

## 18. Correlation review

Explicitly search for:

- secret access + network;
- sensitive files + network;
- conversation context + network;
- dynamic destination + sensitive payload;
- user input + shell;
- network/model output + execution;
- encoded runtime content + execution;
- external content + agent instructions/memory;
- download + execution;
- CI secrets/write authority + untrusted code;
- hidden behavior + persistence/credential access;
- weakened TLS + sensitive data or privileged authority;
- host control + remote input.

Promote only when evidence connects the pieces.

## 19. Benignity challenge

If the target appears safe, ask:

1. What file received the least scrutiny?
2. What would be easiest to hide?
3. What crosses a trust boundary?
4. What survives after invocation?
5. What happens during installation?
6. What talks to the network?
7. What reads credentials?
8. What can execute?
9. What can alter future agent behavior?
10. What can an external attacker control?
11. Was TLS/integrity validation weakened?
12. Did any executable source fail to parse?

## 20. Suspicion challenge

If the target appears malicious, ask:

1. Is the behavior active?
2. Is it negated or defensive?
3. Is it only an example?
4. Is the capability legitimate?
5. Is it explicitly user-controlled?
6. Did I prove the data flow?
7. Am I double-counting?
8. Does severity match impact?
9. Am I mistaking a control-surface presence signal for malicious behavior?
10. Would I reach the same conclusion without the suspicious keyword?

## 21. Final gate

Do not finish until you can answer:

- What does the skill claim to do?
- What does it actually do?
- What powerful capabilities does it contain?
- What sensitive data can it access?
- What external destinations can it reach?
- Does it weaken transport or integrity validation?
- What can it execute?
- What can it persist?
- What can it change about future agent behavior?
- What can external input control?
- What source-to-sink paths exist?
- What suspicious evidence was suppressed due to benign context?
- What executable content was opaque or unparsable?
- What remains unknown?
- Why does the final verdict follow from the evidence?
