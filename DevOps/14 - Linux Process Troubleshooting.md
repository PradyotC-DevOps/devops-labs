# 14 - Linux Process Troubleshooting


## 📋 Task Overview

The production support team of xFusionCorp Industries has deployed some of the latest monitoring tools to keep an eye on every service, application, etc. running on the systems. One of the monitoring systems reported about Apache service unavailability on one of the app servers in `Stratos DC`.

Identify the faulty app host and fix the issue. Make sure Apache service is up and running on all app hosts. They might not have hosted any code yet on these servers, so you don't need to worry if Apache isn't serving any pages. Just make sure the service is up and running. Also, make sure Apache is running on port `8082` on all app servers.

## 🚀 Complete Solution

1. **Set Up Passwordless SSH:** Required for automated looping.
To dramatically speed up the troubleshooting process and allow for automated Bash loops, generate an SSH key on the `jump-host` and distribute it to the three application servers.

```bash
# Generate the key (press Enter to accept defaults)
ssh-keygen

# Copy the key to each app server
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03

```

*(Passwords: `Ir0nM@n` for tony, `Am3ric@` for steve, `BigGr33n` for banner)*

2. **Identify the Faulty Server:**
From the `jump-host`, run a bash loop to check the Apache (`httpd`) service status across all three backend servers without needing to log into each one manually.

```bash
for host in tony@stapp01 steve@stapp02 banner@stapp03; do 
  echo -e "\n=== $host ==="
  ssh $host "systemctl is-active httpd || echo 'SERVICE IS DOWN OR MISSING'"
done

```

**Output:**

```text
tony@stapp01
SERVICE IS DOWN OR MISSING

steve@stapp02
active

banner@stapp03
active
```

**Result:** `stapp01` returned `failed`, indicating it was the faulty host.


3. **Install and Configure the Faulty Host (stapp01):**
SSH into the failing server to install the missing package and configure it.

```bash
ssh tony@stapp01

```

Install the Apache HTTP server and enable it to start on boot:

```bash
sudo yum install httpd -y
sudo systemctl enable httpd

```


4. **Resolve Port 8082 Conflict:** Fixing port conflicts.
During startup, Apache may fail if another service is already bound to the target port. Identify the conflicting process (in this case, `sendmail`) and terminate it.

```bash
# Identify what is using the port
sudo ss -tulpn | grep -E ':80|:8082'

# Kill the conflicting sendmail process using its PID (e.g., 18401)
sudo kill -9 18401

```


5. **Apply the Port Change to stapp01:**
Update the Apache configuration to listen on port `8082`, then start the service.

```bash
sudo sed -i 's/^Listen .*/Listen 8082/' /etc/httpd/conf/httpd.conf
sudo systemctl start httpd

# Verify it is running properly
sudo systemctl status httpd

# Exit back to the jump-host
exit

```


6. **Deploy Configuration to Remaining Fleet:**
To ensure consistency, apply the same port configuration and kill any lingering `sendmail` conflicts on the remaining application servers (`stapp02` and `stapp03`) using a streamlined bash loop from the `jump-host`.

```bash
for host in steve@stapp02 banner@stapp03; do 
  echo -e "\n=== Configuring $host ==="
  ssh -t $host "sudo pkill sendmail; sudo sed -i 's/^Listen .*/Listen 8082/' /etc/httpd/conf/httpd.conf && sudo systemctl restart httpd && sudo systemctl status httpd | grep Active"
done

```

*Result:* Both servers return `Active: active (running)`, confirming the fleet is fully synchronized and operational on port `8082`.