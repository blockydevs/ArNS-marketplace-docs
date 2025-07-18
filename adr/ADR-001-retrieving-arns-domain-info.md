# Retrieval of ArNS Domain (ANT) Information

## Status: draft 🚧

## Context and Problem Statement

Arweave’s Name System (ArNS) uses *Arweave Name Tokens* (ANTs) to represent ownership of human-readable names. Each registered name is tied to a SmartWeave contract (an “ANT”) whose state holds all details about that domain: its name, owner, controllers, and pointer records. We need to decide **how to retrieve domain information** in two main scenarios: (a) *listing all domains (ANTs) owned or controlled by a given wallet address*, and (b) *fetching all detailed data for a specific domain*. The solution should reliably return the domain’s metadata (name, ticker, description, logo, records, etc.), owner and controllers, and any associated pointer records.

## Decision Drivers

* **Complete Information:** Must retrieve *all* relevant data about a domain (name, owner, controllers, description, logo, DNS records/undername pointers) to display full details.
* **Owned/Controlled Domains:** Must list every ANT (domain) for which a wallet is owner or controller.
* **Use of Existing Tools:** Prefer leveraging official libraries or protocols (e.g. AR.IO SDK, contract queries) rather than custom scraping.
* **Simplicity and Reliability:** Approach should minimize complexity (e.g. no manual parsing of raw transactions) and use supported APIs.
* **Protocol Compliance:** Must use the official ArNS (ANT) contracts and registry mechanisms to ensure accuracy.
* **Performance/Scalability:** Solution should not require scanning the entire blockchain for each query.

## Considered Options

* **AR.IO SDK (ANTRegistry and ANT classes).** Use the AR.IO JavaScript SDK (e.g. `@ar.io/sdk`) which provides high-level methods to query the ANT Registry and individual ANT contracts.
* **Arweave/AR.IO GraphQL API.** Use GraphQL queries (via an AR.IO gateway or Arweave node) to fetch contract data directly or via indexed views (for example, the AR.IO `IO.getArNSRecord` endpoint to map a name to its ANT process).
* **Direct SmartWeave Contract Queries (Warp or similar).** Use a SmartWeave query library (like Warp Contracts) to read the raw contract state for the registry or an ANT directly, without AR.IO abstractions.

## Decision Outcome

Chosen option: **AR.IO SDK (ANTRegistry and ANT classes)**, because it directly provides the needed functionality. The SDK’s `ANTRegistry.accessControlList` method returns exactly the list of ANT process IDs owned or controlled by an address. Given a domain name or ANT ID, the SDK’s `ANT.getState()` (or `getInfo()`) method returns the full state of that ANT, including owner, controllers, DNS records, and metadata. The AR.IO SDK abstracts away contract details and yields data in ready-to-use form, so it meets both our *listing* and *detailed info* requirements with minimal extra work. It also ensures we follow the official ArNS/ANT protocol.

* **Consequences:**

  * *Good:* All required data (records, owner, controllers, etc.) is obtained from standard SDK calls, reducing custom code. Official support means less chance of breaking changes.
  * *Good:* We build on existing infrastructure and can configure network endpoints (via `@permaweb/aoconnect` or Warp) as needed.
  * *Bad:* Ties our solution to the AR.IO ecosystem and SDK; if the SDK or underlying contract evolves, updates may be needed.
  * *Bad:* Must ensure AR.IO connectivity (gateway/network) is available; offline or raw query alternatives become harder.
  * *Bad:* Slightly higher dependency overhead (importing and learning the AR.IO SDK) than a raw query.

## Pros and Cons of the Options

### AR.IO SDK (ANTRegistry + ANT class)

* **Good:** Provides exactly what we need. The `accessControlList({address})` call returns arrays of ANT process IDs for owned and controlled domains, and the `ANT` class (via `getState()`, `getOwner()`, `getControllers()`, `getRecords()`) returns all domain details in one call. No need to manually filter or parse transactions.
* **Good:** Well-documented official API. Future changes in the ArNS protocol will likely be reflected in the SDK updates.
* **Neutral:** Requires including the AR.IO SDK or AO Connect library, which may be overkill for a very simple use-case.
* **Bad:** Introduces a dependency on AR.IO’s network and SDK; if those services have issues, our retrieval breaks.

### Arweave/AR.IO GraphQL API

* **Good:** Can fetch data via AR.IO gateway or Arweave node using GraphQL queries, without requiring the AR.IO SDK. For example, `IO.getArNSRecord({name})` (part of AR.IO’s GraphQL interface) returns the ANT process ID for a name. GraphQL can also list registry entries.
* **Neutral:** GraphQL can paginate domain listings and return raw fields, but further processing is needed to get full state (owner/controllers).
* **Bad:** Contract state (owner/controllers, records) isn’t directly accessible via simple GraphQL queries without knowing the correct schema. We would need to query the SmartWeave contract (via warp or scan tags), making it effectively a manual query.
* **Bad:** More brittle if the GraphQL schema or endpoints change. Building comprehensive queries for nested contract state is complex.
* **Bad:** A transaction takes time to be accounted into an Arweave block, which can impact UX.

### Direct SmartWeave Contract Queries (Warp)

* **Good:** Directly read the SmartWeave contract state (either the ArNS registry or an ANT contract) via Warp or similar. This ensures we see the exact on-chain state. No reliance on AR.IO-specific tooling.
* **Good:** No external service dependency beyond Arweave node.
* **Bad:** Requires writing and maintaining custom query logic. For example, to list domains, one would need to query the registry contract or scan all `register` interactions. To get domain details, one must call the ANT contract’s state via Warp.
* **Bad:** Greater implementation effort and potential for errors. The AR.IO SDK already encapsulates this logic.

## More Information

* An ANT’s complete state (including owner, controllers, records) is available via the SDK’s `getState()` or `getInfo()` calls.
* The ANTRegistry is the contract that tracks all ANTs by owner/controller; using its `accessControlList()` is the standard way to list domains for a user.
* Example usage:

  ```js
  const registry = ANTRegistry.init();
  const { Owned, Controlled } = await registry.accessControlList({ address: "WALLET_ADDRESS" });
  for (const antId of Owned) {
    const ant = ANT.init({ processId: antId });
    const state = await ant.getState(); 
  }
  ```
* Mapping a domain name to its ANT process ID can be done via `IO.getArNSRecord({name})`; from there, the same `ANT` class applies.
* We should periodically review this decision if the AR.IO SDK or ArNS protocols change significantly.

**Sources:** AR.IO documentation on ANTs and SDK methods, ARNS community guides.

- [ARIO SDK](https://www.npmjs.com/package/@ar.io/sdk/v/2.4.0-alpha.1)
- [ARnS Cookbook](https://cookbook.arweave.net/concepts/arns.html)
- [ARIO articles](https://ar.io/articles/what-is-an-ant)
- [ARIO Docs](https://docs.ar.io/arns)

