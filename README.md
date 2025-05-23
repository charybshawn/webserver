# Laravel Development Environment

This is an Ansible-based Laravel development environment setup tool. It automates the process of setting up a LEMP stack (Linux, Nginx, MySQL, PostgreSQL, PHP) optimized for Laravel development.

## Features

- Complete LEMP stack installation (Linux, Nginx, MySQL, PostgreSQL, PHP)
- Multiple PHP version support (8.1, 8.2, 8.3)
- Node.js LTS installation (configurable version)
- Composer installation
- Multiple Laravel site management
- Git repository integration
- Custom port configuration for sites
- Database setup and configuration (MySQL and PostgreSQL)
- Adminer database management web interface
- Easy-to-use shell scripts for common tasks
- Rollback functionality for clean uninstallation
- Smart PHP version detection and compatibility checking
- Automatic database system selection and configuration
- Enhanced error handling and validation

## Prerequisites

- Ubuntu/Debian-based Linux system
- sudo access
- Basic knowledge of Linux command line

## Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/laravel-dev-environment.git
   cd laravel-dev-environment
   ```

2. Run the installation script:
   ```bash
   sudo ./install.sh
   ```

   Additional options:
   ```bash
   sudo ./install.sh -v              # Run with verbose output
   sudo ./install.sh -r              # Run rollback first, then install
   sudo ./install.sh -v -r           # Run rollback first with verbose output
   sudo ./install.sh --help          # Show all available options
   ```

This will install all necessary dependencies and configure the server for Laravel development.

## Uninstallation

To completely remove the Laravel development environment and all its components:

```bash
sudo ./rollback.sh
```

This will:
- Remove all installed packages
- Delete configuration files
- Remove created directories
- Clean up system settings

## Setting Up a Laravel Site

### Using the setup-site.sh Script

The simplest way to create a new Laravel site is using the provided `setup-site.sh` script:

```bash
sudo ./setup-site.sh
```

The script will guide you through an interactive process to:
- Enter site name
- Configure domain name (defaults to sitename.local)
- Set port number (defaults to 80)
- Choose PHP version
- Optionally set up from a Git repository
- Configure automatic updates

This will:
- Create a new site directory at `/var/www/sitename`
- Install a fresh Laravel project or clone from Git
- Create a database
- Configure Nginx
- Set up proper permissions

### Setting Up a Site with Custom Port

When running the script, you'll be prompted for the port number. Simply enter your desired port (e.g., 8080) when asked.

### Cloning an Existing Laravel Project

When running the script, you'll be prompted for a Git repository URL. Enter the full URL when asked:

```
Enter Git repository URL (leave empty for new Laravel project): https://github.com/username/laravel-project.git
```

The script will then:
- Ask for the branch name (defaults to 'main')
- Check PHP version requirements from composer.json
- Validate PHP version compatibility
- Offer to switch to a compatible PHP version if needed
- Handle database configuration automatically
- Set up proper permissions and environment

### Working with GitHub Repositories

For GitHub repositories, you have two options:

1. **Public Repositories**: The script will detect public repositories and attempt to clone them anonymously.

2. **Private Repositories**: For private repositories, use SSH URLs instead of HTTPS:
   ```
   Enter Git repository URL: git@github.com:username/private-repo.git
   ```

   Make sure your SSH keys are properly set up on the server with access to the repository.

### Automatic Updates via Cron

When setting up a Git-based project, you'll be asked if you want to enable automatic updates:

```
Enable automatic updates via cron? (yes/no) [no]:
```

When auto-update is enabled:
- A cron job will be created to pull the latest changes every 6 hours
- The update script will handle stashing local changes, pulling updates, running Composer and npm tasks, and running migrations
- All update activities are logged to `/var/log/sitename-updates.log`
- The update script is created at `/usr/local/bin/update-sitename.sh` and can be manually executed at any time

This feature is especially useful for development environments that need to stay in sync with a shared repository.

## Managing Multiple Sites

For managing multiple Laravel sites, you can edit the `playbooks/manage_laravel_sites.yml` file to define your sites:

```yml
vars:
  laravel_sites:
    - name: site1
      domain: site1.local
      port: 80
      db_connection: mysql
      db_database: site1_db
      php_version: 8.1
      
    - name: site2
      domain: site2.local
      port: 8080
      git_repo: git@github.com:username/site2.git
      git_branch: develop
      db_connection: pgsql
      php_version: 8.2
      run_migrations: true
      auto_update: yes
```

Then run:

```bash
ansible-playbook playbooks/manage_laravel_sites.yml
```

Each site can have its own:
- PHP version
- Database system (MySQL or PostgreSQL)
- Git repository and branch
- Port configuration
- Auto-update settings

## Directory Structure

- `playbooks/` - Ansible playbooks
- `roles/` - Ansible roles
- `templates/` - Configuration templates
- `install.sh` - Main installation script
- `rollback.sh` - Uninstallation script
- `setup-site.sh` - Site creation script

## Customization

You can customize various aspects of the environment:

- Edit `playbooks/setup_laravel_server.yml` to modify server configuration
- Modify templates in the `templates/` directory
- Edit role defaults in `roles/*/defaults/main.yml`

## Troubleshooting

Common issues:

1. **Permission problems**: Run the installation script with sudo
2. **Domain not accessible**: Add the domain to your local hosts file
3. **Database connection issues**: Check your .env file configuration
4. **Port conflicts**: Ensure the port you want to use isn't already in use by another service
5. **Git authentication failures**: For private GitHub repositories, use SSH URLs (git@github.com:username/repo.git) instead of HTTPS URLs
6. **PHP version conflicts**: The script will detect and warn about PHP version incompatibilities
7. **Database system issues**: Make sure the selected database system is installed and running

## Database Management

The environment comes with [Adminer](https://www.adminer.org/), a lightweight database management tool that supports both MySQL and PostgreSQL.

### Accessing Adminer

Once your Laravel environment is set up, you can access Adminer at:

```
http://db.hostname.local/
```

(where hostname is your server's hostname)

Default credentials:
- Username: admin
- Password: admin (or the one you provided during setup)

With Adminer you can:
- Create, modify, and delete databases and tables
- Run SQL queries
- Import and export data
- Manage users and permissions
- View database structure

### Customizing Adminer

You can customize Adminer by modifying the settings in `playbooks/setup_laravel_server.yml`. Options include:
- Changing the authentication credentials
- Restricting access by IP address
- Enabling/disabling specific features
- Changing the theme