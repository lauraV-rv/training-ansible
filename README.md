# Configuration Automation with Ansible on an Azure Virtual Machine



This project demonstrates the complete process of:

- Provisioning infrastructure in Microsoft Azure using Terraform
- Deploying a Linux Virtual Machine
- Configuring network access (SSH and application ports)
- Automating software installation using Ansible
- Running a Docker container with a web application (Mario Bros)

This lab integrates Infrastructure as Code (IaC) and configuration management, essential practices in DevOps environments.

---

## Infrastructure Deployment with Terraform

Before starting automation with Ansible, a Linux virtual machine was deployed in Azure using Terraform.

The infrastructure includes:

- Resource Group
- Virtual Network (VNet)
- Subnet
- Public IP
- Network Security Group (NSG)
- Network Interface (NIC)
- Linux Virtual Machine

### Network Configuration

The NSG was configured to allow:

- **SSH access (port 22)** → for remote connection
- **Application access (port 8787)** → to access the deployed container

---

##  Issue Encountered: Azure Region Policy

During the initial deployment, an error occurred:
Error 403: RequestDisallowedByAzure


This happened because the selected region was not allowed by the Azure subscription policy.

### Solution

The region was changed in the Terraform variables file:

```hcl
variable "location" {
  default = "eastus2"
}

```

After updating the region and re-running Terraform, the deployment succeeded.

## Virtual Machine Details

| Item               | Value                         |
|--------------------|-------------------------------|
| Public IP          | VM IP                         |
| OS                 | Ubuntu                        |
| Authentication     | Username & Password           |
| SSH Enabled        | Yes                           |

---

## Ansible Automation Setup

### 1. Clone the Ansible Repository

```bash
git clone https://github.com/ChristianFlor/training-ansible.git
cd training-ansible

```
### 2. Configure Inventory
Edit inventory/hosts.ini:

```
[azure_vm]
<PUBLIC_IP> ansible_user=lauraadmin ansible_ssh_pass=YOUR_PASSWORD
```

### 3. Issue: Ansible Not Working on Windows
Initial error: OSError: [WinError 1] Funcion incorrecta

Solution: Use WSL (Windows Subsystem for Linux) instead of Windows.

### 4. Install sshpass
```
sudo apt update
sudo apt install sshpass -y
```

### 5. Test Connectivity
```
ansible all -i inventory/hosts.ini -m ping
```
Result: "ping": "pong" (SSH communication successful)

## Docker Installation with Ansible
Run the playbook:

```
ansible-playbook -i inventory/hosts.ini playbooks/install_docker.yml -K
```
Note: The -K flag is required to avoid "Missing sudo password" error.

## Running the Container
```
ansible-playbook -i inventory/hosts.ini playbooks/run_container.yml -K
```
Warning: Collection community.docker does not support Ansible version (does not affect execution).

## Accessing the Application

Open a browser at:

```
http://<PUBLIC_IP>:8787
```
The Mario Bros game is available.

<img width="1918" height="1031" alt="cap mario bross" src="https://github.com/user-attachments/assets/716d3be2-ce4c-405e-aadc-8c4c91ab10b7" />

## Final Verification
- VM deployed successfully with Terraform
- SSH connection established
- Ansible connected correctly
- Docker installed automatically
- Web application accessible
