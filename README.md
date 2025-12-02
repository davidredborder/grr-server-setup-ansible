# GRR Server Setup with Ansible

This repository provides an Ansible playbook to automate the setup and configuration of a GRR (Google Rapid Response) server on Debian-based systems. The playbook handles dependencies, MySQL configuration, GRR and Fleetspeak installation, database setup, and initial user creation.

## Features

*   **Automated Installation:** Installs GRR server and its dependencies.
*   **MySQL Configuration:** Sets up MySQL with appropriate settings for GRR and Fleetspeak databases.
*   **Database Creation:** Creates GRR and Fleetspeak databases and users.
*   **GRR Server Configuration:** Configures the GRR server with provided settings.
*   **Admin User Creation:** Creates an initial GRR administrator user.
*   **Fleetspeak Setup:** Configures Fleetspeak for client communication.
*   **Client Installer Generation:** Generates client binaries for distribution.

## Prerequisites

Before running this playbook, ensure you have:

*   **Ansible:** Installed on your control machine.
*   **Target Machine:** A Debian-based system (e.g., Ubuntu, Debian) to act as the GRR server.
*   **SSH Access:** Configured for the target machine from your control machine.
*   **Sudo Privileges:** A user with `sudo` privileges on the target machine.

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/davidredborder/grr-server-setup-ansible.git
cd grr-server-setup-ansible
```

### 2. Configure Variables

The playbook uses variables defined in `vars/grr_config_vars.yml` to customize the installation. **It is crucial to review and update these variables according to your environment and security requirements.**

Edit the `vars/grr_config_vars.yml` file:

```yaml
# vars/grr_config_vars.yml

# --- GRR PACKAGE DETAILS ---
grr_version: 3.4.7-1
grr_deb_url: "https://storage.googleapis.com/releases.grr-response.com/grr-server_{{ grr_version }}_amd64.deb"

# --- MYSQL CONFIGURATION ---
mysql_max_allowed_packet: 40M
mysql_log_bin_trust_function_creators: 1

# --- GRR & FLEETSPEAK DATABASE CREDENTIALS ---
# CHANGE THESE FOR PRODUCTION!
grr_database_user: grr
grr_database_password: your_grr_db_password_here # <-- IMPORTANT: Change this!
grr_database_name: grr

fleetspeak_database_user: fleetspeak
fleetspeak_database_password: your_fs_db_password_here # <-- IMPORTANT: Change this!
fleetspeak_database_name: fleetspeak

# --- GRR SERVER HOSTNAME ---
grr_server_hostname: grr-server.example.com # <-- IMPORTANT: Change this to your server's FQDN or IP
grr_adminui_port: 8000
grr_frontend_port: 8080

# --- GRR ADMIN USER CREATION ---
# CHANGE THESE FOR PRODUCTION!
grr_admin_username: admin
grr_admin_password: your_admin_password_here # <-- IMPORTANT: Change this!
```

**Key variables to change:**
*   `grr_database_password`: Set a strong password for the GRR database user.
*   `fleetspeak_database_password`: Set a strong password for the Fleetspeak database user.
*   `grr_server_hostname`: Set this to the Fully Qualified Domain Name (FQDN) or IP address of your GRR server.
*   `grr_admin_password`: Set a strong password for the GRR admin user.

### 3. Configure Inventory

Update the `inventory` file to point to your GRR server(s).

```ini
# inventory
[grr_servers]
your_grr_server_ip_or_hostname ansible_user=your_ssh_username
```

Replace `your_grr_server_ip_or_hostname` with the actual IP address or hostname of your server, and `your_ssh_username` with the SSH user you use to connect to the server.

### 4. Run the Playbook

Execute the playbook from your control machine:

```bash
ansible-playbook -i inventory grr_server_setup.yml --ask-pass --ask-become-pass
```

*   `--ask-pass`: Prompts for the SSH password of `your_ssh_username` on the target machine. Omit if you are using SSH keys without a passphrase.
*   `--ask-become-pass`: Prompts for the `sudo` password on the target machine.

### 5. Access GRR Admin UI

Once the playbook completes successfully, you should be able to access the GRR Admin UI in your web browser at:

`http://<your_grr_server_hostname>:8000`

Use the `grr_admin_username` and `grr_admin_password` you configured in `vars/grr_config_vars.yml` to log in.

## Directory Structure

*   `grr_server_setup.yml`: The main Ansible playbook.
*   `inventory`: Defines the target hosts for the playbook.
*   `README.md`: This file.
*   `templates/`: Contains Jinja2 templates for GRR and Fleetspeak configurations.
    *   `fleetspeak_config.j2`
    *   `grr_server_config.j2`
*   `vars/`: Contains variable files.
    *   `grr_config_vars.yml`: Customizable variables for the GRR setup.