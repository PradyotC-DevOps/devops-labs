# 3 - Configure Default SSH User for Ansible

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team aims to manage all servers within the stack using Ansible, utilizing a common sudo user across all servers. They plan to use this user for various tasks on each server. While this isn't finalized, they're starting with testing. Ansible is already installed on the <code>jump host</code> via yum. Here's the requirement:</p>

</div><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>On the <code>jump host</code>, modify the default configuration of Ansible to enable the use of <code>anita</code> as the default SSH user for all hosts. Ensure to make changes within Ansible's default configuration without creating a new one.</p>
</div>

---

## 🚀 Complete Solution

1. **Locate the Default Configuration:**
* Investigated the system-level Ansible directory `/etc/ansible/` on the jump host.
* Confirmed the presence of the default global configuration file (`/etc/ansible/ansible.cfg`).


2. **Modify the Global Ansible Config:**
* Escalated privileges using `sudo` to append the necessary settings to the global configuration file.
* Added the `[defaults]` section header.
* Configured the `remote_user` variable to strictly default to `anita`.
```bash
sudo sh -c 'echo "[defaults]" >> /etc/ansible/ansible.cfg'
sudo sh -c 'echo "remote_user = anita" >> /etc/ansible/ansible.cfg'

```




3. **Validation:**
* Verified the contents of the configuration file to ensure the block was appended correctly.
* Executed `ansible-config dump --only-changed` to confirm Ansible successfully registered `anita` as the new default remote user at the global level.



**💡 Key Learnings & Gotchas:**

* **Ansible Configuration Precedence:** Ansible reads configuration files in a very specific order of precedence:
1. `$ANSIBLE_CONFIG` (Environment Variable)
2. `./ansible.cfg` (Current working directory)
3. `~/.ansible.cfg` (User's home directory)
4. `/etc/ansible/ansible.cfg` (Global default).
By modifying the `/etc/ansible/ansible.cfg` file, we ensured that this behavior applies system-wide to any user on the jump host who hasn't defined a local override.


* **`remote_user` Directive:** Setting `remote_user` in the `[defaults]` section is the cleanest way to enforce a standard service account for infrastructure automation without explicitly declaring it in every single inventory file or playbook.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the discovery of the default configuration, the administrative modification of the file, and the verification using Ansible's built-in config dumper tool.

```bash
thor@jump-host ~$ ls -la /etc/ansible/
total 24
drwxr-xr-x 3 root root 4096 Jun 10 09:19 .
drwxr-xr-x 1 root root 4096 Jun 10 09:19 ..
-rw-r--r-- 1 root root  614 Feb 10 16:38 ansible.cfg
-rw-r--r-- 1 root root 1175 Feb 10 16:38 hosts
drwxr-xr-x 2 root root 4096 Feb 10 16:38 roles

thor@jump-host ~$ sudo sh -c 'echo "[defaults]" >> /etc/ansible/ansible.cfg'
thor@jump-host ~$ sudo sh -c 'echo "remote_user = anita" >> /etc/ansible/ansible.cfg'

thor@jump-host ~$ cat /etc/ansible/ansible.cfg
# Since Ansible 2.12 (core):
# To generate an example config file (a "disabled" one with all default settings, commented out):
#               $ ansible-config init --disabled > ansible.cfg
#
# Also you can now have a more complete file by including existing plugins:
# ansible-config init --disabled -t all > ansible.cfg

# For previous versions of Ansible you can check for examples in the 'stable' branches of each version
# Note that this file was always incomplete  and lagging changes to configuration settings

# for example, for 2.9: https://github.com/ansible/ansible/blob/stable-2.9/examples/ansible.cfg
[defaults]
remote_user = anita

thor@jump-host ~$ ansible-config dump --only-changed
CONFIG_FILE() = /etc/ansible/ansible.cfg
DEFAULT_REMOTE_USER(/etc/ansible/ansible.cfg) = anita

```