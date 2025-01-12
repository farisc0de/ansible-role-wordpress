# WordPress Pre-Deployment Role

This Ansible role prepares a server environment for WordPress deployment. It sets up the necessary directory structure, installs WP-CLI, and configures WordPress with a `wp-config.php` template.

---

## Features

- **Directory Setup**: Creates the required WordPress directory structure:
  - `wp-content/plugins`
  - `wp-content/themes`
  - `wp-content/uploads`
  - `logs` (optional)
  - `tmp` (optional)
- **WP-CLI Installation**: Installs [WP-CLI](https://wp-cli.org/) and its tab completion script.
- **Configuration Management**: Deploys a `wp-config.php` template with database credentials.
- **Rollback Support**: Cleans up in case of deployment failure.
- **Task Tags**: Includes tags for selective task execution (`setup`, `config`, `wordpress`, `rollback`).

---

## Role Variables

The role uses the following variables, with defaults provided where applicable:

| Variable              | Default Value        | Description                               |
| --------------------- | -------------------- | ----------------------------------------- |
| `wordpress_base_path` | `/var/www/wordpress` | Base path for the WordPress installation. |
| `deploy_user`         | `www-data`           | User who owns the WordPress files.        |
| `deploy_group`        | `www-data`           | Group for WordPress file ownership.       |
| `wordpress_debug`     | `false`              | Enables or disables WordPress debug mode. |
| `cleanup_tmp`         | `true`               | Cleans the `tmp` directory.               |
| `rollback`            | `false`              | Triggers rollback if set to `true`.       |

### Prompted Variables

During runtime, the role prompts for the following database credentials:

- **`db_name`**: WordPress database name.
- **`db_user`**: WordPress database username.
- **`db_password`**: WordPress database password.

---

## Tags

You can use tags to execute specific parts of the role:

| Tag         | Description                                           |
| ----------- | ----------------------------------------------------- |
| `setup`     | Prepares the directory structure and installs WP-CLI. |
| `config`    | Configures WordPress (e.g., `wp-config.php`).         |
| `wordpress` | Installs WP-CLI and its tab completions.              |
| `rollback`  | Cleans up the WordPress environment.                  |

---

## Example Playbook

```yaml
---
- name: Prepare WordPress environment
  hosts: wordpress_servers
  become: true
  vars_prompt:
    - name: "db_name"
      prompt: "Enter the WordPress database name"
      private: no
    - name: "db_user"
      prompt: "Enter the WordPress database username"
      private: no
    - name: "db_password"
      prompt: "Enter the WordPress database password"
      private: yes
  roles:
    - wordpress_folder_setup
```

---

## Usage

### 1. Run Full Setup

Execute all tasks to prepare the WordPress environment:

```bash
ansible-playbook wordpress_pre_deploy.yml
```

### 2. Selective Task Execution

Run specific tasks using tags:

- **Setup Tasks (e.g., directory creation and WP-CLI installation):**
  ```bash
  ansible-playbook wordpress_pre_deploy.yml --tags setup
  ```
- **Configure WordPress (e.g., `wp-config.php` deployment):**
  ```bash
  ansible-playbook wordpress_pre_deploy.yml --tags config
  ```
- **Install WP-CLI Only:**
  ```bash
  ansible-playbook wordpress_pre_deploy.yml --tags wordpress
  ```

### 3. Rollback Environment

Clean up the WordPress environment in case of deployment failure:

```bash
ansible-playbook wordpress_pre_deploy.yml --extra-vars "rollback=true" --tags rollback
```

---

## Dependencies

None.

---

## Directory Structure

The role creates the following directory structure under `{{ wordpress_base_path }}`:

```
/var/www/wordpress/
├── wp-content/
│   ├── plugins/
│   ├── themes/
│   └── uploads/
├── logs/            # Optional
├── tmp/             # Optional
├── wp-config.php
├── .htaccess        # Optional
```

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## Author

**Faris Alotaibi**  
DevOps Engineer | [GitHub](https://www.itsfaris.com)
