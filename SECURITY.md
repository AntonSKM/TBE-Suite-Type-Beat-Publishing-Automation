# Security & IP Notes

This repository is a public product/engineering showcase, **not the production source repository**.

## Not included

- OAuth client secret files
- access or refresh tokens
- `.env` production values
- user databases
- user media libraries
- production backend implementation
- provider-specific scraping selectors/parsers
- internal orchestration and reconciliation algorithms

## Product security principles

The production application is designed around:

- backend-only OAuth secrets;
- user/channel ownership checks;
- user-scoped media storage;
- verification that an OAuth authorization corresponds to the intended YouTube channel;
- no secrets/tokens in ordinary logs;
- conservative handling of incomplete third-party API responses.

## Intellectual property

Screenshots and documentation are published to demonstrate the product and engineering work. The underlying production implementation remains private and is not licensed for reuse through this repository.
