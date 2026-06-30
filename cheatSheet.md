# DevOps & Troubleshooting Commands

## Linux Commands

### top
Command to list running processes/services with CPU, memory utilization, and PIDs.

---

### df -h
Lists disk usage of mounted filesystems with human-readable sizes and percentage consumed.

---

### journalctl
Used to view and debug system logs (e.g., kubelet process and Kubernetes services).

---

### tcpdump
Captures and analyzes network traffic passing through your system.

Example:
```bash
tcpdump -i eth0 port 80
```
Captures HTTP traffic on interface `eth0`.

---

### dig (Domain Information Groper)
Used for DNS lookups.

Example:
```bash
dig example.com MX
```
Finds the mail exchange servers for a domain.

---

### nslookup (Name Server Lookup)
Retrieves DNS records.

Example:
```bash
nslookup google.com
```
Returns the IP address of Google.

---

### netstat (Network Statistics)
Displays network connections and listening ports.

Example:
```bash
netstat -tuln
```
Lists all active listening TCP and UDP ports.

---

### ip addr show
Displays all IP addresses assigned to network interfaces.

---

### nc (netcat)
Reads and writes data across network connections.

Example:
```bash
nc -zv 192.168.1.1 22
```
Scans if port 22 is open on the target.

---

### tcpflow
Captures and analyzes TCP data streams for network analysis.

---

### systemctl
Primary tool for controlling the systemd init system and service manager.

Examples:
```bash
systemctl start <service>
systemctl stop <service>
systemctl restart <service>
```

---

## Kubernetes Commands

### kubectl top pod
Lists CPU and memory usage of pods.

---

### kubectl top node
Lists CPU and memory usage of nodes.

---

### kubectl get nodes (custom columns)
Lists nodes with their total CPU and memory capacity.

Example:
```bash
kubectl get nodes -o custom-columns=NAME:.metadata.name,CPU_CAPACITY:.status.capacity.cpu,MEMORY_CAPACITY:.status.capacity.memory
```

---

### kubectl debug (netshoot)
Debug a pod using a container with advanced network tools.

Example:
```bash
kubectl debug pod/myapp -it --image=nicolaka/netshoot
```

---

### kubectl debug (busybox)
Debug a pod using a lightweight BusyBox container.

Example:
```bash
kubectl debug pod/myapp -it --image=busybox
```

## terraform Commands

### terraform import
imports the current state into state file

Example:
```bash
terraform import azurerm_resource_group.imported_rg /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-existing-rg
```

### create 2 or more resources using count & for loop

Example:
```bash
resource "azurerum_virtual_machine" "vm_example" {
    count    =   2
    name     =   vm_dev_${count.index}
    // rest of the code
}

variable "vm_names" {
  type    = list(string)
  default = ["vm-web-prod", "vm-app-prod"]
}

resource "azurerum_virtual_machine" "vm_example" {
    for_each = toset(var.vm_names)
    name     =   vm_dev_${each.value}
    // rest of the code
}
```

### what is state locking and why is it required
To prevent terraform apply from 2 persons at the same time


### folder structure for terragrunt and terraform
```
terraform-git-repo/
├── modules/                   # Standard, pure Terraform modules
│   └── azure_vm/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
└── live/                            # The live environment topology
    ├── terragrunt.hcl/root.hcl      # ROOT configuration (Defines Remote State & Providers)
    ├── dev/
    │   ├── env.hcl                  # Environment level variables (e.g., location = "eastus")
    │   └── azure_vm/
    │       └── terragrunt.hcl       # Deploys dev VM (Passes inputs into infra-modules)
    └── prod/
        ├── env.hcl                  # Environment level variables (e.g., location = "westus")
        └── azure_vm/
            └── terragrunt.hcl       # Deploys prod VM (Passes inputs into infra-modules)

```
root.hcl
```
# Automatically configure the remote state backend for all sub-folders
remote_state {
  backend = "azurerm"
  config = {
    resource_group_name  = "rg-terraform-state"
    storage_account_name = "ststateaccount"
    container_name       = "tfstate"
    key                  = "${path_relative_to_include()}/terraform.tfstate"
  }
}
```
live/dev/azure_vm/terragrunt.hcl
```
# Inherit remote state configuration from the root folder
include "root" {
  path = find_in_parent_folders()
}

# Pull down the structural Terraform module code
terraform {
  source = "../../../modules//azure_vm"
}

# Define your infrastructure arguments directly
inputs = {
  vm_names = ["vm-web-dev", "vm-app-dev"]
  size     = "Standard_B1s"
}
