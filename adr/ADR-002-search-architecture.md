# Domain Search Architecture in ARNS Marketplace

Status: Draft 🚧

## Context and Problem Statement

The ARNS (Arweave Name Service) marketplace, deployed as a smart process on AO, should allow users to search available or listed domain names (ANTs). Since it is a decentralized application (dApp), the solution must avoid reliance on centralized or third-party backends while still providing reasonable performance.

This ADR aims to determine the most suitable architecture for implementing domain search within the ARNS marketplace. It should balance decentralization, performance, and feasibility within the AO Lua smart process environment.

## Decision Drivers

- Must preserve decentralization: no reliance on external backend APIs.
- Performance and efficiency for large datasets (~100,000 records).
- Simplicity of querying from the frontend.
- Compatibility with AO and Lua memory/storage limits.

## Considered Options

- **Option 1**: Perform full-text search entirely on-chain (in Lua).
- **Option 2**: Use Arweave GraphQL endpoint for listing retrieval, then filter/search client-side.
- **Option 3**: Hybrid: cache recent listings on client-side (in-memory only), fetched from AO via `results()` or similar APIs.

## Decision Outcome

**Chosen Option**: Option 2 – Use GraphQL for indexed read access, and implement filtering and search logic client-side.

Although Option 1 would offer full decentralization, it introduces significant performance and scalability limitations within Lua’s memory constraints. Option 2 leverages already-indexed Arweave data and enables efficient filtering/searching from the frontend while maintaining data provenance and decentralization.

### Positive Consequences

- Fast, efficient searches from the frontend.
- No centralized backend is introduced.
- Compatibility with existing GraphQL infrastructure (e.g., `arweave.net/graphql`).

### Negative Consequences

- Relies on Arweave indexers, which are not part of AO.
- Indexing delay due to block confirmation times.
- Full decentralization is slightly compromised by dependency on external GraphQL infra.

## Pros and Cons of the Options

### Option 1 – Full Lua-based search

- `+` Fully on-chain and decentralized.
- `+` Always live and AO-native.
- `-` Poor performance at scale (Lua table search is linear).
- `-` Memory limits on AO smart processes.

### Option 2 – GraphQL read + client-side search (Chosen)

- `+` Efficient and scalable.
- `+` Familiar and easy to implement in FE (e.g., React).
- `-` Dependent on GraphQL indexers.
- `-` Delay in reflecting newly created listings.

### Option 3 – Client-side cached AO results

- `+` Query via `results()` endpoint directly from AO.
- `+` Fast once loaded.
- `-` Still limited by pagination and eventual indexing.
- `-` Requires persistent frontend state/cache.

## Links

- [AR.IO Docs](https://docs.ar.io/)
- [AR.IO ANT Process GitHub](https://github.com/ar-io/ar-io-ant-process)
- [Cookbook AO](https://cookbook_ao.arweave.net/)
- [@permaweb/aoconnect on npm](https://www.npmjs.com/package/@permaweb/aoconnect)
- [@wanderapp/connect on npm](https://www.npmjs.com/package/@wanderapp/connect)

## Related Decisions

- [ADR: ANT Domain Info Retrieval](./adr-ant-domain-info.md)

## Notes

This ADR assumes Arweave indexers (e.g., GraphQL) remain a stable, censorship-resistant component of the Arweave ecosystem. If decentralized GraphQL alternatives become available, they may be used as a drop-in.

---

[ADR Template]: https://adr.github.io/
