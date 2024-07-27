## Docker Installation Ansible Playbook

### Notice
This Ansible playbook simplifies the Docker installation process on Linux systems.

- Before running, you must download the Docker files and place them in the ```/etc/ansible/roles/install-docker/files/ubuntu-noble-x68_64/``` path so that it can be installed offline
- To learn how to get the Docker installation package, refer to the following link
https://github.com/mojtabco/docker-package-download

### Overview
The playbook performs the following tasks:

- Updates the apt package cache.
- Copies Docker-related files.
- Installs Docker packages.
- Deletes the temporary Docker files directory post-installation.

### Roles 
```yml
# roles/install-docker/tasks/main.yml
---
- name: Update apt cache
  apt:
    update_cache: yes

- name: Copy Docker files
  copy:
    src: /etc/ansible/roles/install-docker/files/ubuntu-noble-x68_64/
    dest: /tmp/docker
    mode: 0644

- name: Install Docker packages
  shell:
    cmd: dpkg -i *.deb
    chdir: /tmp/docker

- name: Delete Docker files directory
  file:
    path: /tmp/docker
    state: absent
```

### Playbooks
- This line specifies that the playbook should be executed on all hosts defined under the [db] group in your inventory file. The hosts directive tells Ansible which machines to target with the playbook.
- This line enables privilege escalation (also known as "becoming" another user). When set to yes, Ansible will attempt to gain elevated privileges (typically root) on the target host(s) to execute commands that require higher permissions.
- This line explicitly sets the user account to switch to during privilege escalation. In this case, it switches to the root user. This is useful when you want to ensure that certain tasks are always executed as the root user, regardless of the initial user Ansible connects as.
- Under the vars section, variables specific to this playbook execution are declared. Here, ansible_become_pass is set to root. This variable is used to specify the password required for privilege escalation. However, it's worth noting that storing passwords directly in playbooks or inventory files is generally discouraged due to security concerns. Instead, consider using Ansible Vault or environment variables for sensitive data like passwords.
- Finally, this line specifies the roles to be included in the playbook. Roles are reusable collections of tasks, templates, modules, and variables. In this case, the install-docker role is included. This means that the tasks defined within the install-docker role will be executed on the

In summary, this Ansible configuration targets hosts in the [db] group, escalates privileges to the root user, uses root as the password for privilege escalation (though not recommended), and includes the install-docker role to perform the Docker installation tasks.

```yml
# playbooks/install-docker.yml
---
- hosts: db
  become: yes
  become_user: root
  vars:
    ansible_become_pass: root
  roles:
    - role: install-docker
```
### Ansible configuration file
```
[defaults]
inventory      = ./inventory/main.yml
remote_tmp     = /tmp
forks          = 150
sudo_user      = ubuntu
remote_user    = ubuntu
roles_path     = ./roles
host_key_checking = False
log_path = ./log/ansible.log

[privilege_escalation]
become=True
become_method=sudo
become_user=ubuntu
become_ask_pass=False

[ssh_connection]
scp_if_ssh = True

```
## Prerequisites
Before running this playbook, ensure the following:

- An Ubuntu system is targeted.
- Ansible is installed and configured on your machine.

## Usage

1. **Clone the Repository**: Clone this repository to your local machine.

2. **Run the Playbook**: Execute the playbook using the command below. Ensure you're in the directory containing the playbook file.
```bash
# ansible-playbook playbooks/install-docker.yml
```
For access to the source code and reporting issues, visit the GitHub Repository: https://github.com/mojtabco

