# Cancelling an auction

- Status: Discussion 💡

## Context and Problem Statement

In the ArNS Marketplace running on AO, auctions (both Dutch and English) are smart contract processes executed entirely within AO compute. Currently, the marketplace enables sellers to list assets (e.g. ANT tokens) for sale, but the mechanism for cancelling an active auction has not yet been finalized.

There are multiple ways an auction cancellation could be handled — ranging from issuing a specific cancel message to invoking state expiration or token recall. This decision impacts both user experience and protocol trust guarantees.

We need to define:
- How a seller can cancel their own auction.
- What the exact technical flow is (within AO and ANT).
- How we prevent abuse or unintended cancellations.
- Whether cancellation should emit a protocol-level event.

## Decision Drivers

[Identify key factors that influence the decision, such as
requirements, constraints, and other considerations.]

- [driver 1]
- [driver 2]
- [driver 3]

## Considered Options

[List the options that were considered to address the problem, providing a brief
overview of each.]

- [option 1]
- [option 2]
- [option 3]

## Decision Outcome

[Describe the decision that was made, including why it was chosen over the
other options.]

### Positive Consequences

[Highlight the benefits and positive outcomes expected from this decision.]

- [consequence 1]
- [consequence 2]

### Negative Consequences

[Identify any drawbacks or negative outcomes that might result from this decision.]

- [consequence 1]
- [consequence 2]

## Pros and Cons of the Options

[Compare the pros and cons of each considered option.]

### [option 1]

- `+` [pro 1]
- `+` [pro 2]
- `-` [con 1]
- `-` [con 2]

### [option 2]

- `+` [pro 1]
- `+` [pro 2]
- `-` [con 1]
- `-` [con 2]

### [option 3]

- `+` [pro 1]
- `+` [pro 2]
- `-` [con 1]
- `-` [con 2]

## Links

[Include any relevant links to documents, discussions, or other resources that
provide additional context or background information.]

- [link 1](url)
- [link 2](url)

## Related Decisions

[List any related ADRs or decisions that are connected to this one.]

- [ADR-1](1-example.md) - [Title of ADR-1]
- [ADR-2](2-example.md) - [Title of ADR-2]

## Notes

[Include any additional notes or comments that are relevant to the decision.]

---

[ADR Template]: https://adr.github.io/