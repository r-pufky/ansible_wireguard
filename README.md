# Wireguard
Wireguard installation from public release tarball.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_wireguard/blob/main/meta/main.yml)

[collections/roles](https://github.com/r-pufky/ansible_wireguard/blob/main/meta/requirements.yml)

## Role Variables
[defaults](https://github.com/r-pufky/ansible_wireguard/tree/main/defaults/main)

### Ports
All ports and protocols have been defined for the role.

[defaults/ports.yml](https://github.com/r-pufky/ansible_wireguard/blob/main/defaults/main/ports.yml)

## Dependencies
Part of the [r_pufky.srv](https://github.com/r-pufky/ansible_collection_srv)
collection.

## Example Playbook
Read defaults documentation. Store interface keys in `group_vars` so both peers
can be configured.

Create a Wireguard VPN with IP forwarding enabled.
``` yaml
wireguard_service_ipv4_forwarding_enable: true
wireguard_service_ipv6_forwarding_enable: true
wireguard_adapters:
  - name: 'vpn'
    state: 'enabled'
    private_key: '{{ vault_wireguard_vpn_private_key }}'
    listen_port: 51820
    address:
      - '172.31.255.254/24'
    save_config: false
    peers:
      - public_key: '{{ vault_wireguard_client_public_key }}'
        preshared_key: '{{ vault_wireguard_clientvpn_preshared_key }}'
        allowed_ips:
          - '172.31.255.10/32'
        persistent_keepalive: 25
```

Apply the base role
``` yaml
- name: 'Apply base configuration'
  ansible.builtin.include_role:
    name: 'r_pufky.srv.wireguard'
```

### Wireguard-initramfs is supported as well.
Download and install the latest wireguard-initramfs release for use with
`r_pufky.srv.dropbear` to remotely unlocked encrypted disks.

Install wireguard-initramfs and configure boot adapter.
``` yaml
wireguard_initramfs_enable: true
wireguard_initramfs_version: 'latest'
wireguard_initramfs_adapter: 'boot'
wireguard_adapters:
  - name: 'boot'
    state: 'present'
    private_key: '{{ vault_wireguard_vpn_private_key }}'
    listen_port: 51820
    address:
      - '172.31.255.254/24'
    save_config: false
    peers:
      - public_key: '{{ vault_wireguard_client_public_key }}'
        preshared_key: '{{ vault_wireguard_clientvpn_preshared_key }}'
        allowed_ips:
          - '172.31.255.10/32'
        persistent_keepalive: 25
```

Apply the base role
``` yaml
- name: 'Apply base configuration'
  ansible.builtin.include_role:
    name: 'r_pufky.srv.wireguard'
```

## Development
Configure [environment](https://github.com/r-pufky/ansible_collection_srv/blob/main/docs/dev/environment/README.md)

Run all unit tests:
``` bash
molecule test --all
```

## Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_wireguard/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
