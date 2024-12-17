Ansible: Opencast OpenSearch Role
====================================

![lint](https://github.com/elan-ev/opencast_opensearch/actions/workflows/lint.yml/badge.svg)
![molecule](https://github.com/elan-ev/opencast_opensearch/actions/workflows/molecule.yml/badge.svg)

This Ansible role installs and prepares OpenSearch for Opencast.

This role supports the following,

- Supports RHEL9, Debian and Ubuntu
- Install and configure OpenSearch from `elan.opencast_repository`
- Install analysis-icu OpenSearch plugin (required by Opencast 17+)
- Disables the OpenSearch security plugin completely. Use a reverse
  proxy to secure OpenSearch with HTTP Basic Auth and TLS.

## Role Variables

- `opencast_repository_identifiers`
  - List of repository identifiers to temporarily activate for integration
  - Will usually be provided by the [elan.opencast_repository](https://github.com/elan-ev/opencast_repository) role
- `opencast_opensearch_heap_size`
  - Memory configuration (default: `1g`)
  - Might make sense to set this to `2g` for larger installations.
- `opensearch_api_host`
  - Defaults to `127.0.0.1`.
- `opensearch_api_port`
  - Defaults to `9200`.

## Dependencies

This role depends on `elan.opencast_repository`.

## Example Playbook

Example of how to configure and use the role:

```yaml
- hosts: servers
  become: true
  roles:
    - role: elan.opencast_opensearch
```

## License
[BSD-3-Clause](LICENSE)

## Author
[ELAN e.V.](https://elan-ev.de)
