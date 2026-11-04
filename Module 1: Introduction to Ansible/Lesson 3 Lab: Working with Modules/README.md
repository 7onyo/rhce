# Lesson 3 Lab: Working with Modules

## Task: Install an Ansible Collection and Use Ad Hoc Commands

**Goal:** Install the `community.crypto` collection so it is available to both `ansible` and `ansible-navigator`, verify managed host connectivity using the `ping` module, and query installed SSH-related RPM packages on all managed nodes using an ad hoc shell command.

---

## Solution

### Step 1: Verify the `ansible.cfg` configuration

Open the Ansible configuration file in your home directory and confirm it contains the correct settings for inventory, remote user, collections path, and privilege escalation:

```bash
nano ~/ansible.cfg
```

**Configuration file: `ansible.cfg`**

```ini
[defaults]
inventory = inventory
remote_user = ansible
collections_path = /home/onyo/.ansible/collections:/usr/share/ansible/collections
ask_pass = false

[privilege_escalation]
become = true
become_method = sudo
become_user = root
become_ask_pass = false
```

> **Note:** The `collections_path` directive lists the directories Ansible searches for installed collections. Setting it here ensures both `ansible` and `ansible-navigator` use the same lookup paths.

<p align="center">
  <img src="media/ansibleConfig.png" alt="Screenshot showing ansible.cfg open in nano with defaults and privilege escalation sections" width="800"/>
</p>

---

### Step 2: Install the `community.crypto` collection

```bash
ansible-galaxy collection install community.crypto -p collections
```

<p align="center">
  <img src="media/installCrypto.png" alt="Screenshot showing community.crypto collection installed successfully" width="800"/>
</p>

---

### Step 3: Ping all managed hosts

Use the `ping` module in an ad hoc command to verify that all hosts in the inventory are reachable and properly configured:

```bash
ansible all -m ping
```

Both `ansible1` and `ansible2` should return `"ping": "pong"` with a `SUCCESS` status, confirming SSH connectivity and Python availability on each node.

<p align="center">
  <img src="media/pingAllHosts.png" alt="Screenshot showing ansible all -m ping returning SUCCESS and pong for ansible1 and ansible2" width="800"/>
</p>

---

### Step 4: Query SSH-related packages on all managed hosts

Use the `shell` module to pipe the output of `rpm -qa` through `grep ssh` on every managed node:

```bash
ansible all -m shell -a 'rpm -qa | grep ssh'
```

> **Note:** The `shell` module (not `command`) is required here because the command uses a pipe (`|`). The `command` module does not process shell operators.

<p align="center">
  <img src="media/grepSsh.png" alt="Screenshot showing SSH-related RPM packages listed on ansible1 and ansible2" width="800"/>
</p>
