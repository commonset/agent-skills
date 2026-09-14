# Security Review Framework

Use this reference to keep Agent Skill reviews precise, evidence-based, and consistent. It supplements `SKILL.md`; it does not replace judgment about the actual package and execution environment.

## Severity guidance

Severity should describe plausible impact from the reviewed behavior, not how suspicious the text looks.

### Critical

Use sparingly for confirmed behavior that can plausibly cause catastrophic impact with little additional user action, such as deliberate credential exfiltration with a usable destination, destructive actions against production or broad data, or a clear path to major compromise.

### High

Use for confirmed behavior with substantial confidentiality, integrity, availability, or authorization impact, such as executing remote unverified code, transmitting sensitive repository or user data to an external service without a justified boundary, or modifying security controls to expand authority.

### Medium

Use for meaningful risk that is narrower, conditional, or more recoverable, such as broad filesystem access beyond the task, installing mutable dependencies without verification, or making consequential external changes without an adequate confirmation boundary.

### Low

Use for limited-impact behavior that deserves hardening or clearer disclosure but is unlikely to create significant harm by itself.

### Informational

Use for facts that help a reviewer understand the capability footprint or trust boundary but are not vulnerabilities by themselves.

Do not turn every capability into a finding. A code-review skill that reads repository files is expected to access files; the review question is whether the access is necessary, bounded, and accurately represented.

## Evidence model

Prefer evidence in this order:

1. executable code or configuration that directly performs the behavior
2. an explicit operational instruction in `SKILL.md`
3. an instruction in a referenced file that the skill tells the agent to follow
4. metadata that changes execution or tool permissions
5. comments, examples, or descriptive text

Lower-ranked evidence can still matter, but do not classify it as stronger behavior than it supports.

For each finding, distinguish:

- **Observed behavior**: present in the package
- **Required precondition**: credentials, tool access, network access, user confirmation, specific runtime, or another dependency
- **Potential impact**: what could happen if the behavior runs under those conditions

## Review dimensions

### Command and code execution

Look for instructions or code that execute shell commands, interpreters, build tools, package managers, downloaded artifacts, or dynamically generated code.

Stronger evidence:

- explicit instruction to run a command or script
- subprocess or shell execution in a bundled script
- downloaded content passed to an interpreter or shell

Near misses that should not be overstated:

- a command shown only as documentation
- a script path mentioned but never invoked
- comments describing an operation

### Network access

Look for outbound requests, uploads, webhooks, remote APIs, package downloads, remote instruction loading, and untrusted redirect or URL handling.

Ask:

- Is the destination fixed, user-controlled, or discovered dynamically?
- What data leaves the local trust boundary?
- Is remote content treated as data or as instructions/code?
- Is authentication required, and how are credentials handled?

Network access alone is not exfiltration.

### Filesystem behavior

Look for reading, writing, deleting, copying, or traversing files outside the task's expected scope.

Distinguish:

- repository-local reads needed for the task
- writes to generated/output files
- access to home directories, SSH configuration, cloud credentials, shell profiles, or unrelated workspaces
- recursive or destructive operations

### Credentials and secrets

Separate four different behaviors:

1. **Reference**: documentation mentions a credential or environment variable.
2. **Access**: the skill reads a credential, token, secret store, or environment variable.
3. **Use**: the skill supplies the credential to an intended authenticated service.
4. **Disclosure**: the skill transmits or exposes the credential somewhere outside the intended trust boundary.

Do not collapse these into a single "secret access" finding. Report the behavior actually supported by the evidence.

Never reproduce full secret values in review output.

### Data disclosure

Trace data from source to destination. A strong disclosure finding should identify both:

- the sensitive or proprietary data being collected
- the external destination or output that crosses the intended boundary

If one side of the flow is missing, report uncertainty rather than asserting exfiltration.

### Persistence and environment modification

Look for changes to:

- shell startup files
- editor or agent configuration
- Git hooks
- scheduled tasks
- system services
- global package or tool configuration
- PATH or executable search order

Persistence is more material when it survives the task and changes future behavior without clear user intent.

### Dependencies and supply chain

Look for package installation, mutable tags, unpinned remote scripts, binary downloads, plugins, or dependencies retrieved at runtime.

Consider:

- whether the dependency is necessary
- whether its source and version are constrained
- whether integrity is verified
- whether retrieved content is executed

Do not call ordinary use of a declared dependency malicious. Describe the trust boundary and the consequence of compromise.

### Authorization and security controls

Look for instructions to:

- grant broader permissions
- disable verification or security checks
- bypass policy or review gates
- modify ACLs or organization settings
- create long-lived credentials

Distinguish a documented administrative setup step from an attempt to evade controls. Context and user authorization matter.

### Hidden, encoded, or indirect instructions

Inspect encoded or generated content when it materially influences agent behavior. Look for instructions loaded from remote pages, generated files, images, archives, or other sources that the skill tells the agent to trust automatically.

Encoding alone is not malicious. The finding should be about the behavior concealed or delegated by the encoding, not the existence of Base64, compression, or another representation.

## Cross-file flow examples

### Retrieval without disclosure

`SKILL.md` instructs the agent to read `.env` so a local test command can authenticate to a development service. That is credential access. It is not credential disclosure unless another step sends the value outside the intended service boundary.

### Indirect remote execution

`SKILL.md` instructs the agent to run `scripts/setup.sh`; that script downloads a URL and pipes the response into a shell. The material behavior is remote code execution even though the network request and shell execution live in a supporting file.

### Safe negative

A reference file says, "Never upload repository source or secrets to external services." This is a prohibition and should not trigger a disclosure finding.

### Example versus action

Documentation contains `rm -rf build/` as an example cleanup command, but no instruction tells the agent to execute it. Treat the example as context unless another operational step activates it.

## Review conclusion

Use semantic decisions rather than numeric risk scores:

- **No material findings**: reviewed behavior matches the stated purpose and no material security issue is supported by the available evidence.
- **Review required**: one or more behaviors need an explicit trust, access, or operational decision before broader use.
- **Block pending remediation**: a supported material finding should be corrected or constrained before use.

Always include residual uncertainty when the review did not cover every referenced file, remote dependency, executable, provider permission, or runtime condition.
