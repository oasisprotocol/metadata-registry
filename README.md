# Oasis Metadata Registry

This repository contains an off-chain registry of signed statements about
various entities that may exist on the Oasis Network. Each statement is
self-certifying and should be independently verified before being used.

For tooling that can be used to verify and access the signed statements
programatically see the [metadata-registry-tools] repository.

<!-- markdownlint-disable line-length -->
[metadata-registry-tools]: https://github.com/oasisprotocol/metadata-registry-tools
<!-- markdownlint-enable line-length -->

## Contributing New Statements

New statements can be contributed by opening a pull request.

To create an entity metadata statement using [metadata-registry-tools], use the
following steps:

1. Create an `entity-metadata.json` file describing the metadata statement, for
   example something like (most fields can be omitted):

   ```json
   {
     "v": 1,
     "serial": 1,
     "name": "My entity name",
     "url": "https://my.entity/url",
     "email": "my@entity.org",
     "keybase": "keybase_handle",
     "twitter": "twitter_handle"
   }
   ```

   Note that `keybase` and `twitter` fields should contain handles (usernames)
   not URLs. See [ADR 0001] for the description of all the field formats.

   In case you are updating an existing entry, you must bump the serial number.

2. Fork this repository.

3. Use the [`oasis-registry`] command to generate a new signed statement. The
   command must be run in the directory where you cloned this repository. This
   will require the use of an Oasis Core signer (all the same signers and
   configuration flags are supported). For example, using a file-based signer:

   ```bash
   oasis-registry entity update \
     --signer.dir /localhostdir/entity \
     /path/to/entity-metadata.json
   ```

   This will create (or update) the registry entry.

4. Commit your changes in the `registry/` directory and create a pull request.
   Make sure to NOT commit the plain `entity-metadata.json` file in case you
   created it in the same directory. The same metadata can be extracted from the
   generated signed entity statement.

5. Automated checks will make sure that the signed statement is valid and that
   the update has been performed correctly.

6. If all checks pass your pull request will be approved and merged. Once this
   happens the updated entity metadata will be available for queries.

<!-- markdownlint-disable line-length -->
[`oasis-registry`]: https://github.com/oasisprotocol/metadata-registry-tools
[ADR 0001]: docs/adr/0001-entity-metadata.md
<!-- markdownlint-enable line-length -->

## Directories

* `docs`: Documentation and specifications for the registry.
* `registry`: Actual registry of signed statements.
