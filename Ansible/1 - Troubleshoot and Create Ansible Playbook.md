## 1 - Troubleshoot and Create Ansible Playbook

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">

<p>An Ansible playbook needs completion on the <code>jump host</code>, where a team member left off. Below are the details:<br><br></p>

</div><br><div class="markdown-body text-sm mb-8">

<ol>
<li><p>The inventory file <code>/home/thor/ansible/inventory</code> requires adjustments. The playbook must run on <code>App Server 2</code> in <code>Stratos DC</code>. Update the inventory accordingly.<br><br></p></li>
<li><p>Create a playbook <code>/home/thor/ansible/playbook.yml</code>. Include a task to create an empty file <code>/tmp/file.txt</code> on <code>App Server 2</code>.<br><br></p></li>
</ol>
<p><code>Note:</code> Validation will run the playbook using the command <code>ansible-playbook -i inventory playbook.yml</code>. Ensure the playbook works without any additional arguments.</p>

---

## 🚀 Complete Solution

### 1. Update the Inventory File
Navigate to the Ansible directory and update the `inventory` file. App Server 2 goes by the hostname `stapp02`. We will overwrite the inventory to ensure it targets the correct host with the standard Nautilus credentials for App Server 2 (`steve` / `Am3ric@`).

```bash
cd /home/thor/ansible

cat <<EOF> inventory
stapp02 ansible_host=stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@
EOF

```

### 2. Create the Ansible Playbook

Create the `playbook.yml` file. We will use the built-in Ansible `file` module with `state: touch` to create the empty file.

```bash
cat <<EOF> playbook.yml
---
- name: Create empty file on App Server 2
  hosts: stapp02
  become: yes
  tasks:
    - name: Create /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
EOF

```

### 3. Execute the Playbook

Run the playbook using the updated inventory file. The task validation requires that the playbook runs perfectly without passing any extra arguments (like `-u` or `-k`).

```bash
ansible-playbook -i inventory playbook.yml

```

**Expected Output:**

```text
PLAY [Create empty file on App Server 2] *************************************************

TASK [Gathering Facts] *******************************************************************
ok: [stapp02]

TASK [Create /tmp/file.txt] **************************************************************
changed: [stapp02]

PLAY RECAP *******************************************************************************
stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

### 4. Verify the Execution (Optional)

To verify that the playbook actually worked, you can use an ad-hoc Ansible command to check if the file exists on `App Server 2`.

```bash
ansible all -i inventory -a "ls -l /tmp/file.txt"

```

**Expected Output:**

```text
stapp02 | CHANGED | rc=0 >>
-rw-r--r-- 1 root root 0 Jul 25 14:15 /tmp/file.txt

```

## ✅ Conclusion

Successfully corrected the inventory file to target `App Server 2` and executed an Ansible playbook to manage the remote file system in a declarative, automated manner.