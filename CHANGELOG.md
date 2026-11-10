# Changelog

Notable changes to the Sangfor HCI extension, newest first. The version is the `version` in
[`extension/extension.yaml`](extension/extension.yaml); Dynatrace rejects a version it already has, so every upload
needs a new one. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

Pull request numbers refer to the upstream repository,
[rifqifitriady/dt-sangfor-ext](https://github.com/rifqifitriady/dt-sangfor-ext).

## [0.1.1] - 2026-11-08

Makes the package upload on Dynatrace Managed 1.348. Validated there with Environment ActiveGate 1.347.
Version 0.1.0 is rejected by that cluster; use 0.1.1.

Only the screens changed. The Python code, metrics, topology, dashboard and configuration form are the same as in
0.1.0, so existing monitoring configurations need no changes.

### Fixed

- Upload failed with `required property 'mode' not found` and `required property 'displayIcons' not found`.
  Schema 1.348 requires both on every screen card. Added the schema defaults, `mode: NORMAL` on the 5 charts cards and
  `displayIcons: true` on the 7 entity list cards, so the screens look the same.
  ([#2](https://github.com/rifqifitriady/dt-sangfor-ext/pull/2))
- Upload failed with `Invalid entity selector` (target `PLATFORM`) on the four list screens (cluster, host, VM,
  datastore). A plain `type("sangfor_hci:…")` selector is checked against the entity types the cluster already has,
  and on a first install those types do not exist yet, because the same upload creates them. The selectors now end with
  `,$(entityConditions)`, which makes them templates that are checked when the page is rendered, like the relationship
  cards already were. ([#2](https://github.com/rifqifitriady/dt-sangfor-ext/pull/2))

### Added

- Tests for the screens section, which nothing covered before. They fail on the 0.1.0 `extension.yaml`:
  every charts card has `mode`, every entity list card has `displayIcons`, every entity list selector contains
  `$(entityConditions)`, and every screen layout references only cards that exist.
  ([#3](https://github.com/rifqifitriady/dt-sangfor-ext/pull/3))

### Changed

- Version 0.1.1 in `extension/extension.yaml`, `activation.json` and the bug report template.
  ([#3](https://github.com/rifqifitriady/dt-sangfor-ext/pull/3))
- README states the validated Managed and ActiveGate versions. Design spec question Q7 (minimum Managed version)
  records what 1.348 validates. ([#3](https://github.com/rifqifitriady/dt-sangfor-ext/pull/3))

### Upgrading from 0.1.0

Build, sign and upload 0.1.1 as described in [Releases](CONTRIBUTING.md#releases). No configuration changes are needed.

## [0.1.0] - 2026-11-07

First release: a Python Extension 2.0 for Dynatrace Managed that runs on an Environment ActiveGate (Python 3.10 and
3.14 runtimes) and monitors Sangfor HCI read-only.

### Added

- 46 metrics: clusters, hosts, VMs and datastores from the Sangfor REST API, and hardware health over SNMP v2c or v3
  (CPU temperature, fans, RAID, SSD life, filesystems and optical power). Collection mode `both`, `api` or `snmp`;
  in `both` mode SNMP keeps host CPU and memory flowing when the API is down.
- Sangfor alarms as Dynatrace events on the cluster, host or VM they concern: urgent alarms as error events, the rest
  as info.
- Topology: cluster, host, VM and datastore entity types, with hosts and datastores as children of their cluster and
  each VM running on its host.
- Screens for the four entity types and an overview dashboard.
- A login that is never resent, with a 5 to 60 minute back-off after any failed login, so a wrong password cannot lock
  out the service account.
- A mock Sangfor API and snmpsim agents for local runs, `scripts/check_package.py` for package checks, and a read-only
  validation kit for a real HCI with a redacted output zip.

[0.1.1]: https://github.com/rifqifitriady/dt-sangfor-ext/compare/e00e8ff...96f33a8
[0.1.0]: https://github.com/rifqifitriady/dt-sangfor-ext/tree/e00e8ff
