# Your First Go Service, Contract First

> Inside the [Full-Stack Systems Engineering](../../README.md) portfolio · *Application services engineered from contract to running code.*

## Overview

I built a contract-first Go service to practice defining an API before implementing its handlers. The service included health and echo endpoints, strict request validation, predictable error responses, and tests that compared the running application with its published specification. I used OpenAPI 3.1.2 to describe routes, request bodies, response codes, and schemas before adding service code. This order gave reviewers a stable promise against which they could judge the implementation. I also maintained the design records, tests, measured responses, and review decisions in Git. Documentation was therefore part of the acceptance boundary rather than a summary written after development. The completed build demonstrated how even a small API could have explicit behavior, controlled failure paths, traceable engineering decisions, and evidence capable of revealing drift between the documented contract and the service.

The architecture is built across **6 phases**, anchored by **Establishing a Contract-First Engineering Foundation** on the input side and **Completing Review and Releasing the Service** at the end. Each phase is listed in the Implementation section below.

## Architecture

```mermaid
---
title: Your First Go Service, Contract First
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    Reviewer[/Reviewer who judges the interface from the specification, without reading Go/]
    Client[/Client sending well-formed and malformed requests alike/]

    subgraph Workspace["Repository controls, set before any code"]
        Repo(first-service as a sibling of Project 1, its own .git and local main)
        Isolation{{A separate history, so a stray edit or release cannot reach the neighbouring project}}
        Attrs[(.gitattributes and .editorconfig require UTF-8 text and LF endings)]
        Ignore[(.gitignore excludes big.json, needed for request-limit testing, not for permanent history)]
        Noise{{Text controls stop Windows CRLF churn from hiding a meaningful difference}}
        Toolchain[(Inherited Windows toolchain checked first: Go, Git, repository settings, editor behavior)]
        Separable{{Environment recorded before contract design, so a later failure is not confused with a workstation problem}}
    end

    subgraph Promise["The API promise, committed first"]
        Spec[(OpenAPI 3.1.2: health and echo routes, request bodies, media types, status codes, response schemas)]
        Records[(Design records: bounded bodies, strict decoding, stable error shapes, contract-to-router comparison)]
        First{{The repository's first behavioral commitment, made while the service still had no handlers}}
        NoRewrite{{The specification cannot be rewritten afterward to make existing code look compliant; an intentional interface change needs its own revision}}
        Challenge{{Reviewers can challenge the API design independently of how it was built}}
    end

    subgraph Skeleton["A small service, kept inspectable"]
        Module(Go module, the direct YAML dependency for reading the specification, and the executable entry point)
        Router(Router registering only the documented health and echo endpoints)
        Helpers[(Shared helpers producing consistent JSON and problem responses)]
        Modest{{Deliberately small, so boundary behavior is exposed rather than hidden behind a framework}}
        Health[(GET healthz returns status ok, a liveness check usable before and after each error case)]
    end

    subgraph Boundary["handleEcho as a five-step refusal boundary"]
        Limit(1. Byte limit on the body, so an oversized request cannot consume unbounded memory)
        ReadFail(2. Body-read failure rejected)
        Malformed(3. JSON decoded, malformed content rejected)
        Strict(4. Unknown fields and an empty message rejected, so accepted input matches the schema)
        Success(5. Only then: application/json, 200 OK, the supplied message echoed back)
        Terminal{{Every refusal returns before the success writer, so 200 OK is earned rather than the default branch}}
        Problems[(Fixed RFC 9457 problem details: 413 oversized, 400 unknown field, 400 empty message)]
        NoLeak{{No parser text, stack information, or fragments of rejected input in any response}}
    end

    subgraph Parity["Route parity, checked both ways"]
        BothWays(Test compares documented paths against registered routes in both directions)
        BreakA[(A documented route with no implementation fails the task and names the missing side)]
        BreakB[(An implemented route with no documentation fails the same way)]
        Restored[(Parity restored returns the suite to passing)]
        OneWay{{A one-way check creates false confidence: contract-only misses undocumented routes, code-only misses unhandled promises}}
    end

    subgraph Runtime["Runtime evidence, beyond compilation"]
        Cases(Oversized body, undocumented field, and empty message sent to the running service)
        Codes[(413, 400 and 400 recorded with their problem bodies)]
        Recheck[(healthz returns status ok after every refusal)]
        Survived{{An error response alone would not show the process stayed healthy; the follow-up check does}}
        NotCompile{{A successful compilation would have proven neither route alignment nor safe failure}}
    end

    subgraph Review["Strict review, decided on evidence"]
        Accepted[(Three findings accepted, each corrected in its own commit: two-sided route test, explicit task test, YAML as a direct requirement, measured behavior in README)]
        Rejected[(Two findings rejected with reasons: the default problem response already covers 413, and the measured 200, 400 and 413 bodies were complete JSON)]
        NotAgreement{{A strong review does not require agreeing with every comment; both the fixes and the refusals cite the contract, code, tests, or measured responses}}
    end

    subgraph Next["What is not done yet"]
        CI{{Checks still run by hand; the goal is a pipeline where contract drift is a routine build failure}}
        Small{{One small API with two routes, so nothing here speaks to scale or production load}}
    end

    Reviewer -- "needs" --> Spec
    Repo -- "gives" --> Isolation
    Repo -- "carries" --> Attrs
    Repo -- "carries" --> Ignore
    Attrs -- "prevents" --> Noise
    Repo -- "verified against" --> Toolchain
    Toolchain -- "recorded first, giving" --> Separable
    Repo -- "first commit publishes" --> Spec
    Spec -- "paired with" --> Records
    Spec -- "is" --> First
    First -- "held under" --> NoRewrite
    First -- "lets reviewers" --> Challenge
    Challenge -- "exercised by" --> Reviewer
    Spec -- "implemented by" --> Module
    Module -- "registers" --> Router
    Module -- "kept under" --> Modest
    Router -- "serves" --> Health
    Router -- "delegates echo to" --> Limit
    Helpers -- "shape every response from" --> Router
    Client -- "sends bodies into" --> Limit
    Limit -- "then" --> ReadFail
    ReadFail -- "then" --> Malformed
    Malformed -- "then" --> Strict
    Strict -- "only on success" --> Success
    Limit -- "refusal returns through" --> Problems
    ReadFail -- "refusal returns through" --> Problems
    Malformed -- "refusal returns through" --> Problems
    Strict -- "refusal returns through" --> Problems
    Problems -- "bounded by" --> NoLeak
    Problems -- "is why" --> Terminal
    Spec -- "compared with the router by" --> BothWays
    Router -- "compared with the specification by" --> BothWays
    BothWays -- "proven by breaking it" --> BreakA
    BothWays -- "and by breaking it" --> BreakB
    BreakA -- "then" --> Restored
    BreakB -- "then" --> Restored
    Restored -- "is what avoids" --> OneWay
    Client -- "exercised as" --> Cases
    Cases -- "produced" --> Codes
    Codes -- "each followed by" --> Recheck
    Recheck -- "shows" --> Survived
    Codes -- "read beside route parity shows" --> NotCompile
    Success -- "never reached by" --> Cases
    Codes -- "cited in" --> Rejected
    BothWays -- "corrected by" --> Accepted
    Accepted -- "read beside" --> Rejected
    Rejected -- "demonstrates" --> NotAgreement
    Accepted -- "documented for" --> Reviewer
    NotAgreement -- "bounded by" --> CI
    Health -- "bounded by" --> Small

    class Attrs,Ignore,Toolchain,Spec,Records,Helpers,Health,Problems,BreakA,BreakB,Restored,Codes,Recheck,Accepted,Rejected datastore
    class Repo,Module,Router,Limit,ReadFail,Malformed,Strict,Success,BothWays,Cases service
    class Isolation,Noise,Separable,First,NoRewrite,Challenge,Modest,Terminal,NoLeak,OneWay,Survived,NotCompile,NotAgreement,CI,Small event
    class Reviewer,Client io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/contract-first-go-service.md`](./documents/contract-first-go-service.md).

## Implementation

This system is built across **6 phases**:

1. **Establishing a Contract-First Engineering Foundation**
2. **Publishing the API Promise Before Implementation**
3. **Making the Service Behavior Visible**
4. **Engineering a Secure Echo Endpoint**
5. **Proving Contract Integrity and Safe Failures**
6. **Completing Review and Releasing the Service**

For the full walkthrough with screenshots and step-by-step content, see [`documents/contract-first-go-service.md`](./documents/contract-first-go-service.md).

## Validation

Each build phase below is documented in [`documents/contract-first-go-service.md`](./documents/contract-first-go-service.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Establishing a Contract-First Engineering Foundation
- ✅ Publishing the API Promise Before Implementation
- ✅ Making the Service Behavior Visible
- ✅ Engineering a Secure Echo Endpoint
- ✅ Proving Contract Integrity and Safe Failures
- ✅ Completing Review and Releasing the Service
