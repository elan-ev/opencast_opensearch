Ansible: Opencast OpenSearch Role
====================================

![lint](https://github.com/elan-ev/opencast_opensearch/actions/workflows/lint.yml/badge.svg)
![molecule](https://github.com/elan-ev/opencast_opensearch/actions/workflows/molecule.yml/badge.svg)

> ℹ️ Use version 0.2.0 for Opencast 16 and below.

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
- `opencast_opensearch_api_host`
  - Defaults to `127.0.0.1`.
- `opencast_opensearch_api_port`
  - Defaults to `9200`.
- `opencast_opensearch_started`
  - By default, the OpenSearch service will (re)start if something has changed that requires the service to be restarted. This is done via the Ansible notification handler. However, if you expect the OpenSearch service to be running when you run this role, you can force the service to start by setting the value to `true`.
  - Defaults to `false` (make use of Ansible Handler to reach the state)
- `opencast_opensearch_remote_user`
- `opencast_opensearch_remote_password`
  - Since Opencast 16 OpenSearch should be reachable by Admin and Presentation Opencast node.
    To secure the connection from Opencast to OpenSearch an reverse proxy should be used.
    The Ansible role [elan.opencast_nginx](https://github.com/elan-ev/opencast_nginx) is recommended for this purpose.
    Defining `opencast_opensearch_remote_user` and `opencast_opensearch_remote_password` will put
    an Nginx virtual host reverse proxy configuration into `/etc/nginx/sites-enabled/`.
  - Defaults to `""`
- `opencast_opensearch_remote_host`
  - Reverse proxy host name.
  - Defaults to `inventory_hostname`
- `opencast_opensearch_remote_port`
  - Reverse proxy port
  - Defaults to `9243`


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

## SELinux

On systems with SELinux enabled, the reverse proxy configuration may fail du to SELinux restrictions.
In this case you may want to add this tasks to your playbook:

```yaml
- name: Install SELinux management libraries
  ansible.builtin.package:
    name:
      - python3-libsemanage
      - python3-libselinux
  when: ansible_selinux is defined and ansible_selinux.status == 'enabled'

- name: Allow reverse-proxy listen on port {{ opencast_opensearch_remote_port }}
  community.general.seport:
    ports: "{{ opencast_opensearch_remote_port | int }}"
    proto: tcp
    setype: http_port_t
    state: present
  when: >-
    ansible_selinux is defined and ansible_selinux.status == 'enabled' and
    opencast_opensearch_remote_port | int != 443

- name: Allow reverse-proxy connect to other services via http
  ansible.posix.seboolean:
    name: httpd_can_network_connect
    state: true
    persistent: true
  when: ansible_selinux is defined and ansible_selinux.status == 'enabled'
```

## License
[BSD-3-Clause](LICENSE)

## Author
[ELAN e.V.](https://elan-ev.de)
