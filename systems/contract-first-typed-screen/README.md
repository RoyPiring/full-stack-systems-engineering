# Your First Typed Next.js App

> Inside the [Full-Stack Systems Engineering](../../README.md) portfolio · *Application services engineered from contract to running code.*

## Overview

I shipped a private Next.js 16 screen that communicated with a supplied Go service through a typed, server-only boundary. The OpenAPI contract landed on main before the screen branch existed, so the request and response shapes were reviewed before implementation began. This gave the UI a fixed agreement to consume rather than allowing it to define the service boundary while being built.

The completed evidence included a private GitHub issue, contract pull request, screen pull request, review responses, merged commits, and release v0.1.0. I also attached a two-minute recording that explained the request path, typed boundary, failure tests, and known limit. Together, those artifacts created an interview-ready trail from contract design through reviewed release.

The architecture is built across **7 phases**, anchored by **Defining a Contract-First Full-Stack Release** on the input side and **Defending a Runtime Design Limit** at the end. Each phase is listed in the Implementation section below.

## Architecture

```mermaid
---
title: Your First Typed Next.js App
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    Reviewer[/A reviewer who has to see from the history when the agreement became authoritative, not only from the attached walkthrough/]
    Owed[/The runtime schema validation that was deliberately not built, and would be needed before the typed hop could be called checked/]

    subgraph Toolchain["A workspace pinned before any contract work"]
        Versions[("Node v24.21.0 and Go go1.27.1, with git and the GitHub CLI already authenticated against the private work")]
        Scaffold(create-next-app@16.4.2 run, reporting that it initialized a git repository)
        Pinned[("Pinned application and testing libraries, Playwright Chromium, shadcn initialized with its button component")]
        Docs[("Six design documents and a README skeleton present before the contract trail opened")]
        Reproducible{{Fixing the versions first reduces the variation between the local build, the automated tests and another engineer's clean setup}}
    end

    subgraph Order["The sequence that is itself the evidence"]
        TwoIssues[("Two issues on a private GitHub Project, one for the contract and one for the screen")]
        ContractBranch(A first branch carrying the OpenAPI agreement and its documentation, with no screen code in it)
        MergedFirst[("The contract pull request merged before the screen branch was created")]
        Visible{{Merging first puts the reviewed agreement on main, so history shows the implementation started from it rather than defining it while being written}}
        Reviewable{{Two pull requests keep the service agreement and the screen's use of it in separate discussions, so a disagreement has one subject}}
    end

    subgraph Types["Types generated from the reviewed source, not written twice"]
        Spec[("openapi.yaml, with its field names fixed by the merged review")]
        Generated(openapi-typescript generating schema.d.ts from the reviewed contract)
        Consumed[("The screen consuming the generated types instead of inventing request and response objects inside the form")]
        NoDuplicate{{Because the reply shape is never restated in a local interface, a contract change has nowhere to hide and shows up in the consuming code}}
    end

    subgraph Authorship["Five commits that keep authorship legible"]
        Branch(The screen branch cut from the commit holding the merged contract)
        Five[("Separate commits for the typed API client, the unchanged assistant draft, the page structure, the hand-written Route Handler and the reviewed form")]
        Attribution{{Generated, assistant-produced and hand-reviewed work stay distinguishable, so what was proposed and what was decided can both be shown}}
        Traceable{{A regression traces to one responsibility rather than to a single oversized implementation commit}}
    end

    subgraph Boundary["The server kept between the browser and the service"]
        Form(EchoForm in the browser posting only to the application's own /api/echo route)
        Handler(A Route Handler receiving the request, checking the message field and forwarding it onward)
        Client[("A server-only API client reading SERVICE_URL on the server")]
        Upstream[("The supplied Go service, reached by the Next.js server and never by the browser")]
        Loopback[("The loopback service address, which never needs to appear in browser code or in a public environment variable")]
        BuildTime{{The server-only import is a build-time safeguard, so the upstream client cannot drift into the client module graph by accident}}
        Owns{{The browser knows the application route and the Next.js server owns where the upstream service actually lives}}
    end

    subgraph Green["The expected path, proven before anything was broken"]
        Checks(TypeScript checking, Vitest, Playwright and a production build)
        Both[("The live screen exercised with the Go service available and again with it unavailable")]
        UserVisible{{Running the unavailable case covers what the person using the screen sees, not only the successful response}}
    end

    subgraph Red["Two failures introduced on purpose"]
        Rename(The expected response field renamed to text)
        TS[("TypeScript error TS2339 raised in src/components/echo-form.tsx, where the form reads a field that no longer exists")]
        Leak(The server-only API client imported into the Client Component)
        Stopped[("The production build refusing to complete, because that module is marked server-only")]
        Restored[("Both edits reversed, with type checking and the production build green again")]
        Compiler{{Contract drift reaches the compiler before the application can request anything using the wrong property}}
        NotLeftRed{{Restoring and rechecking is what makes the recorded release the green state rather than a known failing one}}
    end

    subgraph Review["Findings answered rather than absorbed"]
        Closes(The screen pull request opened and tied to its issue with Closes #2)
        RejectCast[("The Echo cast finding rejected, because docs/limits.md already calls that hop asserted rather than runtime checked")]
        RejectValidation[("Tighter empty-message validation rejected, because the Go service owns that rule and returns the problem detail")]
        Accepted[("The document-title finding accepted and pushed as Fix review finding 1, setting the browser tab to First screen")]
        TwoAuthorities{{Duplicating the empty-message rule in the Route Handler would create two validation authorities for one condition}}
        Decided{{Each finding carries a written decision, which keeps accepting feedback distinguishable from evaluating it against the documented contract}}
    end

    subgraph Release["What the release actually claims"]
        Published[("Private release v0.1.0 with a two-minute recording covering the request path, the boundary, the red cases and the known limit")]
        Limit[("The stated limit: static types check field names and declared shapes, and do not prove that every value arriving over the network matches the contract")]
        Honest{{Naming the asserted hop is a defensible position, where presenting static types as runtime proof would not be}}
        Clock[("About one hour against a 59-minute estimate, with the guide's incomplete command blocks named as the real friction")]
    end

    Reviewer -- "needs the sequence, which begins with" --> Versions
    Versions -- "then" --> Scaffold
    Scaffold -- "completed alongside" --> Pinned
    Scaffold -- "with" --> Docs
    Pinned -- "gives" --> Reproducible
    Reproducible -- "only then opening" --> TwoIssues
    TwoIssues -- "first of them becoming" --> ContractBranch
    ContractBranch -- "merged as" --> MergedFirst
    MergedFirst -- "establishes" --> Visible
    TwoIssues -- "kept apart for" --> Reviewable
    MergedFirst -- "fixes the field names in" --> Spec
    Spec -- "read by" --> Generated
    Generated -- "produces the types" --> Consumed
    Consumed -- "is why" --> NoDuplicate
    Visible -- "is what the branch is cut from, through" --> Branch
    Branch -- "built in" --> Five
    Five -- "preserves" --> Attribution
    Five -- "delivers" --> Traceable
    Consumed -- "wired into" --> Form
    Form -- "posts to" --> Handler
    Handler -- "forwards through" --> Client
    Client -- "reads, and never publishes," --> Loopback
    Client -- "calls" --> Upstream
    Upstream -- "answers with the shape declared in" --> Spec
    Upstream -- "owns the empty-message rule, which is why" --> RejectValidation
    Client -- "guarded by" --> BuildTime
    Loopback -- "gives" --> Owns
    Handler -- "exercised by" --> Checks
    Checks -- "run against" --> Both
    Both -- "covers" --> UserVisible
    Checks -- "green before introducing" --> Rename
    Rename -- "raised" --> TS
    TS -- "proves" --> Compiler
    NoDuplicate -- "is what makes TS fire in the form" --> TS
    Checks -- "green before introducing" --> Leak
    Leak -- "blocked, giving" --> Stopped
    BuildTime -- "is the mechanism Stopped demonstrates" --> Stopped
    Rename -- "then undone in" --> Restored
    Leak -- "then undone in" --> Restored
    Restored -- "held under" --> NotLeftRed
    Restored -- "only then opening" --> Closes
    Closes -- "answered with" --> RejectCast
    Closes -- "answered with" --> RejectValidation
    Closes -- "answered with" --> Accepted
    RejectValidation -- "avoids" --> TwoAuthorities
    RejectCast -- "together with Accepted gives" --> Decided
    Decided -- "merged into" --> Published
    Published -- "states" --> Limit
    RejectCast -- "is consistent only because of" --> Limit
    Limit -- "makes the claim" --> Honest
    Published -- "cost" --> Clock
    Limit -- "leaves" --> Owed
    Compiler -- "does not reach what Owed would cover" --> Owed
    Attribution -- "read by" --> Reviewer
    Honest -- "read by" --> Reviewer
    Owns -- "read by" --> Reviewer

    class Versions,Pinned,Docs,TwoIssues,MergedFirst,Spec,Consumed,Five,Client,Upstream,Loopback,TS,Stopped,Restored,RejectCast,RejectValidation,Accepted,Published,Limit,Clock,Both datastore
    class Scaffold,ContractBranch,Generated,Branch,Form,Handler,Checks,Rename,Leak,Closes service
    class Reproducible,Visible,Reviewable,NoDuplicate,Attribution,Traceable,BuildTime,Owns,UserVisible,Compiler,NotLeftRed,TwoAuthorities,Decided,Honest event
    class Reviewer,Owed io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/contract-first-typed-screen.md`](./documents/contract-first-typed-screen.md).

## Implementation

This system is built across **7 phases**:

1. **Defining a Contract-First Full-Stack Release**
2. **Preparing a Reproducible Next.js Workspace**
3. **Establishing the Reviewed API Contract**
4. **Building a Typed Screen Behind a Server Boundary**
5. **Proving the Contract and Security Boundary**
6. **Reviewing and Releasing Production Evidence**
7. **Defending a Runtime Design Limit**

For the full walkthrough with screenshots and step-by-step content, see [`documents/contract-first-typed-screen.md`](./documents/contract-first-typed-screen.md).

## Validation

Each build phase below is documented in [`documents/contract-first-typed-screen.md`](./documents/contract-first-typed-screen.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Defining a Contract-First Full-Stack Release
- ✅ Preparing a Reproducible Next.js Workspace
- ✅ Establishing the Reviewed API Contract
- ✅ Building a Typed Screen Behind a Server Boundary
- ✅ Proving the Contract and Security Boundary
- ✅ Reviewing and Releasing Production Evidence
- ✅ Defending a Runtime Design Limit
