# GraphQL Query Registration & Persisted Queries

## Why Persist Queries

A Graphql operation can be executed using an operation's ID instead of the entire operation string.
 - An operation's ID is a hash of the full query string.
 - Hashes can be generated using various hashing techniques. We use `SHA-256` which produces `consistent hashes`.

> PROs
 - Using operation ID enables us to use HTTP `GET` instead of the default `POST`, making them easier to cache.
 - It adds additional security to queries since server only responds if the operation ID matches the registered IDs in server.

> CONs
 - Generating hash is automated using codegen, but adds into the build time.
 - Everytime a hash is generated/updated `mGQL` needs to be updated via GitOps which adds into the build time.
 - Passing hash as query makes network query inspection more difficult.

#### How PQ generation works

```mermaid
sequenceDiagram
  autonumber
    participant User
    participant Local
    participant Codegen
    participant Remote(PR)
    participant Remote(main)
    
    User->>Local: Creates / updates graphql file
    
    Local->>Codegen: User runs codegen / Pre-commit hook triggers codegen
    Note right of Codegen:* Generates types <br/>* Runs posthook & generates operations.json file<br/>* Runs posthook & updates graphql.yml file

    Codegen->>Local: 

    Local->>Remote(PR): User pushes code to remote & creates PR 
    Note right of Remote(PR): Build is triggered
    
    Remote(PR)->>Remote(main): PR is merged
    Note right of Remote(main): Build is triggered

    Remote(main)->>Remote(main): DEV build & deploy
    Remote(main)->>Remote(main): QA deploy
    Remote(main)->>Remote(main): STAGE deploy
    Remote(main)->>Remote(main): PREPROD deploy
    Note right of Remote(main): GitOps
```

#### How PQ is registered
> TODO

#### How client handles PQ calls
> TODO
