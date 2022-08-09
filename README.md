# Ansible Role: iptables_legacy

[![CI](https://github.com/poppen/ansible-role-iptables-legacy/actions/workflows/ci.yml/badge.svg)](https://github.com/poppen/ansible-role-iptables-legacy/actions/workflows/ci.yml)

An ansible Role that installs and enables iptables-legacy on Linux.

I created this role because Docker requires iptables-legacy enabled.

## Requirements

- Ubuntu 22.04 or above
- Debian 10 or above

## Role Variable

Available variable is `iptables_legacy_rules`. It is a dictionary and has two keys, `v4` and `v6`.
The rules in `iptables_legacy_rules.v4` are put as `/etc/iptables/rules.v4` file, and the rules in `iptables_legacy_rules.v6` are put as `/etc/iptables/rules.v6` file for loading by iptables and ip6tables that are symbolic links to iptables-legacy and ip6tables-legacy.

Its default value is below (see `defaults/main`):

```yml
iptables_legacy_rules:
  v4: |
    *filter
    :INPUT DROP [0:0]
    :FORWARD DROP [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p icmp --icmp-type echo-reply -j ACCEPT
    -A INPUT -p icmp --icmp-type destination-unreachable -j ACCEPT
    -A INPUT -p icmp --icmp-type time-exceeded -j ACCEPT
    -A INPUT -p icmp --icmp-type parameter-problem -j ACCEPT
    -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/s --limit-burst 4 -j ACCEPT
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
    -A INPUT -p tcp --tcp-flags ALL NONE -j DROP
    -A INPUT -p tcp ! --syn -m state --state NEW -j DROP
    -A INPUT -p tcp --tcp-flags ALL ALL -j DROP
    -A INPUT -p tcp -j REJECT --reject-with tcp-reset
    -A INPUT -p udp -j REJECT --reject-with icmp-port-unreachable
    -A INPUT -j REJECT --reject-with icmp-proto-unreachable
    COMMIT
  v6: |
    *filter
    :INPUT DROP [0:0]
    :FORWARD DROP [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p ipv6-icmp -j ACCEPT
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
    -A INPUT -p tcp --tcp-flags ALL NONE -j DROP
    -A INPUT -p tcp ! --syn -m state --state NEW -j DROP
    -A INPUT -p tcp --tcp-flags ALL ALL -j DROP
    -A INPUT -p tcp -j REJECT --reject-with tcp-reset
    -A INPUT -p udp -j REJECT --reject-with icmp6-port-unreachable
    COMMIT
```

## Dependencies

- community.general

## Example Playbook

```yml
- hosts: all

  roles:
    - poppen.iptables_legacy
```

## License

BSD-3-Clause

## Future Plan

- RedHat family support (PRs are always welcome).

## Author Information

Shinsuke MATSUI
