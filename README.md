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

3. Use the [`oasis-cli`] (minimum version 0.7.1) command to generate a new
   signed statement.
   For example:

   ```bash
   oasis account entity metadata-update \
      --account my-entity-account \
      --registry-dir /path/to/metadata-registry \
      /path/to/entity-metadata.json
   ```

   _Note that for `--registry-dir` you must specify the path to the top-level
   `metadata-registry` directory (e.g. the one containing this README file) and
   not any of its subdirectories._

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
[`oasis-cli`]: https://docs.oasis.io/general/manage-tokens/cli/
[ADR 0001]: docs/adr/0001-entity-metadata.md
<!-- markdownlint-enable line-length -->

## Directories

* `docs`: Documentation and specifications for the registry.
* `registry`: Actual registry of signed statements.
