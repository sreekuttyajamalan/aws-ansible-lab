# AWS EC2 & Ansible Automation (Using MobaXterm)

## 📌 Project Overview

This project demonstrates:

* Creating EC2 instances in AWS
* Connecting to EC2 using **MobaXterm**
* Setting up an **Ansible Control Node**
* Managing a Target EC2 instance using Ansible
* Writing and executing Ansible playbooks
* Using variables, modules, handlers, and roles
* Ensuring idempotency of automation

This is a beginner-friendly DevOps automation project using AWS and Ansible.

---

## 🏗️ Project Architecture

```
+------------------------+        SSH        +------------------------+
|  EC2: Ansible Control  |  -------------->  |   EC2: Target Instance |
|  (Ubuntu 22.04)        |                  |   (Ubuntu 22.04)       |
+------------------------+                  +------------------------+
         ↑
         |
   Connected via
   MobaXterm (SSH)
```

---

## ✅ Tools & Technologies Used

* AWS (EC2)
* Ubuntu 22.04
* MobaXterm (for SSH connection)
* Ansible
* SSH Key Pair (.pem file)

---

## 🚀 Step 1 — Create EC2 Instances in AWS

### **1.1 Create Ansible Control Node**

1. Go to AWS Console → EC2 → Launch Instance
2. Choose:

   * Name: `ansible-control`
   * AMI: Ubuntu 22.04
   * Instance Type: t2.micro (Free tier)
   * Key Pair: Create or select existing `.pem` file
3. Security Group:

   * Allow SSH (Port 22) from your IP
4. Click **Launch Instance**

### **1.2 Create Target EC2 Instance**

1. Click Launch Instance again
2. Choose:

   * Name: `ansible-target`
   * AMI: Ubuntu 22.04
   * Instance Type: t2.micro
   * Same Key Pair as control node
3. Security Group:

   * Allow SSH (Port 22) from Control Node IP
4. Click **Launch Instance**

---

## 🔐 Step 2 — Connect to EC2 Using MobaXterm

1. Open **MobaXterm**
2. Click **Session → SSH**
3. Enter:

   * Remote host: `<EC2_PUBLIC_IP>`
   * Username: `ubuntu`
4. Click **Advanced SSH settings**
5. Browse and select your `.pem` key file
6. Click **OK** → Connect

Repeat the same for both Control Node and Target Node.

---

## ⚙️ Step 3 — Install Ansible on Control Node

Run in MobaXterm (on Control Node):

```bash
sudo apt update
sudo apt install ansible -y
```

Verify:

```bash
ansible --version
```

---

## 📂 Step 4 — Configure Ansible Inventory

Create project folder:

```bash
mkdir ansible-aws
cd ansible-aws
nano inventory
```

Add:

```ini
[web]
<TARGET_NODE_PRIVATE_IP> ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/your-key.pem
```

Test connection:

```bash
ansible web -i inventory -m ping
```

You should see: **pong**

---

## 📝 Step 5 — Create Ansible Playbook

Create playbook:

```bash
nano install.yml
```

Add:

```yaml
---
- name: Install Apache on AWS EC2
  hosts: web
  become: yes

  vars:
    package_name: apache2

  tasks:
    - name: Install Apache
      apt:
        name: "{{ package_name }}"
        state: present

    - name: Create custom webpage
      copy:
        content: "Hello from Ansible on AWS!"
        dest: /var/www/html/index.html
      notify:
        - Restart Apache

  handlers:
    - name: Restart Apache
      service:
        name: apache2
        state: restarted
```

Run playbook:

```bash
ansible-playbook -i inventory install.yml
```

---

## ✅ Step 6 — Verify Configuration

On target EC2:

```bash
systemctl status apache2
```

Open in browser:

```
http://<TARGET_NODE_PUBLIC_IP>
```

You should see your custom web page.

---

## 🔁 Step 7 — Ensure Idempotency

Run the playbook again:

```bash
ansible-playbook -i inventory install.yml
```

If you see:

```
ok=...
changed=0
```

Then your playbook is idempotent.

---

## 📁 Repository Structure

```
ansible-aws-ec2/
│── inventory
│── install.yml
│── README.md
```

---

## ▶️ How to Run This Project

1. Launch two EC2 instances in AWS
2. Connect using MobaXterm
3. Install Ansible on control node
4. Configure inventory
5. Run playbook:

```bash
ansible-playbook -i inventory install.yml
```

---

## 👤 Author

Your Name
DevOps Student / Cloud Enthusiast

---

## 📚 References

* AWS EC2 Documentation
* Ansible Official Documentation
