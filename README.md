# Ansible Mega Project

## Topics Covered

| Topic | Status |
|-------|--------|
| Directory Structure & Best Practices | ✅ |
| Inventory Management | ✅ |
| Variables (group_vars, host_vars, role vars) | ✅ |
| Playbooks & Roles | ✅ |
| Templates with Jinja2 | ✅ |
| Task Inclusion & Organization | ✅ |
| Handlers for Service Management | ✅ |

## Environment Setup

This project uses two Amazon EC2 instances running Amazon Linux 2023 in AWS. These instances serve as target nodes for Ansible automation and configuration management.

**Infrastructure:**

![EC2 Instances Dashboard](img/Ansible1.png)

- **Instance 1:** rimo-ansible-instance-1 (i-0e547fd60f05e2c72) - t3.micro - eu-central-1b
- **Instance 2:** rimo-ansible-instance-2 (i-02013a3b7103f44e8) - t3.micro - eu-central-1b

**SSH Connection Verification:**

Successfully connected to both instances using SSH with private key authentication:

![SSH Connection - Instance 1](img/Ansible2.png)

![SSH Connection - Instance 2](img/Ansible3.png)

Both instances are running and accessible for Ansible automation.
