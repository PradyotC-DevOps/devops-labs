## 13 - SSH into an Azure Virtual Machine

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team is working on setting up secure SSH access for their virtual machines in Azure. One of the requirements is to add the SSH public key of the root user from the Azure client host (landing host) to the <code>xfusion-vm</code> Azure VM's <code>authorized_keys</code> file. This ensures secure and password-less SSH access to the VM.</p>
<h3 id="task-details">Task Details:</h3>
<p>1) <strong>VM Details</strong>: </p>
<ul>
<li>The VM is named <code>xfusion-vm</code> and is running in the <code>southcentralus</code> region. The default SSH user is <code>azureuser</code> — use this user to connect to the VM.</li>
<li>You need to add the root user's SSH public key from the Azure client host to the <code>authorized_keys</code> file of the VM's root user.</li>
<li>The SSH public key of the root user on the Azure client host is located at <code>/root/.ssh/id_rsa.pub</code>.</li>
</ul>
<p>2) <strong>Public Key Addition</strong>: </p>
<ul>
<li>Copy the public key located at <code>/root/.ssh/id_rsa.pub</code> on the Azure client host to the <code>authorized_keys</code> file of the root user on <code>xfusion-vm</code>.</li>
<li>Ensure that the proper permissions for the <code>.ssh</code> folder and <code>authorized_keys</code> file are set on the VM.</li>
</ul>
<p>3) <strong>Verification</strong>: </p>
<ul>
<li>After adding the public key, make sure that you are able to SSH into the <code>xfusion-vm</code> VM as the <code>root</code> user from the Azure client host without needing a password.</li>
</ul>
<h3 id="important-notes">Important Notes:</h3>
<ul>
<li>Ensure that the VM is up and running before attempting to SSH.</li>
<li>You may need to adjust the firewall or security group rules for the VM to allow SSH access.</li>
</ul>

</div>

---

## 🚀 Complete Solution

### 1. Identify the VM's IP Address
First, retrieve the public IP address of the target `xfusion-vm` using the Azure CLI. This command filters the output to only return the IP address string:

```bash
VM_IP=$(az vm list-ip-addresses -n xfusion-vm --query "[0].virtualMachine.network.publicIpAddresses[0].ipAddress" -o tsv)
echo "Target VM IP: $VM_IP"

```

### 2. Configure Root SSH Access via the Default User

Azure VMs typically restrict direct `root` logins and include a `command=` prefix in the default `authorized_keys` file that forces you to use the `azureuser` account.

To bypass this restriction and set up our key properly, we log in as `azureuser` and execute a script to overwrite the `authorized_keys` file, set strict permissions, update the SSH daemon configuration, and restart the service.

```bash
# Read the public key from the client host
PUB_KEY=$(cat /root/.ssh/id_rsa.pub)

# Connect as 'azureuser' to apply the configurations
ssh -o StrictHostKeyChecking=no azureuser@$VM_IP "
  # 1. Overwrite the file completely to remove the Azure restriction
  echo '$PUB_KEY' | sudo tee /root/.ssh/authorized_keys > /dev/null && 
  
  # 2. Ensure directory and file permissions are strictly set
  sudo chmod 700 /root/.ssh &&
  sudo chmod 600 /root/.ssh/authorized_keys &&
  
  # 3. Explicitly allow root login via SSH keys in the daemon config
  sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin prohibit-password/' /etc/ssh/sshd_config &&
  
  # 4. Restart the SSH service to apply changes
  sudo systemctl restart sshd
"

```

*Note: We explicitly set the `.ssh` directory to `700` and `authorized_keys` to `600`. SSH will silently reject connections if permissions are too permissive.*

### 3. Verify Password-less Root Access

With the configuration updated and the service restarted, verify the setup by attempting to log in directly as `root`.

```bash
ssh root@$VM_IP

```

**Output:**

```bash
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1062-azure x86_64)
...
root@xfusion-vm:~# whoami
root
root@xfusion-vm:~# hostname
xfusion-vm

```

## ✅ Conclusion

Successfully bypassed default Azure SSH restrictions, configured the `sshd_config`, and established a secure, key-based SSH trust between the Azure client host's root user and the `xfusion-vm` root user.