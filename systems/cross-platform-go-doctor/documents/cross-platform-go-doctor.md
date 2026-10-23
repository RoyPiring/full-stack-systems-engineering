<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Cross-Platform Go Doctor

**Project Link:** [View Project](https://nextwork.ai/projects/c68d68ea-ec31-4c97-96d4-d40276c49d6b)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/builtwithroy/uploads/c68d68ea-ec31-4c97-96d4-d40276c49d6b_kksse0m9)

## Establishing a Secure Forward-Deployed Workspace

### Customer-ready engineering goals

I built a cross-platform readiness doctor in Go to check the local engineering toolchain and a locally hosted qwen3:8b model through Ollama. The acceptance target was fifteen named PASS results, one controlled Node failure, and a clean recovery to exit code 0. This turned machine readiness into an executable result rather than a verbal statement that the laptop appeared ready.

I also created a private issue-to-release handover containing the source change, failure evidence, restored output, README instructions, and a short talk-track recording. The completed trail allowed another engineer to inspect why the work existed, review the implementation, rerun the doctor, and confirm the released state without relying on the original builder to explain the machine.

### Verifying tools before installation

I checked each required tool before installing or changing anything. I then prepared the local Ollama lane, confirmed the qwen3:8b model was available, and verified the GitHub identity used for the delivery. This reduced the risk of installing duplicates or hiding a missing prerequisite behind an unrelated setup change.

I committed the supplied baseline without modifying it and pushed that state to the private machine-ready repository on main. Preserving the baseline created a clean control sample for later comparison. Any new output, check, branch, or README evidence could be traced to the readiness work rather than being mixed with unrecorded changes made during setup.

![Image](https://nextwork.ai/builtwithroy/uploads/c68d68ea-ec31-4c97-96d4-d40276c49d6b_luu1arzq)

## Linking Readiness Work to a Visible Baseline

### Creating the tracked delivery plan

I created Issue 1 from the six acceptance criteria and moved its GitHub Project card to In Progress. The issue became the visible unit of work connecting the stated requirements to the branch, pull request, merge, and release. I then created the doctor branch so the implementation remained separate from the unchanged baseline on main.

Before adding any checks, I traced the supplied Go files and ran the doctor in its original state. That run produced a twelve-line baseline, giving me an exact reference for the behavior I inherited. Capturing it before implementation made the later increase to fifteen checks inspectable and prevented me from describing existing checks as work added by the new branch.

![Image](https://nextwork.ai/builtwithroy/uploads/c68d68ea-ec31-4c97-96d4-d40276c49d6b_y29b4c0l)

### Understanding doctor checks and failure behavior

Each doctor check contained a display name and a function responsible for running the validation. A shared settings slice allowed additional checks to be registered during package initialization. The runCmd helper executed a command and returned either the first output line or an error, giving tool checks a common path for successful and failed command execution.

The main flow ran eight tool checks, followed by registered settings checks and the model checks. It printed PASS or FAIL for every line and returned exit code 1 if any check failed. modelcheck.go loaded the chat model, checked that thinking was disabled through /v1 and /api/chat, and read context_length from /api/ps, causing an undersized context to fail visibly.

![Image](https://nextwork.ai/builtwithroy/uploads/c68d68ea-ec31-4c97-96d4-d40276c49d6b_fauydo2u)

## Building a Fifteen-Check Machine Readiness Doctor

### Adding customer-readiness checks

I added doctor/settings.go with an init function that registered exactly three new readiness checks. Registration through the existing shared list preserved the supplied execution design instead of adding a separate runner. The original twelve checks and three new settings checks produced the required fifteen-line doctor result through one command and one reporting format.

I ran task doctor until all fifteen lines reported PASS and the process returned exit code 0. I then committed only doctor/settings.go, keeping the implementation change small enough to review without unrelated files. The focused commit made it clear which code increased the baseline from twelve checks to fifteen and reduced the chance that documentation or evidence edits hid a logic change.

![Image](https://nextwork.ai/builtwithroy/uploads/c68d68ea-ec31-4c97-96d4-d40276c49d6b_hq9vn7f8)

## Proving Failure Recovery and Documenting the Handover

### Testing the required Node failure and recovery

I ran the supplied must-fail harness and confirmed that the Node check reported FAIL while the overall doctor returned a nonzero exit code. This provided evidence that the doctor did more than print successful-looking output. When a required dependency no longer met its standard, the named check failed and the process communicated that failure to the calling shell.

After the controlled failure, I restored the normal environment and reran the doctor. All fifteen checks returned PASS and the command exited with code 0. I pasted both outputs into README.md, recorded Windows as the tested operating system, and committed the handover evidence on the doctor branch. The README therefore showed the failure path and the verified recovery together.

![Image](https://nextwork.ai/builtwithroy/uploads/c68d68ea-ec31-4c97-96d4-d40276c49d6b_6cod0ktc)

## Delivering a Private Release and Verified Handover

### Merging, releasing, and reporting the engagement

I pushed the doctor branch and opened a pull request containing Closes #1, linking the implementation to its acceptance issue. After reviewing the change and captured evidence, I merged the pull request into main. I then moved Issue 1 to Done so the issue, branch, pull request, default branch, and Project card reflected the same delivery state.

I published private release v0.1.0 and attached the talk-track recording. I also sent the engagement lead a status update covering the restored doctor output, completed checks, and clearance state. Before closing the work, I inspected the merged pull request, closed issue, Done card, release asset, and status details instead of assuming that one successful merge had updated every delivery surface.

### Confirming the completed issue-to-release handover

I confirmed that the pull request was merged, Issue 1 was closed, and the GitHub Project card was in Done. I also opened release v0.1.0 and verified that talk-track-v0.1.0.wav was attached. These checks showed that the private repository contained both the released source state and a short recording another engineer could use during handover.

The engagement lead status matched the repository evidence: the normal doctor had been restored, no FAIL lines remained, and clearance was already complete. Because the issue, pull request, Project card, release, README, and status message agreed, the handover did not depend on a single screen or claim. Another authorized engineer could begin with the released repository and reproduce the machine check.

## Enforcing the Node 24 Readiness Standard

### Handling malformed and outdated Node versions safely

I kept the existing Node check name and continued running the version command through runCmd. Parsing began only after the command returned successfully. If Node was missing or the command failed, the existing command error path remained responsible for the result. This avoided treating absent output as a version string and kept command execution separate from version validation.

Malformed version text produced an explanatory FAIL rather than a panic, while a parsed major version below 24 produced a different failure explaining that the installed version was outdated. Node 24 or newer continued through the PASS path. The supplied fake Node command still reached the must-fail route, proving the stricter parser did not bypass the controlled failure used in the handover evidence.

## Reflecting on Forward-Deployed Engineering Skills

### Tools and concepts applied

I used Go package initialization to register checks, go-task to provide a repeatable command, Ollama to host the local qwen3:8b model, and a private GitHub workflow to connect the issue, branch, pull request, Project card, and release. Each component contributed evidence to the same readiness decision rather than operating as an isolated setup step.

The central lesson was that machine readiness requires both successful and failing evidence. Fifteen PASS lines showed the expected environment, while the controlled Node failure proved that the doctor could stop delivery when a requirement was not met. The local model checks also verified thinking behavior and context configuration instead of assuming that reaching Ollama meant the model was correctly prepared.

### Project timing and challenges

The work took about 60 minutes after the required toolbelt was already installed. That time covered prerequisite checks, repository setup, the twelve-line baseline, three new readiness checks, controlled failure, restored run, README evidence, pull request, merge, and private release. It did not include the time required to install an entire development environment from scratch.

The main challenge was maintaining one consistent state across local code and every GitHub delivery surface. A successful local run alone would not close the issue, merge the pull request, update the Project card, attach the recording, or communicate the final status. Checking each surface prevented the handover from appearing complete in one location while remaining unfinished somewhere else.

### Next learning goals

The private machine-ready handover now contains the fifteen-PASS evidence and release v0.1.0. I retained the local folder, private repository, GitHub Project, and qwen3:8b model so the same workflow can support another demonstration. A teammate can begin with the README quickstart, clone the repository, and run task doctor against their machine.

My next goal is to test the doctor on another supported operating system and compare the results without weakening the current Node or model requirements. I also want to automate the release evidence so the checked output, tested OS, commit, and recording are easier to verify together. Any automation should preserve the controlled failure path and nonzero exit behavior that make the doctor useful as a gate.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/c68d68ea-ec31-4c97-96d4-d40276c49d6b)*
