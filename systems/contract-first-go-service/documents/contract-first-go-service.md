<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Your First Go Service, Contract First

**Project Link:** [View Project](https://nextwork.ai/projects/d2b861ee-767a-4da1-bd54-8627ac1fdc9b)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/d2b861ee-767a-4da1-bd54-8627ac1fdc9b_np71w3xm)

## Establishing a Contract-First Engineering Foundation

### Project purpose and contract-first rationale

I built a contract-first Go service to practice defining an API before implementing its handlers. The service included health and echo endpoints, strict request validation, predictable error responses, and tests that compared the running application with its published specification. I used OpenAPI 3.1.2 to describe routes, request bodies, response codes, and schemas before adding service code. This order gave reviewers a stable promise against which they could judge the implementation. I also maintained the design records, tests, measured responses, and review decisions in Git. Documentation was therefore part of the acceptance boundary rather than a summary written after development. The completed build demonstrated how even a small API could have explicit behavior, controlled failure paths, traceable engineering decisions, and evidence capable of revealing drift between the documented contract and the service.

### Verifying the development environment

I verified the inherited Windows toolchain before creating the service. I checked Go, Git, repository settings, and editor behavior, then created first-service as a sibling of Project 1. This placement gave the service its own .git directory, local main branch, and commit history, preventing its files from affecting the earlier project. I recorded the environment checks before moving into contract design so later failures could be separated from workstation problems. I also established repository controls at the start. The .gitattributes and .editorconfig files required UTF-8 text and LF line endings, which prevented Windows CRLF changes from creating noisy diffs. The .gitignore file excluded big.json, which was needed for request-limit testing but did not belong in permanent history. After committing these controls, I confirmed that the working tree was clean and ready for implementation.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/d2b861ee-767a-4da1-bd54-8627ac1fdc9b_wueb3e97)

### How early checks protect the build

The early checks protected the build by separating first-service from Project 1 and fixing repository behavior before any contract or Go source was added. Because the service had its own local main history, every commit applied only to the new API. An accidental edit, staging command, or release action could not silently alter the neighboring project. This boundary also gave reviewers a clean sequence showing environment setup, contract publication, implementation, testing, and release as distinct stages. The text controls reduced quieter risks. The .gitattributes and .editorconfig files forced UTF-8 and LF endings, preventing Windows CRLF changes from hiding meaningful differences. The .gitignore rule kept big.json outside version control even though it supported oversized-body testing. Together, these controls reduced platform noise, protected repository history, and made the later evidence easier to inspect and reproduce.

## Publishing the API Promise Before Implementation

### Publishing the contract and design record

I published the OpenAPI contract as the repository’s first behavioral commitment. It defined the health and echo routes, accepted JSON structure, successful responses, and problem responses before any handler existed. I also recorded the main design choices, including bounded request bodies, strict decoding, stable error shapes, and contract-to-router comparison. Committing these artifacts first created a visible boundary between what the service promised and how I later implemented it. The Git history therefore became useful evidence rather than a simple record of changed files. A reviewer could inspect the specification on main, assess the interface, and identify missing cases without reading Go code. Later commits could then be compared with that frozen promise. If a route, status code, or schema differed, the implementation had to be corrected or the contract deliberately revised. The specification could not be rewritten afterward merely to make existing code appear compliant.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/d2b861ee-767a-4da1-bd54-8627ac1fdc9b_a43zsgyf)

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/d2b861ee-767a-4da1-bd54-8627ac1fdc9b_atr2aq7k)

### Why the documented contract leads the code

The documented contract led the code because it stated the externally visible behavior before implementation choices could influence that description. OpenAPI identified each route, method, request body, media type, status code, and response schema while the service still contained no handlers. Reviewers could therefore challenge the API design independently of its internal construction. The specification became the promise, while the Go service became one implementation required to satisfy it. This order also changed how I handled disagreement. If the router omitted a documented path, returned a different status, or accepted fields outside the schema, I treated that as measurable drift rather than quietly changing the specification. Any intentional interface change required an explicit contract revision. The comparison worked in both directions: every documented route needed code, and every implemented route needed documentation. That made the contract testable instead of decorative.

## Making the Service Behavior Visible

### Assembling the Go service skeleton

I assembled the Go service around a small structure that kept startup, routing, handlers, and tests easy to inspect. I initialized the module, added the direct YAML dependency needed to read the OpenAPI document, and created the executable entry point. The router registered the documented health and echo endpoints, while shared helpers produced consistent JSON and problem responses. I kept the structure modest because the purpose was to expose boundary behavior rather than hide it behind a large framework. I then made the service executable through repeatable tasks and tests. The health route returned {"status":"ok"}, providing a simple liveness check before and after error cases. The echo route delegated parsing and validation to a bounded handler instead of accepting arbitrary input. Tests compared OpenAPI paths with registered routes so drift could be detected in either direction. This skeleton connected each observed response to specific code, tests, and contract statements.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/d2b861ee-767a-4da1-bd54-8627ac1fdc9b_wit1tj96)

## Engineering a Secure Echo Endpoint

### Implementing bounded, strict request handling

I implemented handleEcho as a five-step request boundary rather than a permissive decoder. First, the handler limited how many bytes it could read, preventing an oversized request from consuming unbounded memory. Second, it rejected body-read failures. Third, it decoded the JSON and refused malformed content. Fourth, it rejected unknown fields and empty messages so the accepted input matched the published schema. Only after those checks succeeded did the fifth step return the supplied message as JSON with 200 OK. The failure behavior was equally deliberate. Invalid requests received fixed RFC 9457-style problem details instead of raw parser messages or internal implementation information. Oversized content returned 413, while unknown fields and empty bodies returned 400. Each refusal ended the handler immediately, preventing execution from falling through to the success writer. Only input satisfying every contract condition could reach the echo response.

### Tracing the five-step refusal sequence

The five-step sequence made every refusal explicit and terminal. A request first encountered the size boundary, which stopped bodies beyond the accepted limit before normal decoding. If reading failed, the handler returned a problem response and exited. The JSON decoder then rejected malformed content, after which strict field validation refused properties not named in the contract. The final validation rejected an empty message. Every failed condition returned before writeJSON, so invalid input could not accidentally receive a success status or be echoed back to the caller. Only a request that passed all four refusal checks reached step five. At that point, the handler set application/json, wrote 200 OK, and returned the validated message in the documented response shape. The success path was therefore not the default branch. It was the result of satisfying every stated condition, which made the endpoint’s behavior easier to inspect, test, and explain.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/d2b861ee-767a-4da1-bd54-8627ac1fdc9b_uorsuyt3)

## Proving Contract Integrity and Safe Failures

### Demonstrating route-drift detection

I demonstrated route-drift detection by intentionally creating mismatches between the OpenAPI paths and the Go router. The test checked both directions: a route documented without an implementation and a route implemented without documentation. Each deliberate mismatch caused the task to fail and identified the missing side. This showed that the comparison did more than search for a few expected route names. Restoring parity returned the suite to a passing state. I also sent invalid requests to the running service to test behavior beyond static route comparison. The exercised cases included an oversized body, an undocumented field, and an empty message. I recorded their status codes and problem bodies, then checked /healthz after each refusal. This evidence demonstrated two different properties: the published and implemented route sets remained aligned, and malformed client input did not terminate the process. A successful compilation alone would not have proven either condition.

### Evidence of safe request refusals

The runtime evidence showed that every invalid echo request was refused with a stable response. The oversized body returned 413, while the request containing an extra field and the request containing an empty message each returned 400. Their bodies followed the documented problem format and did not expose decoder internals, stack information, or fragments of rejected input. None of the three requests reached the success writer, so invalid data was never returned as though it had passed validation. I checked /healthz after each refusal, and it continued to return {"status":"ok"}. That follow-up mattered because receiving an error response would not, by itself, prove that the process remained healthy. The repeated health checks showed that the handler contained each bad request and stayed ready for later work. Together, the status codes, fixed problem bodies, absence of parser leakage, and successful health checks demonstrated safe failure behavior.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/d2b861ee-767a-4da1-bd54-8627ac1fdc9b_6dm9w5e7)

## Completing Review and Releasing the Service

### Resolving named strict-review findings

I completed the strict review by recording each finding, deciding whether to accept or reject it, and supporting that decision with repository evidence. I accepted three findings and resolved them through separate commits. The route test was updated to identify both sides of drift, the task test workflow became explicit, the YAML module was recorded as a direct requirement, and the measured service behavior was added to README.md. Keeping the changes separate made each accepted finding traceable to its correction. I rejected two findings with documented reasons. The suggested missing 413 response was unnecessary because the OpenAPI default problem response already covered that status, and the measured body matched the declared Problem schema. I also rejected the encode-error concern because the helpers were supplied and the measured 200, 400, and 413 responses contained complete JSON. Strict review required evidence, not automatic agreement with every comment.

## Reflecting on Contract-First Delivery

### Tools and concepts applied

I used Go 1.27.1 and the standard net/http package to build the service, OpenAPI 3.1.2 to define its public interface, and Git to preserve the order from contract through implementation and review. GitHub Issues and Project Boards supported work tracking, while repository tasks and tests provided repeatable checks. The central practice was contract-first development: the API promise existed before the handlers and became the basis for later validation. I also implemented bounded request reading, strict JSON decoding, rejection of undocumented fields, and RFC 9457 problem responses that avoided exposing parser internals. Two-way route checks detected both missing implementations and undocumented additions. The attributed review added another lesson: a strong review process does not require accepting every finding. It requires accepted fixes and rejected recommendations to be supported by the same contract, source code, tests, and measured runtime evidence.

### Time investment and challenges

I completed the build in approximately 60 minutes. The most demanding part was the strict review because every finding required a specific decision supported by the OpenAPI contract, tests, measured responses, or source structure. It was not enough to say that a comment appeared reasonable or unnecessary. Accepted findings needed isolated changes, while rejected findings needed evidence showing why the existing behavior already met the requirement. Route parity required particular care because a one-way check could create false confidence. Verifying only that contract paths existed in code would miss undocumented implementation routes, while checking only implemented routes would miss promises with no handler. I therefore ensured that the test named both sides of drift and that task test exercised the comparison consistently.

### Next learning goals

I completed this work to strengthen my contract-first Go development skills, especially publishing an OpenAPI 3.1.2 specification before implementation, enforcing strict request boundaries, and proving route parity through automated checks. The service gave me practical experience with body-size limits, unknown-field rejection, empty-input validation, stable JSON responses, and RFC 9457 problem details. It also showed how Git history and review records could prove that the API promise preceded the code. My next goal is to move these controls into an automated CI/CD pipeline. I want each proposed change to validate the OpenAPI document, run Go tests, compare documented and implemented routes in both directions, and exercise representative success and problem responses before merge. I also want to refine the problem-detail implementation by testing content types, error identifiers, and response consistency across additional handlers, making contract drift a routine build failure instead of a manual review step.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/d2b861ee-767a-4da1-bd54-8627ac1fdc9b)*
