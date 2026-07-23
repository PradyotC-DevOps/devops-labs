# 13 - IPtables Installation And Configuration

## 📋 Task Overview

We have one of our websites up and running on our `Nautilus` infrastructure in `Stratos DC`. Our security team has raised a concern that right now Apache’s port i.e `3001` is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:

1. Install `iptables` and all its dependencies on each app host.
2. Block incoming port `3001` on all apps for everyone except for LBR host.
3. Make sure the rules remain, even after system reboot.

---


## 🚀 Complete Solution

1. **Identify the Load Balancer IP:**
Before applying strict firewall rules, resolve the exact IP of the Load Balancer (`stlb01`) so it can be whitelisted. From the jump host, run:

    ```bash
    getent hosts stlb01
    ```

*Output confirms the LBR IP is **`10.244.97.189`**.*


2. **Access the Application Server:** Repeat this process for all three application servers.
SSH into the first application server (`stapp01`) and elevate your privileges to root.

    ```bash
    ssh tony@stapp01
    sudo su -
    ```

*(Credentials: `tony@stapp01`, `steve@stapp02`, `banner@stapp03`)*


3. **Install iptables Utilities:**
Install both the core `iptables` command-line utility and the `iptables-services` package, which provides the systemd service files required for persistence.

    ```bash
    yum install iptables iptables-services -y
    ```

4. **Apply and Save Firewall Rules:** Order matters: Always specify ACCEPT rules before DROP rules.
Allow incoming TCP traffic on port `3001` exclusively from the Load Balancer IP, and drop all other traffic to that port.

    ```bash
    # Allow traffic from LBR host
    iptables -A INPUT -p tcp -s 10.244.97.189 --dport 3001 -j ACCEPT
    
    # Block all other incoming traffic to port 3001
    iptables -A INPUT -p tcp --dport 3001 -j DROP
    ```

Save the rules in memory directly to the system configuration file so the service can read them.

    ```bash
    iptables-save > /etc/sysconfig/iptables
    ```

5. **Start and Enable the Service:** Fulfills requirement #3.
Start the iptables service and enable it so the rules automatically reload if the system reboots.

    ```bash
    systemctl start iptables
    systemctl enable iptables
    ```

Verify the configuration by checking the service status and the active rule chain.

    ```bash
    systemctl status iptables
    iptables -L INPUT -n -v
    ```

6. **Apply to the Remaining Fleet:**
Exit the SSH session for `stapp01` and repeat **Steps 2 through 5** for the remaining backend servers (`stapp02` and `stapp03`) to fully secure the environment.