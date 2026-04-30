# Experiment 9

## Ansible

---

# Theory

## Problem Statement

Managing infrastructure manually across multiple servers leads to:

* Configuration drift
* Inconsistent environments
* Time-consuming repetitive tasks

Scaling from one server to hundreds becomes difficult using manual SSH-based administration.

---

## What is Ansible?

* Open-source automation tool for configuration management, deployment, and orchestration
* Agentless architecture (uses SSH for Linux, WinRM for Windows)
* Uses YAML-based playbooks
* Industry-standard automation tool

---

## How Ansible Solves the Problem

* **Agentless** → No installation on target machines
* **Idempotent** → Same result every time
* **Declarative** → Define desired state
* **Push-based** → Control node sends instructions

---

## Key Concepts

| Component     | Description                    |
| ------------- | ------------------------------ |
| Control Node  | Machine with Ansible installed |
| Managed Nodes | Target servers                 |
| Inventory     | List of servers                |
| Playbooks     | YAML automation files          |
| Tasks         | Individual actions             |
| Modules       | Built-in functions             |

---

## How Ansible Works

* Control node connects to managed nodes via SSH
* Executes modules through tasks
* Tasks combine into playbooks
* Inventory defines servers

---

## Benefits

* Free & open-source
* Easy to learn
* No agents required
* Reusable and modular

---

# Part A: Installation

## Install using pip

```bash id="pipinstall"
pip install ansible
ansible --version
```

📸 Screenshot:
`![Ansible Install Pip](../screenshots/lab9/Screenshot%202026-04-26%20012840.png)

---

## Install using apt

```bash id="aptinstall"
sudo apt update -y
sudo apt install ansible -y
ansible --version
```

📸 Screenshot:
`![Ansible Install Apt](../screenshots/lab9/Screenshot%202026-04-26%20012854.png)

---

## Test Installation

```bash id="pingtest"
ansible localhost -m ping
```

### Expected Output

```bash id="expected"
localhost | SUCCESS => {
  "changed": false,
  "ping": "pong"
}
```

📸 Screenshot:
`![Ansible Ping](../screenshots/lab9/Screenshot%202026-04-26%20012904.png)

---

# Part B: Docker + SSH Setup

---

## Step 1: Create SSH Key Pair

```bash id="sshkey"
ssh-keygen -t rsa -b 4096
cp ~/.ssh/id_rsa.pub .
cp ~/.ssh/id_rsa .
```

📸 Screenshot:
`![SSH Key](../screenshots/lab9/Screenshot%202026-04-26%20012738.png)

---

## Step 2: Create Dockerfile

```dockerfile id="dockerfile"
FROM ubuntu

RUN apt update -y
RUN apt install -y python3 python3-pip openssh-server
RUN mkdir -p /var/run/sshd

RUN mkdir -p /run/sshd && \
    echo 'root:password' | chpasswd && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config && \
    sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config

RUN mkdir -p /root/.ssh && chmod 700 /root/.ssh

COPY id_rsa /root/.ssh/id_rsa
COPY id_rsa.pub /root/.ssh/authorized_keys

RUN chmod 600 /root/.ssh/id_rsa && \
    chmod 644 /root/.ssh/authorized_keys

RUN sed -i 's@session\\s*required\\s*pam_loginuid.so@session optional pam_loginuid.so@g' /etc/pam.d/sshd

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
```

📸 Screenshot:
`![Dockerfile](../screenshots/lab9/Screenshot%202026-04-26%20012753.png)

---

## Step 3: Build Image

```bash id="buildimg"
docker build -t ubuntu-server .
```

📸 Screenshot:
`![Docker Build](../screenshots/lab9/Screenshot%202026-04-26%20012753.png)

---

## Step 4: Run Container

```bash id="runcontainer"
docker run -d -p 2222:22 --name ssh-test-server ubuntu-server
```

📸 Screenshot:
`![Docker Run](../screenshots/lab9/Screenshot%202026-04-26%20012812.png)

---

## Step 5: Get Container IP

```bash id="getip"
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' ssh-test-server
```

📸 Screenshot:
`![Container IP](../screenshots/lab9/Screenshot%202026-04-26%20012827.png)

---

## Step 6: Test SSH

### Password Login

```bash id="sshpass"
ssh root@localhost -p 2222
```

### Key-based Login

```bash id="sshkeylogin"
ssh -i ~/.ssh/id_rsa root@localhost -p 2222
```

📸 Screenshot:
`![SSH Login](../screenshots/lab9/Screenshot%202026-04-26%20012827.png)

---

## Cleanup

```bash id="cleanup"
docker stop ssh-test-server
docker rm ssh-test-server
```
📸 Screenshot:
`![SSH Login](../screenshots/lab9/Screenshot%202026-04-26%20012904.png)


---

# Part C: Ansible with Docker

---

## Step 1: Start Multiple Containers

```bash id="multicontainer"
for i in {1..4}; do
  docker run -d --rm -p 220${i}:22 --name server${i} ubuntu-server
done
```

📸 Screenshot:
`![Multiple Servers](../screenshots/lab9/Screenshot%202026-04-30%20224342.png)

---

## Stop All Servers

```bash id="stopservers"
for i in {1..4}; do
  docker stop server${i}
done
```

---

## Step 2: Create Inventory

```bash id="inventory"
echo "[servers]" > inventory.ini

for i in {1..4}; do
  docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' server${i} >> inventory.ini
done

cat << EOF >> inventory.ini

[servers:vars]
ansible_user=root
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
EOF
```

---

## Step 3: View Inventory

```bash id="viewinventory"
cat inventory.ini
```

---

## Step 4: Test Connectivity

```bash id="ansibleping"
ansible all -i inventory.ini -m ping
```
📸 Screenshot:
`![Verification](../screenshots/lab9/Screenshot%202026-04-30%20224523.png)
---

## Step 5: Create Playbook

```yaml id="playbook"
---
- name: Update servers
  hosts: all
  become: yes

  tasks:
    - name: Update packages
      apt:
        update_cache: yes
```

---

## Step 6: Run Playbook

```bash id="runplaybook"
ansible-playbook -i inventory.ini playbook1.yml
```
📸 Screenshot:
`![Verification](../screenshots/lab9/Screenshot%202026-04-30%20224545.png)
---

## Step 7: Verify

```bash id="verify"
ansible all -i inventory.ini -m command -a "cat /root/ansible_test.txt"
```

📸 Screenshot:
`![Verification](../screenshots/lab9/Screenshot%202026-04-30%20224557.png)

---

# Conclusion

Ansible simplifies server management by automating tasks and ensuring consistency across multiple systems.

---

# Key Takeaways

* Ansible is agentless
* Uses SSH
* Playbooks automate tasks
* Docker simulates servers

---
