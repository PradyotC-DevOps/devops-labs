# 5 - Create Files on App Servers using Ansible.md

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team is testing various Ansible modules on servers in <code>Stratos DC</code>. They're currently focusing on file creation on remote hosts using Ansible. Here are the details:</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>a. Create an inventory file <code>~/playbook/inventory</code> on <code>jump host</code> and include <code>all app servers</code>.</p>
<p>b. Create a playbook <code>~/playbook/playbook.yml</code> to create a blank file <code>/usr/src/webapp.txt</code> on <code>all app servers</code>.</p>
<p>c. Set the permissions of the <code>/usr/src/webapp.txt</code> file to <code>0655</code>.</p>
<p>d. Ensure the user/group owner of the <code>/usr/src/webapp.txt</code> file is <code>tony</code> on <code>app server 1</code>, <code>steve</code> on <code>app server 2</code> and <code>banner</code> on <code>app server 3</code>.</p>
<p><code>Note:</code> Validation will execute the playbook using the command <code>ansible-playbook -i inventory playbook.yml</code>, so ensure the playbook functions correctly without any additional arguments.</p>

</div>

---

## 🚀 Complete Solution

1. **Configure the Inventory File:**
* Created the required directory `~/playbook` and navigated into it.
* Created a static INI inventory file named `inventory` defining the `[app]` group.
* Added the three application servers (`stapp01`, `stapp02`, `stapp03`) with their respective SSH connection variables (`ansible_user` and `ansible_ssh_pass`) so the playbook could run without extra CLI arguments.


2. **Develop the Playbook:**
* Authored `playbook.yml` to target the `app` group with privilege escalation enabled (`become: yes`).
* Utilized the `ansible.builtin.file` module with `state: touch` to create the blank file.
* Defined the permissions using the octal string `'0655'`.
* **Dynamic Ownership:** Leveraged Jinja2 templating (`{{ ansible_user }}`) for the `owner` and `group` fields. Since `stapp01` uses `tony`, `stapp02` uses `steve`, and `stapp03` uses `banner`, mapping the ownership dynamically to the connection user perfectly satisfied the requirement without writing three separate tasks.


3. **Execute and Validate:**
* Ran the playbook using `ansible-playbook -i inventory playbook.yml`.
* Verified the file creation, permissions, and ownership using an ad-hoc Ansible command across all nodes.



**💡 Key Learnings & Gotchas:**

* **`file` vs `copy` module:** While the `copy` module is great for transferring existing files, the `file` module (specifically with `state: touch`) is the standard, idempotent way to provision blank files or modify permissions/ownership of existing files in Ansible.
* **Octal Permissions in YAML:** When defining permissions like `0655` in an Ansible playbook, it is best practice to wrap the value in quotes (e.g., `'0655'`). If left unquoted, the YAML parser might interpret the leading zero as an integer base conversion, resulting in incorrect permissions being applied to the file.
* **Jinja2 Variables:** Instead of hardcoding conditionals (e.g., `when: inventory_hostname == 'stapp01'`), utilizing the `{{ ansible_user }}` variable allowed a single concise task to dynamically adapt to the target host.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the setup of the inventory, the use of dynamic variables in the playbook, the successful execution, and the final ad-hoc validation proving the distinct ownership requirements were met on each node.

```bash
thor@jump-host ~$ mkdir -p ~/playbook && cd ~/playbook
thor@jump-host ~/playbook$ touch inventory
thor@jump-host ~/playbook$ nano inventory
thor@jump-host ~/playbook$ cat inventory
[app]
stapp01 ansible_host=stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n
thor@jump-host ~/playbook$ nano playbook.yml
thor@jump-host ~/playbook$ cat playbook.yml
- name: Create webapp.txt with specific permissions and ownership
  hosts: app
  become: yes
  tasks:
    - name: Create blank file /usr/src/webapp.txt
      ansible.builtin.file:
        path: /usr/src/webapp.txt
        state: touch
        mode: '0655'
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
thor@jump-host ~/playbook$ ansible-playbook -i inventory playbook.yml

PLAY [Create webapp.txt with specific permissions and ownership] ****************

TASK [Gathering Facts] **********************************************************
ok: [stapp03]
ok: [stapp01]
ok: [stapp02]

TASK [Create blank file /usr/src/webapp.txt] ************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

PLAY RECAP **********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

thor@jump-host ~/playbook$ ansible app -i inventory -m command -a "ls -l /usr/src/webapp.txt" -b
stapp02 | CHANGED | rc=0 >>
-rw-r-xr-x 1 steve steve 0 Aug  3 11:34 /usr/src/webapp.txt
stapp03 | CHANGED | rc=0 >>
-rw-r-xr-x 1 banner banner 0 Aug  3 11:34 /usr/src/webapp.txt
stapp01 | CHANGED | rc=0 >>
-rw-r-xr-x 1 tony tony 0 Aug  3 11:34 /usr/src/webapp.txt
thor@jump-host ~/playbook$ 
```