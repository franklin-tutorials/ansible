# Ansible Role: WordPress Installer

This role installs and configures a WordPress instance on Debian/Ubuntu and RedHat/Rocky Linux systems. It sets up Apache as the web server and MariaDB as the database server.

## Requirements

-   Ansible 2.9 or higher.
-   Supported target OS:
    -   Debian (e.g., Buster, Bullseye)
    -   Ubuntu (e.g., Focal, Jammy)
    -   Rocky Linux (e.g., 8, 9)
    -   Other RHEL derivatives might work but are not explicitly tested.
-   Python `pymysql` (for Debian/Ubuntu) or `PyMySQL` (for RedHat/Rocky) library on the target host for Ansible's MySQL modules. The role attempts to install this.
-   `wget` and `unzip` must be available or installable on the target system.
-   Root or sudo privileges on the target host.

## Role Variables

Variables are defined in `defaults/main.yml`. You can override these in your playbook or inventory.

### WordPress Core Settings

-   `wp_version`: (Default: `"latest"`) WordPress version to install (e.g., "5.8.3", "latest").
-   `wp_install_path`: (Default: `"/var/www/html"`) The root directory where WordPress files will be installed. Apache's `DocumentRoot` will point here.
-   `wp_user`: (Default: `"www-data"` on Debian, automatically set to `"apache"` on RedHat) System user for WordPress files. This is typically determined by the OS family.
-   `wp_group`: (Default: `"www-data"` on Debian, automatically set to `"apache"` on RedHat) System group for WordPress files. Typically determined by the OS family.

### WordPress Database Settings

**Important:** Change default passwords for production environments!

-   `wp_db_name`: (Default: `"wordpress"`) Name of the WordPress database.
-   `wp_db_user`: (Default: `"example"`) WordPress database username.
-   `wp_db_password`: (Default: `"examplePW"`) Password for the WordPress database user. **CHANGE THIS!**
-   `wp_db_host`: (Default: `"localhost"`) Hostname for the MariaDB server.
-   `wp_db_root_password`: (Default: `"examplerootPW"`) Desired root password for MariaDB. **CHANGE THIS!** The role will attempt to set this on new MariaDB installations.
-   `wp_db_table_prefix`: (Default: `"wp_"`) Table prefix for WordPress tables.

### Webserver Settings (Apache)

-   `apache_server_admin`: (Default: `"admin@{{ ansible_fqdn | default('localhost') }}"`) Server admin email for Apache configuration.
-   `apache_port`: (Default: `"80"`) Port Apache will listen on for WordPress.
-   `apache_doc_root`: (Default: `{{ wp_install_path }}`) DocumentRoot for Apache. By default, it's the same as `wp_install_path`.

### PHP Settings (Placeholder - Not yet implemented in tasks)

These variables are defined but not actively used to configure `php.ini` in the current version of the role.
-   `php_memory_limit`: (Default: `"256M"`)
-   `php_max_execution_time`: (Default: `"30"`)
-   `php_upload_max_filesize`: (Default: `"32M"`)

### Package and Service Names

These are mostly for internal use and determined based on OS family. They can be overridden if necessary for unusual setups.
-   `apache_package_name_debian`, `apache_package_name_redhat`
-   `php_package_names_debian`, `php_package_names_redhat`
-   `mariadb_server_package_name_debian`, `mariadb_server_package_name_redhat`
-   `mariadb_client_package_name_debian`, `mariadb_client_package_name_redhat`
-   `apache_service_name_debian`, `apache_service_name_redhat`
-   `mariadb_service_name` (Default: "mariadb")

### Temporary Paths

-   `wp_tmp_download_dir`: (Default: `"/tmp"`) Temporary directory for downloading WordPress.
-   `wp_archive_path`: (Default: `{{ wp_tmp_download_dir }}/wordpress-{{ wp_version }}.zip`) Full path to the downloaded WordPress archive.
-   `wp_extracted_path`: (Default: `{{ wp_tmp_download_dir }}/wordpress`) Path where the archive is extracted.

### WordPress Salts

The role attempts to fetch unique salts from `https://api.wordpress.org/secret-key/1.1/salt/`. If this fails, or if you prefer to define them manually, you can set the following variables. It is **highly recommended** to use unique salts. The defaults are placeholders and insecure.

-   `wp_auth_key`
-   `wp_secure_auth_key`
-   `wp_logged_in_key`
-   `wp_nonce_key`
-   `wp_auth_salt`
-   `wp_secure_auth_salt`
-   `wp_logged_in_salt`
-   `wp_nonce_salt`

(All default to `"put your unique phrase here"`)

### MySQL/MariaDB Python Dependency

-   `python_mysql_dependency`: (Default: `"python3-pymysql"` for Debian, `"python3-PyMySQL"` for RedHat) The Python library needed by Ansible's MySQL modules. The role tries to install this.

## Dependencies

None.

## Example Playbook

```yaml
---
- hosts: webservers
  become: yes
  roles:
    - role: ansible-wordpress # Or your custom role name if you rename the directory
  vars:
    # Override default variables here if needed
    wp_db_password: "MySecurePassword$123"
    wp_db_root_password: "MySecureRootPassword#456"
    # It's strongly recommended to set your own unique salts if API fetching is disabled or fails
    # wp_auth_key: "your_unique_auth_key_here"
    # wp_secure_auth_key: "your_unique_secure_auth_key_here"
    # ... and so on for all 8 salts
```

## Execution

1.  Ensure your Ansible control node can connect to the target hosts.
2.  Create a playbook (e.g., `wordpress_playbook.yml`) like the example above.
3.  Adjust variables as needed, especially passwords and salts.
4.  Run the playbook:
    ```bash
    ansible-playbook wordpress_playbook.yml
    ```

## Idempotency

The role is designed to be idempotent. Running it multiple times on the same host should not cause unintended changes, and should result in the same configured state.
- Package installation uses `state: present`.
- Service management ensures services are started and enabled.
- File templating only changes files if the content differs.
- Database and user creation tasks check for existence before acting.
- WordPress download/extraction is skipped if WordPress appears to be already installed.

## Notes

-   **Security:** The default passwords provided in `defaults/main.yml` are insecure and **must** be changed for any production or publicly accessible environment. Similarly, ensure WordPress salts are unique.
-   **DocumentRoot:** The `wp_install_path` variable defines where WordPress core files (index.php, wp-admin, etc.) are placed. The `apache_doc_root` variable (which defaults to `wp_install_path`) tells Apache where to find these files. Ensure these align with your desired URL structure.
-   **PHP Configuration:** Advanced PHP settings (like `memory_limit`, `upload_max_filesize`) are defined as variables but not yet implemented in tasks to modify `php.ini`. This can be added as a future enhancement.
-   **MariaDB Root Password:** The task to set the MariaDB root password works best on fresh installations where the root user has no password or can be accessed via a Unix socket without a password.

## License

Specify your preferred license (e.g., MIT, GPLv2, Apache 2.0). For example:

MIT

## Author Information

This role was created by [Your Name/Organization].
Based on an initial shell script.
Assisted by AI.
```
