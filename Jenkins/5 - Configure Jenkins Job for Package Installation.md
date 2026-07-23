# 5 — Configure Jenkins Job for Package Installation

## 📋 Task Overview

Some new requirements have come up to install and configure some packages on the Nautilus infrastructure under Stratos Datacenter. The Nautilus DevOps team installed and configured a new Jenkins server so they wanted to create a Jenkins job to automate this task.  
Find below more details and complete the task accordingly:  
1. Access the Jenkins UI by clicking on the Jenkins button in the top bar. Log in using the credentials: username admin and password Adm!n321.  
2. Create a new Jenkins job named install-packages and configure it with the following specifications:  
    Add a string parameter named PACKAGE.  
    Configure the job to install a package specified in the $PACKAGE parameter on the storage server within the Stratos Datacenter.  
Note:  
1. Ensure to install any required plugins and restart the Jenkins service if necessary. Opt for Restart Jenkins when installation is complete and no jobs are running on the plugin installation/update page. Refresh the UI page if needed after restarting the service.  
2. Verify that the Jenkins job runs successfully on repeated executions to ensure reliability.  
3. Capture screenshots of your configuration for documentation and review purposes. Alternatively, use screen recording software like loom.com for comprehensive documentation and sharing.

---

## 🚀 Complete Solution

1. **Create Parameterized Job:**
* Created a new Freestyle project named `install-packages`.
* Checked **This project is parameterized**.
* Added a **String Parameter** named `PACKAGE` with a default value of `vim-enhanced`.


2. **Configure Build Step:**
* Added an **Execute shell** build step.
* Implemented the following conditional deployment script to handle authentication and OS-agnostic package management via `sshpass`:
```bash
sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@ststor01 << EOF
  if command -v yum >/dev/null 2>&1; then
      echo "Detected yum. Installing..."
      echo 'Bl@kW' | sudo -S yum install -y $PACKAGE
  elif command -v apt-get >/dev/null 2>&1; then
      echo "Detected apt-get. Installing..."
      echo 'Bl@kW' | sudo -S apt-get update
      echo 'Bl@kW' | sudo -S apt-get install -y $PACKAGE
  else
      echo "Neither yum nor apt-get found."
      exit 1
  fi
EOF

```




3. **Execution & Verification:**
* Triggered **Build with Parameters** using the default `vim-enhanced` variable.
* Monitored the console output to verify successful execution, noting the secure SSH connection, the successful `yum` detection, dependency resolution (`gpm-libs`, `vim-common`), and the final `Finished: SUCCESS` status.