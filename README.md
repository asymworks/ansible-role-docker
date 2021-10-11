# Ansible Role: Docker Server

An Ansible Role that installs the Docker Container Server and optionally configures [Logspout](https://github.com/gliderlabs/logspout) and [Diun](https://crazymax.dev/diun/).

## Requirements

None

## Role Variables (General)

Available variables for the role are listed below, along with default values if applicable (see `defaults/main.yml`):

```yaml
docker_ipv6: false
docker_ipv6_nat: true
docker_ipv6_subnet: fd00::/80
docker_ipv6_network: default
```

Configure Docker for IPv6 operation when the `docker_ipv6` variable is truthy. This sets the `ipv6` and `fixed-cidr-v6` properties on the Docker daemon, and either installs the Docker IPv6 NAT container when `docker_ipv6_network` is `default`, or creates a new IPv6 bridge network for containers. Note that in the second mode, additional steps will be required to route network traffic to the containers, such as installing `ndppd`, which is not done automatically.

```yaml
docker_telegraf: true
```

Install the Docker input plugin for Telegraf.

```yaml
telegraf_docker_gather_services: false
telegraf_docker_containers: []
telegraf_docker_containers_include: []
telegraf_docker_containers_exclude: []
telegraf_docker_timeout: 5s
telegraf_docker_stats_perdev: true
telegraf_docker_stats_total: false
```

Configuration options for the Telegraf Docker input plugin.

```yaml
logspout_enabled: true
logspout_dest_proto: multiline+udp
logspout_dest_host: logs.lan.kraussnet.com
logspout_dest_port: 5000
```

Enables or disables the Logspout container and configures the destination URI `{{ logspout_dest_proto }}://{{ logspout_dest_host }}:{{ logspout_dest_port }}`.

```yaml
logspout_backlog: false
logspout_format: >-
  {
    "hostname": "{{ ansible_hostname }}",
    "container": "{{ '{{' }} .Container.Name {{ '}}' }}",
    "labels": {{ '{{' }} toJSON .Container.Config.Labels {{ '}}' }},
    "timestamp": "{{ '{{' }} .Time.Format "2006-01-02T15:04:05.000Z07:00" {{ '}}' }}",
    "source": "{{ '{{' }} .Source {{ '}}' }}",
    "message": {{ '{{' }} toJSON .Data {{ '}}' }}
  }
```

Configures the Logspout logging behavior and format.

```yaml
diun_enabled: true
diun_watch_schedule: 0 */6 * * *
diun_watch_stopped: true
diun_watch_workers: 20
```

Enables or disables the Diun container and configures the Diun Docker watcher.

```yaml
diun_notif_mail_host: localhost
diun_notif_mail_port: 25
diun_notif_mail_localname: "{{ ansible_hostname }}"
diun_notif_mail_from: "diun@{{ ansible_fqdn }}"
diun_notif_mail_to: "diun@localhost"
```

Configures the Diun Mail notifier.

## Role Facts

None

## Role Handlers

None

## Dependencies

None

## Example Playbook

```yaml
- hosts: all
  roles:
    asymworks.baseline
    asymworks.docker
```

## License

MIT / BSD

## Author

This role was created in 2019 by Jonathan Krauss for managing home network infrastructure.
