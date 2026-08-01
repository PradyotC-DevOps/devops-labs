# 4 - Copy Data to App Servers using Ansible

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team needs to copy data from the <code>jump host</code> to all <code>application servers</code> in <code>Stratos DC</code> using Ansible. Execute the task with the following details:</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>a. Create an inventory file <code>/home/thor/ansible/inventory</code> on <code>jump_host</code> and add all application servers as managed nodes.</p>
<p>b. Create a playbook  <code>/home/thor/ansible/playbook.yml</code> on the <code>jump host</code> to copy the <code>/usr/src/finance/index.html</code> file to all application servers, placing it at <code>/opt/finance</code>.</p>
<p><code>Note:</code> Validation will run the playbook using the command <code>ansible-playbook -i inventory playbook.yml</code>. Ensure the playbook functions properly without any extra arguments.</p>

</div></div>

---

## 🚀 Complete Solution

1. **Configure the Inventory File:**
* Navigated to `/home/thor/ansible/` on the jump host.
* Created a static INI inventory file named `inventory` defining the `[app]` group.
* Added all three application servers (`stapp01`, `stapp02`, `stapp03`) and attached inline connection variables (`ansible_user`, `ansible_ssh_pass`) to fulfill the "no extra arguments" requirement.


2. **Develop the Playbook:**
* Authored `playbook.yml` to target the `app` group.
* Utilized the `ansible.builtin.copy` module to transfer `/usr/src/finance/index.html` from the control node (jump host) to the destination path `/opt/finance/index.html` on the managed nodes.
* Enabled privilege escalation (`become: yes`) at the play level, as writing to the `/opt/` directory on Linux requires root access.


3. **Validate Connectivity:**
* Executed an ad-hoc `ping` command (`ansible app -i inventory -m ping`) to verify that the jump host could successfully authenticate and communicate with all three app servers using the newly created inventory.


4. **Execute the Playbook & Verify Idempotency:**
* Ran the playbook using `ansible-playbook -i inventory playbook.yml`. The output showed `changed=1` for all nodes, confirming the file transfer.
* Executed the playbook a second time. The output showed `changed=0`, confirming that Ansible recognized the files were already present with matching checksums and performed no unnecessary overwrites.



**💡 Key Learnings & Gotchas:**

* **`ansible.builtin.copy` module behavior:** By default, the `copy` module transfers files from the *local* machine (the control node running Ansible) to the *remote* machines. If you ever need to copy a file from one location on the remote machine to another location on that *same* remote machine, you must set the `remote_src: yes` parameter.
* **Privilege Escalation:** Writing to system directories like `/opt/` requires root permissions. Forgetting to add `become: yes` to the playbook (or failing to pass `--ask-become-pass` in a real environment) would result in a `Permission denied` failure during execution.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the inventory configuration, successful ping tests across all nodes, and the dual-execution of the playbook to prove file transfer success and idempotency.

```bash
thor@jump-host ~/ansible$ cat inventory
[app]
stapp01 ansible_host=stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n

thor@jump-host ~/ansible$ cat playbook.yml
- name: Copy file to app servers
  hosts: app
  become: yes
  tasks:
    - name: Copy /usr/src/finance/index.html to /opt/finance
      ansible.builtin.copy:
        src: /usr/src/finance/index.html
        dest: /opt/finance/index.html

thor@jump-host ~/ansible$ ansible app -i inventory -m ping
stapp01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
stapp02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
stapp03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

thor@jump-host ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Copy file to app servers] *************************************************

TASK [Gathering Facts] **********************************************************
ok: [stapp03]
ok: [stapp02]
ok: [stapp01]

TASK [Copy /usr/src/finance/index.html to /opt/finance] *************************
changed: [stapp02]
changed: [stapp03]
changed: [stapp01]

PLAY RECAP **********************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

thor@jump-host ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Copy file to app servers] *************************************************

TASK [Gathering Facts] **********************************************************
ok: [stapp03]
ok: [stapp02]
ok: [stapp01]

TASK [Copy /usr/src/finance/index.html to /opt/finance] *************************
ok: [stapp02]
ok: [stapp03]
ok: [stapp01]

PLAY RECAP **********************************************************************
stapp01                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```