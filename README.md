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


## Inventory Management
The inventory file (`hosts.ini`) defines all the hosts and groups that Ansible will manage. This is the foundation of your Ansible infrastructure, specifying which servers to target and how to connect to them.

**Inventory File Structure:**

![Inventory File - hosts.ini](img/Ansible5.png)

Our `hosts.ini` file contains two EC2 instances organized into the `[webservers]` group. Each host entry specifies the IP address, Python interpreter location, SSH user, and 
private key path needed for Ansible to connect and execute commands.

**Testing Connectivity:**

![Inventory Connectivity Test with Ping](img/Ansible6.png)

Using the `ansible all -i inventories/hosts.ini -m ping` command, we verify that both instances are reachable and responsive. 
The successful "pong" responses from both `web1` and `web2` confirm that Ansible can communicate with all managed hosts. 
This ping test ensures your inventory configuration is correct before running actual playbooks.

## Playbooks & Roles

Playbooks are the core of Ansible automation. They contain plays that define a set of tasks to execute on target hosts. Roles provide a way to organize playbooks into reusable, 
modular components for better code organization and maintainability.

**Installing and Configuring Nginx:**

![Install Nginx Playbook](img/Ansible7.png)

The `install-nginx.yml` playbook demonstrates task organization with multiple steps: it ensures Nginx is installed using the package manager, starts the Nginx service, 
and enables it to run on system boot. Each task targets the webservers group, allowing Ansible to execute these steps on all hosts simultaneously.

**Deploying a Custom Webpage:**

![Deploy Webpage Playbook](img/Ansible8.png)

The `deploy-webpage.yml` playbook shows how to use the `copy` module with dynamic content. It creates a custom `index.html` file using Jinja2 variables like `{{ inventory_hostname }}` 
to personalize the webpage with server-specific information. This demonstrates how playbooks can generate and deploy configuration files dynamically across all managed servers.

**Execution Results:**

![Playbook Execution and Verification](img/Ansible9.png)

Running these playbooks successfully installs Nginx on both webservers (web1 and web2) and deploys the custom webpage. The terminal output shows successful curl requests to both servers, 
displaying the personalized welcome pages with server hostnames and dates. This proves that Ansible has orchestrated the entire deployment across multiple hosts efficiently and consistently.

## Variables (group_vars, host_vars, role vars)

Variables in Ansible allow you to make your playbooks dynamic and reusable. Instead of hardcoding values, you can define variables at different scopes and reference them throughout your automation. 
This makes your playbooks flexible, maintainable, and adaptable to different environments.

**Group Variables and Host Variables:**

![Variables Organization and Playbook Usage](img/Ansible12.png)

The screenshot shows three types of variable organization. The `group_vars/webservers.yml` file contains variables that apply to all servers in the webservers group (like `nginx_port: 80`, `nginx_user: nginx`, `app_name: MyWebApp`). 
Meanwhile, `host_vars/web1.yml` and `host_vars/web2.yml` contain host-specific variables that override or supplement group variables (like `app_name: MyWebApp 1` for web1 and `app_name: MyWebApp 2` for web2). 
The `deploy-with-variables.yml` playbook demonstrates how to load and use these variables through the `vars_files` directive.

**Variable Consumption in Action:**

![Variables Execution Results](img/Ansible13.png)

When the playbook executes, it creates a dynamic HTML page using variables like `{{ app_name }}`, `{{ app_version }}`, and `{{ nginx_port }}`. 
The curl commands show the results on both servers: web1 displays "MyWebApp 1" while web2 displays "MyWebApp 2", proving that host-specific variables override group variables. 
This demonstrates how Ansible intelligently merges variables at different scopes to create host-specific configurations from a single playbook.

### Understanding group_vars vs host_vars

**group_vars** - Apply to all hosts in a specific group defined in your inventory. Use these for common configuration that should be shared across multiple servers (like web server port, package names, or service names). 
They reduce duplication and ensure consistency across your infrastructure.

**host_vars** - Apply to individual hosts only. Use these for host-specific customizations like unique IP addresses, specific application configurations, or server-specific settings that differ from the group defaults. 
Host variables take precedence over group variables when there's a naming conflict.

In essence: group_vars provide the baseline configuration, while host_vars allow fine-tuning for specific servers. This hierarchical approach keeps your automation both standardized and flexible.

## Templates with Jinja2

Jinja2 is a templating language that allows you to create dynamic configuration files with variables, conditionals, and loops. Templates in Ansible enable you to generate customized files across multiple servers without manually editing each one, making infrastructure provisioning scalable and consistent.

**Role Structure for Templates:**

![Role Directory Structure](img/Ansible14.png)

This screenshot shows the standard Ansible role structure created for the `webserver` role. The role contains subdirectories including `handlers/` (for service restart logic), `tasks/` (main task definitions), `templates/` (Jinja2 template files), and `vars/` (role-specific variables). This organized structure makes roles reusable and self-contained.

**Complete Project Structure with Roles:**

![Project Tree with Role Organization](img/Ansible15.png)

The full project hierarchy shows how the `webserver` role is integrated into the larger project. The role's `tasks/main.yml` contains the core tasks (Install Nginx, Start Nginx service), while the `deploy-with-roles.yml` playbook demonstrates how to call this role. This modular approach allows roles to be reused across different playbooks and even shared across projects.

**Executing Roles from Playbooks:**

![Role Execution Results](img/Ansible16.png)

When executing the `deploy-with-roles.yml` playbook, Ansible seamlessly runs all tasks defined within the `webserver` role on both host groups. The output shows "[webserver : Install Nginx]" and "[webserver : Start Nginx service]" tasks executing successfully on web1 and web2, demonstrating how roles encapsulate related tasks and make playbooks cleaner and more maintainable.

## Task Inclusion & Organization

As your Ansible projects grow, keeping all tasks in a single file becomes difficult to maintain. Task inclusion allows you to break down complex playbooks into smaller, focused task files that can be organized logically and reused across different playbooks. This follows the DRY (Don't Repeat Yourself) principle and makes your automation code more modular and maintainable.

**Organizing Tasks with Jinja2 Templates:**

![Task Inclusion and Template Organization](img/Ansible17.png)

The screenshot demonstrates how tasks are organized within a role. The `index.html.j2` Jinja2 template dynamically generates HTML content with variables like `{{ inventory_hostname }}`, `{{ app_name }}`, and `{{ app_version }}`. The `main.yml` file in the tasks directory uses `include_tasks: main_template.yml` to include task definitions from separate files. The `main_template.yml` contains the "Deploy index.html from template" task that copies the rendered template to the destination, separating template deployment logic from installation and service management tasks.

**Task Separation and Execution:**

![Task Execution Output](img/Ansible17.png)

When the `deploy-with-role-templates.yml` playbook executes, Ansible orchestrates the included tasks in sequence. The output shows multiple task phases: "[webserver : Install Nginx]", "[webserver : Start Nginx service]", "[webserver : Include_tasks]", and "[webserver : Deploy index.html from template]" all executing successfully on both web1 and web2. This demonstrates how separated task files are loaded and executed at runtime while maintaining a logical flow.

**Template Content Verification:**

![Curl Verification of Rendered Templates](img/Ansible18.png)

Using curl commands on both server IPs verifies that the Jinja2 templates were properly rendered with host-specific content. Web1 displays "Welcome to web1" with its corresponding server information, while web2 displays "Welcome to web2" with different server details. This confirms that the dynamic templates were correctly processed and deployed, with each server showing personalized content based on its inventory variables.
