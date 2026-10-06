# Contributing to PiesP projects

Use the target repository's contribution guide and supported environment.
Describe the user-visible problem, reproduction steps, expected result, and the
smallest relevant change. Keep unrelated cleanup and dependency upgrades out of
behavior fixes. Include the checks you ran and any skipped or unavailable scope.

For automation changes, follow the [ownership, command catalog and migration
rules](MAINTENANCE_POLICY.md#automation-ownership). Use the target project's
existing documentation and command surfaces for its implementation details.

Product-specific requirements remain in each repository: browser compatibility
and emitted artifacts, Windows file safety and source-bound candidate evidence,
and supported game builds and translation source hashes. Never bypass those
requirements to make a shared process uniform.

Do not include credentials, private media, local paths, or raw security
reproduction details in public reports. Follow the repository's SECURITY.md for
vulnerabilities. Maintainers review changes and validation before integration.
