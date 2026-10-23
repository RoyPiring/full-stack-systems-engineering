<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Your First Typed Next.js App

**Project Link:** [View Project](https://nextwork.ai/projects/572079bc-3939-4b0a-92c8-f33daf8a7dc0)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/builtwithroy/uploads/572079bc-3939-4b0a-92c8-f33daf8a7dc0_7czkem7o)

## Defining a Contract-First Full-Stack Release

### Project goals and release evidence

I shipped a private Next.js 16 screen that communicated with a supplied Go service through a typed, server-only boundary. The OpenAPI contract landed on main before the screen branch existed, so the request and response shapes were reviewed before implementation began. This gave the UI a fixed agreement to consume rather than allowing it to define the service boundary while being built.

The completed evidence included a private GitHub issue, contract pull request, screen pull request, review responses, merged commits, and release v0.1.0. I also attached a two-minute recording that explained the request path, typed boundary, failure tests, and known limit. Together, those artifacts created an interview-ready trail from contract design through reviewed release.

## Preparing a Reproducible Next.js Workspace

### Setting up the project foundation

I prepared the fixed first-screen scaffold and verified the required tools before beginning contract work. I installed the pinned application and testing libraries, added Chromium for Playwright, and initialized the shadcn button component. Keeping these dependencies fixed reduced variation between the local build, automated tests, and another engineer’s clean setup.

I opened the completed workspace tree in Cursor and confirmed that Grok was available before drafting or implementing the contract. This created one prepared environment for design, coding, testing, and review. The application work did not begin until the scaffold, package dependencies, browser test runtime, component setup, documentation folders, and development tools were all present.

![Image](https://nextwork.ai/builtwithroy/uploads/572079bc-3939-4b0a-92c8-f33daf8a7dc0_kn1u78z6)

### Verifying the toolchain and scaffold

The environment checks reported Node v24.21.0 and Go go1.27.1. Git and the GitHub CLI were already authenticated with access to the private work. create-next-app@16.4.2 completed successfully and printed Initialized a git repository, establishing the application and local history before the feature branches were created.

The pinned library installation, Playwright Chromium installation, shadcn initialization, and button addition also completed. The docs folder contained six design files and the README skeleton, while Grok appeared in the available chat-model selector. These checks confirmed that the application, documentation structure, test browser, component library, source control, and coding assistant were ready before the contract trail began.

## Establishing the Reviewed API Contract

### Designing the service boundary

I created the six design documents and opened a private review space in GitHub. Two issues represented the contract and screen work, and both were added to a private GitHub Project. The first branch contained the OpenAPI agreement and supporting documentation without any screen implementation, keeping the service boundary review separate from UI behavior.

I opened and merged the contract pull request before creating the screen branch. That order provided visible proof that the implementation started from a reviewed contract already present on main. The resulting history showed when the request and response shapes became authoritative and prevented the screen changes from obscuring whether the agreement existed before the feature code.

![Image](https://nextwork.ai/builtwithroy/uploads/572079bc-3939-4b0a-92c8-f33daf8a7dc0_mdcjvcmw)

![Image](https://nextwork.ai/builtwithroy/uploads/572079bc-3939-4b0a-92c8-f33daf8a7dc0_yc10jy0b)

### Why the contract came before implementation

Merging the contract first fixed the OpenAPI field names and allowed openapi-typescript to generate the initial TypeScript definitions from a reviewed source. The screen branch could then consume those generated types instead of inventing request and response objects inside the form. A later contract change would become a visible schema update capable of breaking incompatible TypeScript code.

Separating the pull requests also simplified review. The first discussion focused on the service agreement, while the second focused on how the screen used that agreement. If both had arrived together, reviewers would have needed to evaluate API semantics, generated types, UI behavior, server boundaries, validation, and styling in one change, making the source of disagreements harder to isolate.

## Building a Typed Screen Behind a Server Boundary

### Implementing the typed Next.js screen

I created the screen branch from the commit containing the merged contract. I then used five focused commits to separate the typed API client, unchanged Grok draft, page structure, Route Handler written by me, and reviewed form. This history preserved authorship and intent without combining generated, agent-produced, and manually reviewed work into one indistinguishable commit.

The commit sequence became part of the delivery evidence. During an interview or code review, I could show what the agent proposed, what I implemented, and what changed after review. Keeping each responsibility separate also made regressions easier to trace because the request client, page, server route, and client form did not enter history as one oversized implementation change.

![Image](https://nextwork.ai/builtwithroy/uploads/572079bc-3939-4b0a-92c8-f33daf8a7dc0_9xbejezd)

### Keeping the Go service address server-only

The browser form posted only to /api/echo on the Next.js application. The Route Handler received the request, checked the message field, and forwarded the call through the server-only API client. This placed the Next.js server between the browser and Go service instead of allowing the Client Component to call the Go endpoint directly.

The API client read SERVICE_URL on the server, so 127.0.0.1:8080 did not need to appear in browser code or a public environment variable. The server-only import added a build-time safeguard against accidental use in a Client Component. The browser knew the application route, while the Next.js server owned the private location of the upstream service.

## Proving the Contract and Security Boundary

### Testing green paths and deliberate failures

I validated the expected path with TypeScript checking, Vitest, Playwright, and a production build. I then exercised the live screen while the Go service was available and again while it was unavailable. This checked both the successful response path and the user-visible behavior when the local upstream service could not answer.

After the green checks passed, I introduced two deliberate failures. One changed the response field and tested whether contract drift reached the TypeScript compiler. The other imported the server-only API client into the Client Component and tested whether the production build blocked that boundary violation. I restored both files and reran the checks until the release state returned to green.

### What the red cases proved

Renaming the expected response field to text caused TypeScript error TS2339 in echo-form.tsx. The failure showed that the form’s reply shape came from the generated contract types rather than a separately written interface. A contract field change therefore broke incompatible UI code during type checking, before the application could make a browser request using the wrong property.

Importing the API client into the Client Component caused the production build to stop because that module was marked server-only. This demonstrated that the service address and upstream client could not silently enter the client module graph. After reversing both deliberate edits, type checking and the production build passed again, proving that the recorded release was restored rather than left in a known failing state.

## Reviewing and Releasing Production Evidence

### Turning implementation into credible delivery evidence

I opened the screen pull request and connected it to Issue 2 with Closes #2. Each review finding received a written decision and, where required, a follow-up commit. This preserved the difference between blindly accepting feedback and evaluating whether a suggestion matched the documented contract, service ownership, and stated limits.

After resolving the review, I merged the pull request and published private release v0.1.0. I attached the two-minute talk track so another reviewer could hear the contract-first sequence, request path, security boundary, red-case evidence, and known runtime limit. The release closed the trail from reviewed API agreement to reviewed and recorded screen delivery.

![Image](https://nextwork.ai/builtwithroy/uploads/572079bc-3939-4b0a-92c8-f33daf8a7dc0_7l9jodwc)

### Resolving review findings

I rejected the Echo cast finding because docs/limits.md already described that hop as asserted rather than runtime checked. Accepting the suggestion without adding real runtime validation would have changed the wording without changing the behavior. I kept the limitation explicit so the release did not claim that TypeScript had validated values received over the network.

I also rejected tighter empty-message validation because the Go service already owned that rule and returned the appropriate problem detail. Duplicating it in the Route Handler would have created two validation authorities. I accepted the document-title finding and pushed Fix review finding 1, updating the browser tab to First screen because that change corrected a clear presentation defect without altering the service contract.

![Image](https://nextwork.ai/builtwithroy/uploads/572079bc-3939-4b0a-92c8-f33daf8a7dc0_idpciwpz)

## Defending a Runtime Design Limit

### Interview-ready engineering judgment

I documented that TypeScript checks field names and declared shapes during development, but it does not prove that every value received at runtime matches the contract. This boundary allowed a renamed response field to fail at compile time without claiming that the application had validated every returned message value after crossing the network.

That limitation was an intentional part of v0.1.0, not an omitted explanation. A later feature could add runtime schema validation if the risk justified the additional dependency and processing. For this release, I chose to state the asserted hop honestly and rely on the Go service for its owned validation. That tradeoff is clearer in an interview than presenting static types as runtime proof.

### Evidence from the request path

The strongest evidence was the deliberate TS2339 failure in src/components/echo-form.tsx after the response property was renamed. The error occurred where the form tried to access a field that no longer existed in the generated Echo type. This connected the visible failure to the exact component consuming the service response.

The type path began with openapi.yaml, continued through the generated schema.d.ts, and ended in the form’s typed reply. Because the field name was not manually duplicated in a separate local interface, changing the contract surfaced the mismatch in the consuming code. The restored typecheck then proved that the release returned to the agreed field name before merge.

### Connecting the limit to the runtime boundary

EchoForm sent the user’s input only to /api/echo. The Route Handler received that request and forwarded the message through the server-only API client, which used the generated Echo type for the response. This created a typed path across the Next.js code while keeping the Go service address outside the browser-facing component.

When the response field changed, the compiler failure appeared in the form before any browser test could claim the returned value was correct. That proves static alignment between the contract and TypeScript consumer. It does not prove that the Go service will always return valid runtime content, which is why the limit belongs at the typed network hop rather than being presented as validation of the Go service itself.

## Reflecting on the Full-Stack Delivery Journey

### Tools and concepts learned

I used OpenAPI as the reviewed contract, openapi-typescript to generate TypeScript definitions, and openapi-fetch for the typed server client. Next.js 16 provided the Route Handler and server-client module boundary, while Cache Components defined the application’s current rendering and caching model. The local Go service remained behind the Next.js server.

Vitest covered focused application behavior, Playwright exercised the browser path, and the production build checked whether the release could compile under deployment conditions. The two deliberate red cases proved that contract drift and server-only leakage failed visibly. GitHub issues, pull requests, review comments, commits, and the private release turned those technical checks into a traceable delivery record.

### Timebox and key challenge

The work took about one hour across setup, implementation, validation, review, and release. This aligned with the stated 59-minute page estimate after including reading, typing, package installation, browser installation, command execution, GitHub updates, and recording time. No multi-day observation period was required because the delivery fit within one focused session.

The engineering concepts were clear, but several page steps lacked complete command blocks. That created avoidable pauses because I had to reconstruct the intended commands from surrounding instructions. The main lesson was not a change to the architecture. It was that a technically sound guide still needs complete, copyable command sequences so the learner can move through setup, red cases, recovery, and release without guessing.

### Next learning goal

Shipping a private screen with a reviewed contract trail gave me evidence I could defend in an interview. I would preserve the same typed, server-only boundary for the next feature because it kept the upstream service location outside the browser and made a contract field change fail before release. The commit and pull-request history also made the design sequence visible.

My next goal is to add another feature through the same contract-first path while strengthening the guide’s command coverage. I would keep the static type boundary honest, decide explicitly whether the new response requires runtime validation, and include every command needed to create, test, deliberately break, restore, review, and release the feature without filling procedural gaps from memory.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/572079bc-3939-4b0a-92c8-f33daf8a7dc0)*
