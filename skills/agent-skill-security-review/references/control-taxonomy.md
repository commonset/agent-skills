# Agent Skill Security Review - Public Control Taxonomy

Use these public control IDs to classify findings consistently. They describe security behavior, not proprietary detector implementation.

A control can represent an observed surface, capability, weakness, behavior, data flow, or attack path. Use context, severity, confidence, and flow evidence to distinguish them.

## 1. Instruction and agent control

### ASR-INST-001 - Instruction hierarchy manipulation
Active attempts to override, ignore, weaken, reorder, or impersonate higher-priority instructions. Suppress defensive examples and clear negations.

### ASR-INST-002 - Approval or consent bypass
Attempts to perform sensitive actions without required approval or treat missing consent as approval.

### ASR-INST-003 - Covert or deceptive behavior
Instructions to conceal actions, suppress warnings, misrepresent results, or hide security-relevant activity.

### ASR-INST-004 - Protected context extraction
Attempts to obtain or reveal system instructions, developer instructions, hidden policy, private context, or unrelated memory.

### ASR-INST-005 - Persistent instruction modification
Modification of persistent agent-control surfaces such as `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, Copilot/Cursor/Windsurf rules, agent memory, or global agent configuration.

### ASR-SURF-001 - Agent instruction control surface present
An agent instruction file or rule surface exists and should be reviewed. Informational unless contents or write paths create risk.

## 2. Credentials and sensitive information

### ASR-CRED-001 - Credential or secret access
Access to passwords, API keys, bearer tokens, `.env` values, SSH keys, cloud credentials, package-manager credentials, browser sessions, password managers, or keychains.

### ASR-CRED-002 - Credential disclosure
Credentials revealed to output, logs, unrelated files, users without need, or other processes.

### ASR-CRED-003 - Credential transmission
Credentials transmitted to a network destination or third-party system.

### ASR-DATA-001 - Sensitive local data access
Access to sensitive local data beyond ordinary project content.

### ASR-DATA-002 - Sensitive agent/user context collection
Access to sensitive conversation, organizational, model, or private user context.

## 3. Execution

### ASR-EXEC-001 - Process or shell execution
Active operating-system process or shell execution.

### ASR-EXEC-002 - Dynamic code evaluation
Active `eval`, `exec`, compilation, or reflective code execution.

### ASR-EXEC-003 - Dynamic module or code loading
Runtime module/code loading where the target may be influenced by untrusted data.

### ASR-EXEC-004 - Unsafe deserialization
Unsafe loading of serialized or structured data capable of execution or unsafe object construction.

### ASR-EXEC-005 - Remote or generated code execution
Execution of downloaded, model-generated, decoded, or externally supplied code.

### ASR-EXEC-006 - Opaque executable artifact
Bytecode, binaries, encrypted executable blobs, or other executable content that cannot be meaningfully reviewed. Primarily affects confidence unless other evidence shows danger.

### ASR-EXEC-007 - Unparseable executable source
Malformed, truncated, or otherwise unparsable executable source that materially prevents structural security analysis. Not automatically malicious; may require reduced confidence or partial coverage.

## 4. Network and transport

### ASR-NET-001 - Outbound network capability
Ability to make outbound requests. Capability only; not automatically a vulnerability.

### ASR-NET-002 - Sensitive data reaches outbound sink
Supported source-to-sink path where sensitive information reaches an external destination.

### ASR-NET-003 - Dangerous or externally controlled destination
Networking to user-controlled hosts, cloud metadata, localhost, link-local, private networks, or sensitive internal services.

### ASR-NET-004 - Undisclosed telemetry or beaconing
Hidden or unnecessary analytics, tracking, telemetry, image beaconing, DNS signaling, or similar outbound behavior.

### ASR-NET-005 - Transport security weakened
TLS/certificate/hostname verification is disabled or downgraded, or integrity/signature checks on remote content are bypassed.

## 5. Filesystem and destructive behavior

### ASR-FILE-001 - Filesystem boundary escape
Traversal, unsafe absolute paths, symlink/hardlink abuse, or unexpected writes outside intended scope.

### ASR-FILE-002 - Destructive operation
Deletion, overwrite, corruption, or destructive modification with meaningful impact.

### ASR-FILE-003 - Unsafe permission change
Unnecessarily broad permissions or changes enabling unauthorized access or execution.

### ASR-FILE-004 - Sensitive global configuration modification
Writes to security-sensitive global or user-wide configuration.

## 6. Persistence

### ASR-PERSIST-001 - Shell or login persistence
Shell profiles, login configuration, or startup scripts modified for persistence.

### ASR-PERSIST-002 - Scheduled/system persistence
Cron, systemd, launch agents, scheduled tasks, registry startup, or equivalent mechanisms.

### ASR-PERSIST-003 - Repository persistence
Git hooks or hidden repository startup/control mechanisms used for persistence.

### ASR-PERSIST-004 - Agent persistence
Persistent AI-agent instructions, configuration, rules, or memory modification.

### ASR-SURF-002 - Repository/CI persistence surface present
A Git hook, CI workflow, or similar persistent repository control surface exists. Informational unless behavior or privilege creates risk.

## 7. Host and sandbox exposure

### ASR-HOST-001 - Container-control access
Access to Docker, containerd, Podman, Kubernetes administration, or equivalent host-control interfaces.

### ASR-HOST-002 - Privileged execution environment
Privileged containers or equivalent excessive host capability.

### ASR-HOST-003 - Sensitive host mount or namespace sharing
Host root/home mounts, host network/PID namespace, `/proc`, `/sys`, devices, or SSH-agent forwarding.

### ASR-HOST-004 - Sandbox or isolation escape
Behavior designed to break or bypass intended isolation.

## 8. CI/CD and repository authority

### ASR-CI-001 - Excessive workflow permission
Unnecessary repository or organization write authority.

### ASR-CI-002 - Sensitive identity-token authority
`id-token: write` or equivalent workload-identity authority.

### ASR-CI-003 - Secret exposure in CI
CI secrets reach logs, untrusted code, or unsafe network sinks.

### ASR-CI-004 - Untrusted code with privileged CI context
Attacker-influenced code executes with meaningful credentials or write privileges.

### ASR-CI-005 - Deployment, publication, or release authority
Automated authority to deploy, publish, push images, release, or modify protected repositories.

### ASR-SURF-003 - CI workflow control surface present
A workflow or CI control surface exists and should be reviewed. Informational by itself.

## 9. Supply chain

### ASR-SUPPLY-001 - Mutable or unpinned executable dependency
Executable dependency or action loaded from mutable references where integrity matters.

### ASR-SUPPLY-002 - Remote dependency or executable artifact
Direct use of packages, Git repositories, tarballs, binaries, or scripts from remote locations.

### ASR-SUPPLY-003 - Install or lifecycle execution
Package lifecycle hooks or installation processes automatically execute code.

### ASR-SUPPLY-004 - Download-and-execute chain
Remote content is downloaded and then passed to a shell, interpreter, loader, or executable environment.

## 10. Obfuscation and hidden behavior

### ASR-OBF-001 - Encoded hidden content
Behavior concealed through base64, hex, percent encoding, escapes, compression, or reconstructed strings. Encoding alone is not malicious.

### ASR-OBF-002 - Unicode concealment
Zero-width characters, bidi controls, suspicious homoglyphs, or misleading mixed scripts.

### ASR-OBF-003 - Misleading representation
Executable or security-sensitive content hidden behind misleading filenames, extensions, formatting, or presentation.

### ASR-OBF-004 - Runtime decoding capability
Active decoding or reconstruction of runtime-controlled content. Usually informational until correlated with a dangerous sink.

## 11. Resource and cost abuse

### ASR-DOS-001 - Unbounded compute or process behavior
Unbounded loops, recursion, process creation, or work generation.

### ASR-DOS-002 - Unbounded filesystem or network traversal
Uncontrolled crawling, scanning, traversal, or request generation.

### ASR-DOS-003 - Unbounded model/tool cost
Autonomous model or tool invocation capable of uncontrolled cost or activity.

## 12. Social engineering and deception

### ASR-SOC-001 - Credential solicitation or phishing behavior
Deceptive attempts to persuade users to provide credentials or sensitive data.

### ASR-SOC-002 - Authority impersonation
Misrepresentation of identity, authority, security requirements, or organizational policy.

## 13. Correlated attack paths

### ASR-FLOW-001 - Sensitive data -> network
A sensitive source reaches an outbound network sink.

### ASR-FLOW-002 - Untrusted input -> execution
User, network, file, or other untrusted input reaches code/process execution.

### ASR-FLOW-003 - Untrusted input -> persistent agent control
Untrusted content reaches persistent agent instructions, memory, rules, or configuration.

### ASR-FLOW-004 - Remote content -> execution
Downloaded or remotely retrieved material reaches executable behavior.

### ASR-FLOW-005 - Hidden payload -> dangerous sink
Encoded or concealed content reaches execution, persistence, credential handling, or another consequential sink.

### ASR-FLOW-006 - CI secret/authority -> untrusted sink
Privileged CI context reaches attacker-influenced code, logging, networking, or another unsafe sink.

### ASR-FLOW-007 - User-controlled destination + sensitive payload
Sensitive data is transmitted to a destination controlled by user or external input.

### ASR-FLOW-008 - Weakened transport + sensitive data/authority
Sensitive data or privileged authority crosses a channel whose certificate, hostname, encryption, or integrity validation has been disabled or materially weakened.

## Context modifiers

Use these modifiers rather than inventing new controls:

- `active`: operational/reachable behavior;
- `defensive`: behavior described only to prevent it;
- `negated`: content explicitly prohibits the action;
- `example`: documentation/demo/quoted material;
- `user_gated`: behavior requires explicit user initiation or confirmation;
- `hidden`: behavior is materially obscured;
- `opaque`: content cannot be adequately inspected;
- `control_surface`: a security-relevant file/surface exists without evidence that it is dangerous.

## Evidence tiers

### Tier 1 - Indicator
Suspicious word, API, path, file, or construct exists. Do not infer an attack from this alone.

### Tier 2 - Surface or capability
A security-relevant control surface exists or the package can perform a sensitive action.

### Tier 3 - Behavior
Evidence demonstrates how or when the capability is used.

### Tier 4 - Data flow
Evidence connects a meaningful source to a sink.

### Tier 5 - Attack path
Multiple behaviors form a credible chain producing unauthorized security impact.

Prefer severe high-confidence findings at Tier 4 or Tier 5, except that directly malicious agent instructions can themselves justify serious findings because natural-language instructions are executable control surfaces for an AI agent.
