# ADR 0001: Entity Metadata

## Changelog

- 2020-09-29: Make keybase/twitter fields non-ambiguous
- 2020-08-21: Fix maximum name length
- 2020-08-05: Initial version

## Status

Proposed

## Context

In order to support distributing additional entity metadata which is not
otherwise required for on-chain operation we introduce an entity metadata
registry containing self-certifying entity statements.

## Decision

At a high level, this proposal introduces a new metadata registry provider
interface currently containing only a single method:

* `GetEntity(id) -> EntityMetadata`

The method is given an entity identifier (currently an Ed25519 public key) and
returns entity metadata from the registry after performing the necessary
verification.

### Entity Metadata Statements

The entity metadata structure is a document encoded using [canonical CBOR as
defined by RFC 7049] with the following fields:

* `v`: format version (uint16, required, must be `1`)
* `serial`: the serial number of the entity metadata statement where the highest
  serial number should be treated as the most recent (uint64)
* `name`: an entity name (string, optional, max 50 characters)
* `url`: an URL associated with the entity (string, optional, max 64 characters,
  must be a valid URL using the scheme `https` without any query or fragments)
* `email`: an e-mail address associated with the entity (string, optional, max
  32 characters, must be a valid e-mail address)
* `keybase`: a keybase.io handle (string, optional, max 32 characters, must
  match the regular expression `^[A-Za-z0-9_]+$`)
* `twitter`: a Twitter handle (string, optional, max 32 characters, must match
  the regular expression `^[A-Za-z0-9_]+$`)

The entity metadata document is signed using the [signed envelope] used by Oasis
Core with the following [domain separation context]:

```
oasis-metadata-registry: entity
```

The maximum size of the signed statement encoded in JSON is 16384 bytes.

<!-- markdownlint-disable line-length -->
[canonical CBOR as defined by RFC 7049]: https://tools.ietf.org/html/rfc7049
[signed envelope]: https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/common/crypto/signature?tab=doc#Signed
[domain separation context]: https://github.com/oasisprotocol/oasis-core/blob/master/docs/crypto.md#domain-separation
<!-- markdownlint-enable line-length -->

### Filesystem Provider

This proposal also standardizes a filesystem-based provider that uses a
filesystem implementation for storing the signed metadata statements.

We assume the filesystem base directory is `/`. The top-level directory must be
located `/registry`.

Entity metadata statements must be stored as a subdirectory named `entity`,
giving the relative path `/registry/entity`.

Each entity metadata statement must be stored in its own file named based on
the hexadecimal encoding of the entity public key that signed the statement, for
example:

```
/registry/entity/5fb36d105a6c85a21542abdd712c292ae37425e842b38db450970cdef0780bd8.json
```

The signed statement must be the [signed envelope] encoded in JSON format.

### Git Provider

As an extension of a filesystem-based provider this proposal standardizes Git
repositories as a way to distribute signed statements. The conventions defined
for the filesystem provider are used.

## Consequences

### Positive

- No additional data needs to be maintained by the consenus layer.

- Additional entity metadata can be distributed in a decentralized fashion by
  distributing Git repositories.

- All statements can be verified independently.

### Negative

- Revoking a previous statement by bumping the serial number depends on the new
  statement being published in the registry.

## References

- [oasis-core#2932](https://github.com/oasisprotocol/oasis-core/issues/2932)
