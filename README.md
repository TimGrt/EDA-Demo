# Ansible Event-Driven Automation Demo

Supported systems currently are:

* ArchLinux
* Debian 11
* Fedora 36 (I tested with Fedora 37)
* Ubuntu 22.04

If you don't have a supported system in place, testing may be done in my Fedora 37 Docker Image (originally intended for Molecule testing).

```bash
docker pull timgrt/fedora37-ansible
```

Run a container from the image:

```bash
docker run --detach --privileged volume=/sys/fs/cgroup:/sys/fs/cgroup:rw --cgroupns=host --name fedora37 timgrt/fedora37-ansible
```

Login to container:

```bash
docker exec -it fedora37 /bin/bash
```

Clone the repository inside the container, install *git* beforehand:
```bash
dnf install git && git clone https://github.com/TimGrt/EDA-Demo.git
```

## Requirements

Install python dependencies on the host running EDA:

```bash
pip3 install -r requirements.txt
```

## Installation

Install EDA Galaxy collection:

```bash
ansible-galaxy collection install ansible.eda
```

Run playbook from collection, this installs

```bash
ansible-playbook -i localhost, -c local ansible.eda.install_rulebook_cli
```

**Note:** Some tasks in this role require privilege escalation and therefore you may need to provide the necessary credentials.

## Inventory

Adjust the provided inventory file, currently it uses localhost as the target.

> The inventory file must be in YAML format, .ini format is not yet supported.

## Execution

The provided rulebook scans the directory `/tmp/important-directory`, if any file-system changes ocur in it (e.g. a file within this folder gets deleted), the playbook `redeploy.yml` is triggered.

To prepare everything (the folder must exist and be filled), run the playbook (`ansible-playbook -i inventory.yml redeploy.yml`).


Now, lets start the EDA process, run the rulebook definition:

```bash
ansible-rulebook --rulebook rulebook.yml -i inventory.yml --verbose
```

The process is running in foreground and is watching the sources and matches the rules (of the *rulebook.yml* against them).

Open a second bash session and remove the important file:

```bash
rm -f /tmp/important-directory/my-super-important-file.cfg
```
