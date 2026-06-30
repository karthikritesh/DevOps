# DevOps & Troubleshooting Commands

## 🐧 Linux Commands

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

## ☸️ Kubernetes Commands

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
