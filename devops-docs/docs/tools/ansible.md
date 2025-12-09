# Ansible

## Overview

Ansible is a configuration management and infrastructure automation tool.

## Key Concepts

**Playbook**: YAML file with automation tasks

**Play**: Set of tasks to execute

**Task**: Individual action

**Handler**: Conditional action triggered by tasks

**Role**: Organized collection of files

## Playbook Structure

```yaml
---
- name: Configure web servers
  hosts: web_group
  become: yes
  vars:
    http_port: 80
    web_root: /var/www/html
  
  roles:
    - common
    - webserver
  
  tasks:
    - name: Install packages
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - nginx
        - curl
        - git
    
    - name: Start nginx service
      service:
        name: nginx
        state: started
        enabled: yes
      notify: reload nginx
  
  handlers:
    - name: reload nginx
      service:
        name: nginx
        state: reloaded
```

## Common Modules

- **apt/yum** - Package management
- **service** - Service management
- **file** - File operations
- **template** - Template processing
- **command/shell** - Execute commands
- **copy** - Copy files
- **git** - Git operations
- **docker** - Docker management
- **aws** - AWS operations

## Role Structure

```
webserver/
├── tasks/
│   └── main.yml
├── handlers/
│   └── main.yml
├── templates/
│   └── nginx.conf.j2
├── files/
│   └── default.conf
├── vars/
│   └── main.yml
└── defaults/
    └── main.yml
```

## Inventory

```ini
[webservers]
web1.example.com
web2.example.com

[databases]
db1.example.com

[all:vars]
ansible_user=ubuntu
ansible_private_key_file=~/.ssh/key.pem
```

## Common Commands

```bash
# Run playbook
ansible-playbook playbook.yml

# Run specific host
ansible-playbook -i inventory.ini playbook.yml -l webservers

# Dry run
ansible-playbook playbook.yml --check

# Verbose output
ansible-playbook -v playbook.yml

# Run ad-hoc command
ansible webservers -m apt -a "name=nginx state=present"

# Check syntax
ansible-playbook playbook.yml --syntax-check
```

## Best Practices

1. **Idempotency** - Safe to run multiple times
2. **Modularity** - Use roles
3. **Version control** - Track all playbooks
4. **Testing** - Validate playbooks
5. **Documentation** - Document roles and tasks
6. **Vault** - Encrypt sensitive data

