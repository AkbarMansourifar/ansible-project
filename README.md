# Ansible Server Automation

Automated configuration of Debian servers using Ansible roles.

## Overview

This project automates common package installation and configures Nginx and Docker on test and production servers.

## Requirements

- Ansible installed on the control node
- SSH access to target servers
- Appropriate privileges on target servers
- An SSH private key for production access

## Project Structure

    .
    ├── ansible.cfg
    ├── inventory/
    │   ├── hosts.yml
    │   ├── production.example.yml
    │   └── production.yml
    ├── playbooks/
    │   └── site.yml
    └── roles/
        ├── common/
        ├── nginx/
        └── docker/

`inventory/production.yml` stores local production connection details and must not be committed. Use `inventory/production.example.yml` as a template.

## Inventory

The default inventory contains the test server:

| Host | Address | User |
|---|---|---|
| node1 | 192.168.56.11 | vagrant |

## Roles

### common

- Backs up the original APT sources list
- Optionally configures an Iranian Debian mirror
- Updates the APT package cache
- Installs common utilities

### nginx

- Installs Nginx
- Enables and starts the service
- Deploys the Nginx site configuration
- Creates a test `index.html`

### docker

- Configures the official Docker APT repository
- Installs Docker Engine and related plugins
- Enables and starts the Docker service
- Verifies the Docker installation

## Usage

### Validate the playbook

    ansible-playbook playbooks/site.yml --syntax-check

### Test connectivity

    ansible test -m ping

### Run against the test server

    ansible-playbook playbooks/site.yml --limit test --check
    ansible-playbook playbooks/site.yml --limit test

### Run against production

The production inventory must be explicitly included because `ansible.cfg` defaults to `inventory/hosts.yml`.

    ansible -i inventory/hosts.yml -i inventory/production.yml production -m ping
    ansible-playbook -i inventory/hosts.yml -i inventory/production.yml playbooks/site.yml --limit production --check
    ansible-playbook -i inventory/hosts.yml -i inventory/production.yml playbooks/site.yml --limit production

Run only the Docker role on production:

    ansible-playbook -i inventory/hosts.yml -i inventory/production.yml playbooks/site.yml --limit production --tags docker

Review check-mode output before applying changes to production.

## Nginx Variables

| Variable | Default | Description |
|---|---|---|
| `nginx_port` | `80` | Listening port |
| `nginx_server_name` | `_` | Server name |
| `nginx_root` | `/var/www/html` | Document root |

## Security

- Do not commit production inventories containing real server details.
- Never commit SSH private keys, credentials, or vault passwords.
- Keep `inventory/production.yml` local and verify Git status before committing.

## License

MIT
