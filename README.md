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

## Directory Structure & Best Practices

Organizing your Ansible project with a clear and logical directory structure is crucial for maintainability, scalability, and team collaboration. Below is the recommended structure for this project:

![Project Directory Structure](img/Ansible4.png)

**Project Layout:**

```
ansible-mega-project/
├── inventories/          # Inventory files defining hosts and groups
├── playbooks/            # Main playbook files
├── roles/                # Reusable roles
├── group_vars/           # Variables for groups of hosts
└── host_vars/            # Variables for individual hosts
```

### Best Practices for Ansible Project Organization

**1. Inventories Directory**
- Store all your inventory files here (e.g., `hosts`, `production`, `staging`, `development`)
- Keep inventory files organized by environment
- Makes it easy to manage different target environments separately

**2. Playbooks Directory**
- Keep main orchestration playbooks here
- Use descriptive names like `site.yml`, `webservers.yml`, `databases.yml`
- Playbooks should call roles rather than contain individual tasks
- This keeps playbooks clean and roles reusable

**3. Roles Directory**
- Create a subdirectory for each role (e.g., `webserver`, `database`, `monitoring`)
- Each role should be self-contained and reusable
- Follows the standard Ansible role structure for better organization

**4. Group Variables (group_vars)**
- Store variables that apply to an entire group of hosts
- File naming matches group names from inventory
- Reduces repetition and makes configuration management easier
- Example: `group_vars/webservers.yml`, `group_vars/all.yml`

**5. Host Variables (host_vars)**
- Store variables specific to individual hosts
- File naming matches hostnames from inventory
- Use for host-specific configurations that differ from group settings
- Example: `host_vars/server1.example.com.yml`
