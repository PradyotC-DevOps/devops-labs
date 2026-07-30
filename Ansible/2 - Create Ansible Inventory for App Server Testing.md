# 2 - Create Ansible Inventory for App Server Testing

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team is testing Ansible playbooks on various servers within their stack. They've placed some playbooks under <code>/home/thor/playbook/</code> directory on the <code>jump host</code> and now intend to test them on <code>app server 1</code> in <code>Stratos DC</code>. However, an inventory file needs creation for Ansible to connect to the respective app. Here are the requirements:</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>a. Create an ini type Ansible inventory file <code>/home/thor/playbook/inventory</code> on <code>jump host</code>.<br><br></p>
<p>b. Include <code>App Server 1</code> in this inventory along with necessary variables for proper functionality.<br><br></p>
<p>c. Ensure the inventory hostname corresponds to the <code>server name</code> as per the wiki, for example <code>stapp01</code> for <code>app server 1</code> in <code>Stratos DC</code>.<br><br></p>
<p><code>Note:</code> Validation will execute the playbook using the command <code>ansible-playbook -i inventory playbook.yml</code>. Ensure the playbook functions properly without any extra arguments.</p>

</div>

---

## 🚀 Complete Solution

1. **Navigate to the Playbook Directory:**
Logged into the `jump host` and navigated to the pre-existing playbook directory:
```bash
cd /home/thor/playbook/

```


2. **Configure the INI Inventory File:**
* Created a new file named `inventory`.
* Configured the inventory to include the `stapp01` host along with the inline SSH connection variables (`ansible_user` and `ansible_ssh_pass`). This satisfies the requirement to run the playbook without passing `--user` or `--ask-pass` flags.


```ini
[all]
stapp01 ansible_host=stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n

```


3. **Validate Connectivity:**
* Ran an ad-hoc Ansible `ping` module to verify that the jump host could successfully parse the inventory and authenticate with the app server.


```bash
ansible all -i inventory -m ping

```


4. **Execute the Playbook:**
* Ran the provided `playbook.yml` (which installs and starts the `httpd` service) using the newly created inventory file.


```bash
ansible-playbook -i inventory playbook.yml

```


* *Idempotency Check:* Executed the playbook a second time to ensure declarative state. The second run correctly returned `changed=0`, proving the `httpd` package and service were already in the desired state.



**💡 Key Learnings & Gotchas:**

* **Inventory Variables vs. Production Security:** While passing `ansible_ssh_pass` directly in plain-text inside the inventory file is standard for quick testing and lab environments to fulfill the "no extra arguments" requirement, it is an anti-pattern for production. In real-world enterprise environments, authentication should be handled via SSH key pairs (passwordless SSH) or encrypted securely using **Ansible Vault**.
* **Idempotency in Action:** Running the playbook twice and observing the green `ok` statuses (with 0 changes) is the perfect demonstration of Ansible's declarative nature.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the inventory configuration, successful ping test, and the dual-execution of the playbook to prove idempotency:

```bash
thor@jump-host ~/playbook$ cat inventory
[all]
stapp01 ansible_host=stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n

thor@jump-host ~/playbook$ ansible all -i inventory -m ping
stapp01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

thor@jump-host ~/playbook$ ansible-playbook -i inventory playbook.yml

PLAY [all] *******************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************
ok: [stapp01]

TASK [Install httpd package] *************************************************************************************
changed: [stapp01]

TASK [Start service httpd] ***************************************************************************************
changed: [stapp01]

PLAY RECAP *******************************************************************************************************
stapp01                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

thor@jump-host ~/playbook$ ansible-playbook -i inventory playbook.yml

PLAY [all] *******************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************
ok: [stapp01]

TASK [Install httpd package] *************************************************************************************
ok: [stapp01]

TASK [Start service httpd] ***************************************************************************************
ok: [stapp01]

PLAY RECAP *******************************************************************************************************
stapp01                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```