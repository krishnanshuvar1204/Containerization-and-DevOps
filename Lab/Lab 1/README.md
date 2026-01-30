# Experiment 1: Virtual Machines vs Containers

## Objective
Compare Virtual Machines and Containers by deploying Nginx web server in both environments and analyzing their resource utilization and performance characteristics.

## Requirements

### Hardware
- 64-bit system with virtualization enabled
- Minimum 8 GB RAM
- Internet connection

### Software
- Oracle VirtualBox
- Vagrant
- Windows Subsystem for Linux (WSL 2)
- Docker Engine

## Theory

**Virtual Machine (VM)**
- Emulates complete physical computer with own OS kernel
- Full isolation, higher resource usage, slower startup

**Container**
- OS-level virtualization sharing host kernel
- Lightweight, fast startup, efficient resources

---

## Part A: Virtual Machine Setup

### 1. Install VirtualBox
Download and install VirtualBox from [official website](https://www.virtualbox.org/)

![VirtualBox Installation](Screenshots/Screenshot(833).png)

### 2. Install Vagrant
Install Vagrant and verify:
```bash
vagrant --version
```
![Vagrant Installation](Screenshots/Screenshot(835).png)

### 3. Create Ubuntu VM
```bash
mkdir vm-lab
cd vm-lab
vagrant init ubuntu/jammy64
vagrant up
vagrant ssh
```
![Vagrant Setup](Screenshots/Screenshot(836).png)

### 4. Install Nginx in VM
```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl start nginx
curl localhost
```
![Nginx VM](Screenshots/Screenshot(837).png)
![Nginx VM Installed](Screenshots/Screenshot(838).png)

### 5. Cleanup
```bash
vagrant halt
vagrant destroy
```

---

## Part B: Container Setup

### 1. Install WSL 2
```bash
wsl --install
```
Reboot after installation.


### 2. Install Ubuntu on WSL
```bash
wsl --install -d Ubuntu
```
![WSL Ubuntu](Screenshots/Screenshot(840).png)

### 3. Install Docker
```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo usermod -aG docker $USER
```
![Docker Installation](screenshots/docker-install.png)

### 4. Run Nginx Container
```bash
docker pull nginx
docker run -d -p 8080:80 --name nginx-container nginx
curl localhost:8080
```
![Nginx Container](Screenshots/Screenshot(841).png)

---

## Resource Monitoring

### VM Commands
```bash
free -h
htop
systemd-analyze
```

### Container Commands
```bash
docker stats
free -h
```
![Resource Comparison](Screenshots/Screenshot(839).png)
![Resource Comparison](Screenshots/Screenshot(840).png)
![Resource Comparison](Screenshots/Screenshot(843).png)
![Resource Comparison](Screenshots/Screenshot(844).png)
---

## Comparison Results

| Parameter | Virtual Machine | Container |
|-----------|----------------|-----------|
| Boot Time | High | Very Low |
| RAM Usage | High | Low |
| CPU Overhead | Higher | Minimal |
| Disk Usage | Larger | Smaller |
| Isolation | Strong | Moderate |

---

## Conclusion
VMs provide complete isolation with higher resource overhead, while Containers offer lightweight, efficient deployment by sharing the host kernel. The choice depends on isolation requirements and resource constraints.