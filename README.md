# Ansible Role: AdguardHome

[![ubuntu-18](https://img.shields.io/badge/ubuntu-18.x-orange?style=flat&logo=ubuntu)](https://ubuntu.com/)
[![ubuntu-20](https://img.shields.io/badge/ubuntu-20.x-orange?style=flat&logo=ubuntu)](https://ubuntu.com/)

[![License](https://img.shields.io/badge/license-MIT%20License-brightgreen.svg?style=flat)](https://opensource.org/licenses/MIT)
[![GitHub issues](https://img.shields.io/github/issues/OnkelDom/ansible-role-adguard-home?style=flat)](https://github.com/OnkelDom/ansible-role-adguard-home/issues)
[![GitHub tag](https://img.shields.io/github/tag/OnkelDom/ansible-role-adguard-home.svg?style=flat)](https://github.com/OnkelDom/ansible-role-adguard-home/tags)
[![GitHub action](https://github.com/OnkelDom/ansible-role-adguard-home/workflows/ansible-lint/badge.svg)](https://github.com/OnkelDom/ansible-role-adguard-home)

## Description

Deploy and manage [AdGuardHome](https://github.com/AdguardTeam/AdGuardHome) service using ansible.

For full confguration Informations visit [AdGuardHome Config](https://github.com/AdguardTeam/AdGuardHome/wiki/Configuration#configuration-file).

### Reset Web Password
AdGuard Home stores password as a BCrypt-encoded hash.

The role default password ist "admin".

Here's what you need to do to change the password:

* Stop AdGuard Home
* Edit AdGuardHome.yaml
* Find password field there
* Replace it with the new value. You can use htpasswd tool or any online BCrypt generation tool (there are many available online).
* Start AdGuard Home
* Now you'll be able to log in to Web interface using your new password.

Example using htpasswd:

```bash
htpasswd -bnBC 10 "" MY_NEW_PASS | tr -d ':'
```

## Configuration in WebUI

:bangbang: | When you finished you Configuration in WebUI, save you config in hosts vars.
:---: | :---

## Requirements

- Ansible >= 2.9 (It might work on previous versions, but we cannot guarantee it)

## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `proxy_env` | {} | Proxy environment variables for Client|
| `adguard_version` | 0.107.0 | AdGuardHome package version. Also accepts `latest` as parameter. |
| `adguard_web_listen_address` | 0.0.0.0 | Address on which adguard-home will be listening |
| `adguard_web_listen_port` | 3200 | Port on which adguard-home will be listening |
| `adguard_config_dir` | /etc/adguard | Path to directory with adguard-home configuration |
| `adguard_db_dir` | /var/lib/adguard | Path to directory with adguard-home database |
| `adguard_binary_install_dir` | /usr/local/bin | Binary install dir |
| `adguard_system_user` | "{{ adguard_user \| default('adguard') }}" | Service User |
| `adguard_system_group` | "{{ adguard_group \| default('adguard') }}" | Serviceuser Group |
| `adguard_limit_nofile` | 8192 | Limit NoFile |
| `#adguard_http_proxy` | http://proxy:3128 | Set Proxy in System unit as Environment |
| `#adguard_https_proxy` | http://proxy:3128 | Set Proxy in System unit as Environment |
| `adguard_config` | {} | AdGuardHome configuration | 

## Example
```yaml
---
adguard_config:
  bind_host: "{{ adguard_web_listen_address }}"
  bind_port: "{{ adguard_web_listen_port }}"
  beta_bind_port: 0
  users:
  - name: admin
    # password is: admin
    # you can generate one on https://bcrypt-generator.com/
    # or with "htpasswd -bnBC 10 "" MY_NEW_PASS | tr -d ':'"
    password: $2y$12$SMsrAS0r0FPvpBPDPJygD.GO7V7jeUd2GI2KN.l.rubeV6aKVauEy
  auth_attempts: 5
  block_auth_min: 15
  http_proxy: ""
  language: ""
  debug_pprof: false
  web_session_ttl: 720
  dns:
    bind_hosts:
    - "{{ adguard_web_listen_address }}"
    port: 53
    statistics_interval: 1
    querylog_enabled: true
    querylog_file_enabled: true
    querylog_interval: 2160h
    querylog_size_memory: 1000
    anonymize_client_ip: false
    protection_enabled: true
    blocking_mode: default
    blocking_ipv4: ""
    blocking_ipv6: ""
    blocked_response_ttl: 10
    parental_block_host: family-block.dns.adguard.com
    safebrowsing_block_host: standard-block.dns.adguard.com
    ratelimit: 20
    ratelimit_whitelist: []
    refuse_any: true
    upstream_dns:
    - https://dns10.quad9.net/dns-query
    upstream_dns_file: ""
    bootstrap_dns:
    - 9.9.9.10
    - 149.112.112.10
    - 2620:fe::10
    - 2620:fe::fe:10
    all_servers: false
    fastest_addr: false
    allowed_clients: []
    disallowed_clients: []
    blocked_hosts:
    - version.bind
    - id.server
    - hostname.bind
    cache_size: 4194304
    cache_ttl_min: 0
    cache_ttl_max: 0
    bogus_nxdomain: []
    aaaa_disabled: false
    enable_dnssec: false
    edns_client_subnet: false
    max_goroutines: 300
    ipset: []
    filtering_enabled: true
    filters_update_interval: 24
    parental_enabled: false
    safesearch_enabled: false
    safebrowsing_enabled: false
    safebrowsing_cache_size: 1048576
    safesearch_cache_size: 1048576
    parental_cache_size: 1048576
    cache_time: 30
    rewrites: []
    blocked_services: []
    upstream_timeout: 10s
    local_domain_name: lan
    resolve_clients: true
    use_private_ptr_resolvers: true
    local_ptr_upstreams: []
  tls:
    enabled: false
    server_name: ""
    force_https: false
    port_https: 443
    port_dns_over_tls: 853
    port_dns_over_quic: 784
    port_dnscrypt: 0
    dnscrypt_config_file: ""
    allow_unencrypted_doh: false
    strict_sni_check: false
    certificate_chain: ""
    private_key: ""
    certificate_path: ""
    private_key_path: ""
  filters:
  - enabled: true
    url: https://adguardteam.github.io/AdGuardSDNSFilter/Filters/filter.txt
    name: AdGuard DNS filter
    id: 1
  - enabled: true
    url: https://adaway.org/hosts.txt
    name: AdAway Default Blocklist
    id: 2
  whitelist_filters: []
  user_rules: []
  dhcp:
    enabled: false
    interface_name: ""
    dhcpv4:
      gateway_ip: ""
      subnet_mask: ""
      range_start: ""
      range_end: ""
      lease_duration: 86400
      icmp_timeout_msec: 1000
      options: []
    dhcpv6:
      range_start: ""
      lease_duration: 86400
      ra_slaac_only: false
      ra_allow_slaac: false
  clients: []
  log_compress: false
  log_localtime: false
  log_max_backups: 0
  log_max_size: 100
  log_max_age: 3
  log_file: ""
  verbose: false
  os:
    group: "{{ adguard_system_user }}"
    user: "{{ adguard_system_user }}"
    rlimit_nofile: "{{ adguard_limit_nofile }}"
  schema_version: 12
```

### Playbook

```yaml
- hosts: all
  roles:
    - onkeldom.adguard_home
```

## Contributing

See [contributor guideline](CONTRIBUTING.md).

## License

This project is licensed under MIT License. See [LICENSE](/LICENSE) for more details.
