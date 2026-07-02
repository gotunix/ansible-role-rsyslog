# Ansible Role: Rsyslog

This Ansible role installs and configures `rsyslog` for centralized logging. It supports configuring a node either as a **Centralized Server** (which receives logs) or as a **Forwarding Client** (which sends logs to the central server).

## Features

- **Server Mode**: Configures a central node to listen for incoming log messages natively and stores them in `/var/log/server/`.
- **Client Mode**: Configures nodes to reliably forward their local logs to the centralized server.
- Supported protocols: `UDP`, `TCP`, or `both` (`both` is only valid in server mode).
- Automatically removes conflicting configurations to maintain state.

## Configuration Variables

You can customize the role behavior via `host_vars` or `group_vars`. Default variables inside `defaults/main.yaml`:

| Variable | Default Value | Description |
|----------|---------------|-------------|
| `rsyslog_mode` | `"client"` | Determines the node's logging role. Set to `"server"` for the central receiver, or `"client"` for a forwarder. |
| `rsyslog_server_ip` | `"127.0.0.1"` | The IP address of your central rsyslog server. Required when `rsyslog_mode` is `"client"`. |
| `rsyslog_port` | `514` | The port used to send or receive logs. |
| `rsyslog_protocol` | `"udp"` | Protocol to use (`"udp"`, `"tcp"`, or `"both"`). |

## Usage Models

### 1. Central Rsyslog Server

Deploy this configuration to your central aggregation node:

```yaml
# host_vars/logserver.yaml
rsyslog_mode: "server"
rsyslog_protocol: "udp"
```

### 2. Forwarding Rsyslog Client

Deploy this to all other nodes so they forward their logs to the server:

```yaml
# host_vars/node1.yaml
rsyslog_mode: "client"
rsyslog_server_ip: "10.0.0.5" # The IP of the server
rsyslog_protocol: "udp"
```

## Running

Include it in your playbook (`site.yaml`):

```yaml
---
- name: Deploy Rsyslog
  hosts: all
  become: true
  roles:
    - rsyslog
```

Run it using:
```bash
ansible-playbook site.yaml -i inventory.ini
```
