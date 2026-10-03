# Ansible Project

Automated server configuration for Debian-based systems using Ansible.

## Requirements

- Ansible 2.9 or higher
- SSH access to target servers
- User with sudo privileges

## Project Structure

.
├── ansible.cfg
├── inventory/
│   └── hosts.yml
├── playbooks/
│   └── site.yml
└── roles/
    ├── common/
    ├── nginx/
    └── docker/

## Usage

Test connectivity to all servers:

    ansible all -m ping

Run playbook in check mode:

    ansible-playbook playbooks/site.yml --check

Apply configuration:

    ansible-playbook playbooks/site.yml

## Inventory

| Name  | IP            | User    |
|-------|---------------|---------|
| node1 | 192.168.56.11 | vagrant |

## Roles

### common

- Backs up original sources.list
- Switches Debian mirrors to mirror.shatel.ir
- Installs base packages: curl, wget, git, vim, htop, ca-certificates, gnupg, python3-pip

### nginx

- Installs nginx
- Enables and starts the service
- Deploys configuration from template
- Creates a test index.html

### docker

- Installs Docker CE and related packages
- Enables and starts the Docker service

## Role Variables

Variables defined in roles/nginx/defaults/main.yml:

| Variable          | Default        | Description        |
|-------------------|----------------|--------------------|
| nginx_port        | 80             | Listening port     |
| nginx_server_name | _              | Server name        |
| nginx_root        | /var/www/html  | Document root      |

## Example Playbook

    - name: Configure test servers
      hosts: test
      become: true
      vars:
        nginx_port: 8080
      roles:
        - common
        - nginx
        - docker

## License

MIT
