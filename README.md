# Wireguard
Wireguard installation from public release tarball.

## [Requirements][i]
Requires [r_pufky.deb][g] galaxy-ng collection.

See [reference documentation][h] for specific wireguard network configurations.
Management of routing table and firewall services should be applied before this
role (unless changes can be applied in standard wireguard adapter
configurations).

## Role Variables
Detailed variable use documented in defaults. See usage for role operation.

* [defaults][j] - User configurable options.

* [ports][k] - Ports are **not** managed (defined for external use).

## Usage

### Feature Flags
Tasks are gated by feature flags and executed in the following order.

  Step | Flag                    | Notes
 ------|-------------------------|-------
  1    | wireguard_flg_container | Deploy container specific wireguard settings.
  2    | wireguard_flg_initramfs | Install wireguard-initramfs.

### Example Playbooks
Store interface keys in **group_vars** so both peers can be configured.

#### Wireguard
``` yaml
- name: 'Create a Wireguard VPN with IP forwarding enabled.'
  ansible.builtin.include_role:
    name: 'r_pufky.deb.wireguard'
  vars:
    wireguard_srv_ipv4_forwarding: true
    wireguard_srv_ipv6_forwarding: true
    wireguard_cfg_adapters:
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

#### Wireguard-initramfs
Use wireguard-initramfs with **r_pufky.deb.dropbear** to remotely unlocked
encrypted disks.

``` yaml
- name: 'Install wireguard-initramfs and configure boot adapter.'
  ansible.builtin.include_role:
    name: 'r_pufky.deb.wireguard'
  vars:
    wireguard_flg_initramfs: true
    wireguard_cfg_initramfs_adapter: 'boot'
    wireguard_cfg_adapters:
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

## Development
Configure [environment][a].

``` bash
# Run all tests.
molecule test --all
```

### [Releases][b]

  Release | Debian | Ansible | Notes
 ---------|--------|---------|-------
  4.x.x   | 13     | 2.20    | Ansible 2.20, feature flags, and semantic versioning.
  3.x.x   | 13     | 2.18    | Migrate to Debian Trixie.
  2.x.x   | 12     | 2.18    | Use standardized libraries.
  1.x.x   | 12     | 2.18    | Migration from private repository.

## Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License][c] | [direct link][f]

## Author Information
PGP: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9][d] | [github gist][e]


[a]: https://r-pufky.github.io/ansible_docs
[b]: https://semver.org/spec/v2.0.0
[c]: https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0
[d]: https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9
[e]: https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22

[f]: https://github.com/r-pufky/ansible_wireguard/blob/main/LICENSE
[g]: https://github.com/r-pufky/ansible_collection_deb
[h]: https://r-pufky.github.io/docs/service/wireguard
[i]: https://github.com/r-pufky/ansible_wireguard/blob/main/meta/main.yml
[j]: https://github.com/r-pufky/ansible_wireguard/tree/main/defaults/main/main.yml
[k]: https://github.com/r-pufky/ansible_wireguard/blob/main/defaults/main/ports.yml