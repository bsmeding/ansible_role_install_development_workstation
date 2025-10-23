# Development Workstation Ansible Playbook

An Ansible playbook to set up a development workstation on both **macOS** and **Ubuntu** systems with comprehensive development tools and packages.

Inspired by [Jeff Geerling's mac-dev-playbook](https://github.com/geerlingguy/mac-dev-playbook). But as I use a Mac AND Ubuntu for development I want to have same setup on both machines. This Ansilbe Role will install and setup those systems almost the same.

## Features

✅ **Cross-Platform**: Works on macOS (Intel & Apple Silicon) and Ubuntu  
✅ **Comprehensive**: Installs essential development tools, languages, and utilities  
✅ **Modular**: OS-specific tasks separated for easy maintenance  
✅ **Customizable**: Easy to add/remove packages via configuration files  
✅ **Automated**: Minimal manual intervention required  

## What Gets Installed

### macOS
- **Package Manager**: Homebrew (auto-installed)
- **Languages**: Python, Node.js, Go, Ruby
- **Cloud Tools**: OpenTofu, AWS CLI, Azure CLI
- **Containers**: Docker Desktop, Docker Compose, Kubernetes CLI, Containerlab
- **Databases**: PostgreSQL, MySQL clients
- **Editors**: VSCode (with extensions), Cursor AI, Vim, Nano, Neovim, Emacs
- **Terminal Emulators**: iTerm2 (with SSH handler), Ghostty
- **Utilities**: git, tmux, jq, fzf, ripgrep, htop, and many more
- **Applications**: Postman, GitHub Desktop, Slack, Chrome, Firefox
- **Dock Management**: Automatically configure macOS Dock with your preferred apps

> **Note:** Some apps (OneDrive, TeamViewer) require manual installation due to system-level permissions. These are commented out in defaults but can be installed afterward with `brew install --cask <app>`.

### Ubuntu
- **Languages**: Python 3, Node.js 20.x
- **Containers**: Docker CE, Docker Compose, Containerlab
- **Cloud Storage**: rclone (supports OneDrive, Google Drive, Dropbox, S3, and 40+ providers)
- **Databases**: PostgreSQL, MySQL clients
- **Build Tools**: gcc, g++, make, cmake, build-essential
- **Editors**: VSCode (with extensions), Vim, Nano, Neovim, Emacs
- **Terminal Emulators**: Terminator, Tilix
- **Utilities**: git, tmux, jq, ripgrep, bat, htop, and many more

### Common (Both Platforms)
- **Python Packages**: ansible, boto3, pytest, black, flake8, pylint, and more
- **Editor Extensions**: Installed to both VSCode and Cursor
  - Python, Black Formatter, Docker, Remote SSH, Kubernetes, GitLens, Prettier, ESLint, and more
- **Shell**: zsh configured as default with custom configuration
- **Shell Aliases**: 60+ productivity aliases for git, docker, kubernetes, and more
- **Ansible Configuration**: User-level ansible.cfg with best practices
- **/etc/hosts Management**: Custom host entries for development
- **Customizable**: Override any setting via variables in your playbook

## Installation

### Prerequisites

#### macOS
```bash
# 1. Install Xcode Command Line Tools
xcode-select --install

# 2. Install Ansible
pip3 install ansible
```

#### Ubuntu
```bash
# Install Ansible
sudo apt update
sudo apt install ansible python3-pip
```

### Install the Role

**Option 1: Install from Ansible Galaxy** (Recommended)
```bash
ansible-galaxy role install bsmeding.install_development_workstation
```

**Option 2: Install from GitHub**
```bash
ansible-galaxy role install git+https://github.com/bsmeding/ansible_role_install_development_workstation.git
```

### Create Your Playbook

Create a playbook file (e.g., `setup.yml`):

```yaml
---
- name: Setup Development Workstation
  hosts: localhost
  connection: local
  gather_facts: yes
  
  vars:
    # Customize your settings here
    preferred_editor: "nano"
    manage_custom_hosts: true
    custom_hosts_entries:
      - { ip: "127.0.0.1", hostname: "local.dev", comment: "Local dev" }
  
  roles:
    - bsmeding.install_development_workstation
```

### Run the Playbook

```bash
ansible-playbook setup.yml -K
```

The `-K` flag prompts for your sudo password (needed for system-level installations).

### First Run Tips

- On **macOS**, the first run will install Homebrew, which requires sudo access
- On **Ubuntu**, Docker installation adds your user to the docker group (requires logout/login)
- The playbook takes 15-30 minutes depending on your internet speed
- Some tasks may require manual approval (macOS security prompts)

## Customization

> **⚠️ IMPORTANT: Never edit role files directly!**  
> When you install this role via `ansible-galaxy`, always override variables in your playbook, `host_vars`, or `group_vars`.  
> This keeps the role intact and your customizations separate.

### Method 1: Variables in Your Playbook

The simplest way - define variables directly in your playbook:

```yaml
---
- name: Setup Development Workstation
  hosts: localhost
  connection: local
  gather_facts: yes
  
  vars:
    preferred_editor: "vim"
    manage_custom_hosts: true
    
    common_tools:
      pip:
        - ansible
        - boto3
        - your-custom-package
      
      vscode_extensions:
        - ms-python.python
        - your-custom-extension
  
  roles:
    - bsmeding.install_development_workstation
```

### Method 2: Using host_vars

Create `host_vars/localhost.yml`:

```yaml
---
# Override settings for localhost
preferred_editor: "nano"

custom_hosts_entries:
  - { ip: "192.168.1.100", hostname: "dev.server", comment: "Dev server" }

common_tools:
  pip:
    - ansible
    - boto3
    - my-custom-package
```

### Method 3: Using group_vars

Create `group_vars/all.yml` for settings that apply to all hosts:

```yaml
---
# Global settings
preferred_editor: "vim"

common_tools:
  git_config:
    - { name: "user.name", value: "Your Name" }
    - { name: "user.email", value: "you@example.com" }
    - { name: "core.editor", value: "{{ preferred_editor }}" }
```

### Choosing Your Preferred Editor

The playbook installs multiple text editors (vim, nano, neovim) and allows you to choose your preferred default editor.

**Set your preferred editor:**
```yaml
# In defaults/main.yml
preferred_editor: "nano"  # Options: vim, nano, nvim, emacs, code, cursor
```

This will configure:
- `EDITOR` environment variable in your shell
- `VISUAL` environment variable in your shell
- Git's `core.editor` configuration
- All shell profiles (.zshrc, .bashrc)

**Available editors:**
- `nano` - Simple, user-friendly (default)
- `vim` - Powerful modal editor
- `nvim` - Modern Neovim
- `code` - VSCode
- `cursor` - Cursor AI editor
- `emacs` - Extensible editor

### Terminal Emulators

**macOS:**
- **iTerm2**: Full-featured terminal with advanced features
  - Automatically set as default SSH:// URL handler
  - Pre-configured development profile included
  - Split panes, search, autocomplete
- **Ghostty**: Modern, GPU-accelerated terminal
  - Pre-configured with sensible defaults
  - Config file deployed to `~/.config/ghostty/config`
  - Shell integration, clipboard support, keybindings

**Ubuntu:**
- **Terminator**: Feature-rich terminal with split panes
- **Tilix**: Tiling terminal emulator
- **GNOME Terminal**: Default terminal (pre-installed)
- **Ghostty**: (If installed manually) Config will be deployed automatically

> **Note for Ubuntu:** Ghostty is not in standard Ubuntu repositories. If you install it manually, the role will automatically deploy the configuration file.

### Editor Extensions (VSCode & Cursor)

Extensions are automatically installed to **both** VSCode and Cursor. This means:
- ✅ Same extensions in both editors
- ✅ No manual installation needed
- ✅ Easy to maintain one extension list

**To customize extensions**, edit `defaults/main.yml`:
```yaml
vscode_extensions:
  - ms-python.python
  - ms-python.vscode-pylance
  - eamodio.gitlens
  # Add your extensions here
```

Both `code --install-extension` and `cursor --install-extension` commands are used, so extensions work in both editors seamlessly.

### Shell Aliases

The playbook configures 60+ productivity-boosting aliases in your shell:

**Git Shortcuts:**
```bash
gs      # git status
ga      # git add
gaa     # git add --all
gc      # git commit
gcm     # git commit -m
gp      # git push
gpl     # git pull
gb      # git branch
gco     # git checkout
gcb     # git checkout -b (new branch)
gd      # git diff
gl      # git log (graph)
glog    # git log --all (graph)
gst     # git stash
gstp    # git stash pop
```

**Docker Shortcuts:**
```bash
d       # docker
dc      # docker compose
dps     # docker ps
dpsa    # docker ps -a
di      # docker images
dex     # docker exec -it
dlogs   # docker logs -f
dprune  # docker system prune -a
```

**Kubernetes Shortcuts:**
```bash
k       # kubectl
kgp     # kubectl get pods
kgs     # kubectl get services
kgd     # kubectl get deployments
klogs   # kubectl logs -f
kexec   # kubectl exec -it
```

**Ansible Shortcuts:**
```bash
ap      # ansible-playbook
av      # ansible-vault
ag      # ansible-galaxy
```

**System Shortcuts:**
```bash
ll      # ls -lh (list detailed)
la      # ls -lah (list all)
..      # cd ..
...     # cd ../..
c       # clear
reload  # restart shell
myip    # show public IP
```

All aliases are defined in the shell templates and can be customized by overriding the templates in your playbook.

### macOS Dock Configuration

The playbook can automatically configure your macOS Dock, removing unwanted apps and adding your preferred development tools.

**Enable/Disable:**
```yaml
configure_dock: true  # Set to false to skip Dock configuration
```

**Remove Apps from Dock:**
```yaml
dockitems_remove:
  - Launchpad
  - TV
  - Music
  - Maps
  # Add more apps to remove
```

**Add Apps to Dock:**
```yaml
dockitems_persist:
  - name: "Google Chrome"
    path: "/Applications/Google Chrome.app/"
    pos: 1
  - name: "iTerm"
    path: "/Applications/iTerm.app/"
    pos: 2
  - name: "Visual Studio Code"
    path: "/Applications/Visual Studio Code.app/"
    pos: 3
  # Add your apps here
```

**How it works:**
- Uses `dockutil` (installed via Homebrew) to manage Dock items
- Removes unwanted default macOS apps
- Adds your development tools in your preferred order
- Automatically restarts the Dock to apply changes

### Managing /etc/hosts Entries

The playbook can automatically manage custom host entries in `/etc/hosts` for your development environment.

**Enable/Disable:**
```yaml
# In defaults/main.yml
manage_custom_hosts: true  # Set to false to disable
```

**Add Custom Hosts:**
```yaml
# In defaults/main.yml
custom_hosts_entries:
  - { ip: "127.0.0.1", hostname: "local.dev", comment: "Local development" }
  - { ip: "127.0.0.1", hostname: "app.local", comment: "Local app" }
  - { ip: "192.168.1.100", hostname: "dev.example.com", comment: "Development server" }
  - { ip: "10.0.0.50", hostname: "staging.example.com", comment: "Staging server" }
```

**Example /etc/hosts entries created:**
```
127.0.0.1	local.dev	# Local development
127.0.0.1	app.local	# Local app
192.168.1.100	dev.example.com	# Development server
10.0.0.50	staging.example.com	# Staging server
```

**Notes:**
- Original `/etc/hosts` is backed up to `/etc/hosts.backup`
- Each change creates an automatic backup with timestamp
- Entries are managed idempotently (won't duplicate)

### Example: Adding a Package

#### macOS (Homebrew)
Add to `vars/mac.yml`:
```yaml
brew_packages:
  - git
  - your-new-package  # Add here

brew_cask_packages:
  - visual-studio-code
  - your-new-app  # Add GUI apps here
```

#### Ubuntu (APT)
Add to `vars/debian.yml`:
```yaml
debian_packages:
  - git
  - your-new-package  # Add here
```

## Advanced Usage

### Testing Without Changes
```bash
# Dry run to see what would change
ansible-playbook main.yml --check -K
```

### Running Specific Tasks
```bash
# Only install packages (skip configuration)
ansible-playbook main.yml -K --tags packages

# Only configure shell
ansible-playbook main.yml -K --tags shell
```

### Verbose Output
```bash
# Debug mode
ansible-playbook main.yml -K -vvv
```

## Troubleshooting

### macOS Issues

**Homebrew installation fails with "Need sudo access":**
```bash
# Ensure you're using the -K flag
ansible-playbook main.yml -K

# Or manually install Homebrew first:
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**"command not found: brew" after installation:**
```bash
# Restart your terminal or run:
exec zsh

# Add to your PATH manually:
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

**VSCode extensions fail to install:**
```bash
# Install VSCode first, then run:
code --version

# Re-run the playbook
ansible-playbook main.yml -K
```

### Ubuntu Issues

**Docker permission denied:**
```bash
# Log out and back in to apply docker group membership
# Or run:
newgrp docker

# Test Docker:
docker run hello-world
```

**Node.js not found:**
```bash
# Verify Node.js installation
node --version
npm --version

# If missing, manually add NodeSource repository:
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## Post-Installation

### macOS
1. **Restart terminal** to load new shell configuration
2. **Open Docker Desktop** and complete initial setup
3. **Verify installations**:
   ```bash
   brew --version
   git --version
   python3 --version
   node --version
   docker --version
   ```

### Ubuntu
1. **Log out and back in** (for Docker group to take effect)
2. **Restart terminal**: `exec zsh`
3. **Verify installations**:
   ```bash
   git --version
   python3 --version
   node --version
   docker --version
   code --version
   ```

## Project Structure

```
mac_install_role/
├── README.md                         # This file
├── QUICKSTART.md                     # Quick reference guide
├── main.yml                          # Main playbook
├── requirements.yml                  # Ansible Galaxy dependencies
├── ansible.cfg                       # Ansible configuration
├── inventory                         # Inventory file
└── roles/
    └── preflight_dev_workstation/
        ├── README.md                 # Role documentation
        ├── defaults/main.yml         # Default variables
        ├── vars/
        │   ├── mac.yml              # macOS packages
        │   └── debian.yml           # Ubuntu packages
        ├── tasks/
        │   ├── main.yml             # Main entry point
        │   ├── osx/main.yml         # macOS-specific tasks
        │   └── ubuntu/main.yml      # Ubuntu-specific tasks
        └── templates/
            ├── bashrc.j2            # Bash configuration template
            └── zshrc.j2             # Zsh configuration template
```

## Contributing

Feel free to fork and customize for your needs! Some ideas:
- Add more languages (Rust, Java, PHP, etc.)
- Add IDE configurations (IntelliJ, PyCharm)
- Add more development tools
- Improve shell configurations

## Contributing

Found a bug or have a feature request? Please open an issue on [GitHub](https://github.com/bsmeding/ansible_role_install_development_workstation/issues).

## References

- [Jeff Geerling's mac-dev-playbook](https://github.com/geerlingguy/mac-dev-playbook) - Excellent macOS setup playbook
- [Ansible Documentation](https://docs.ansible.com/)
- [Homebrew](https://brew.sh/) - macOS package manager

## Links

- **GitHub Repository:** https://github.com/bsmeding/ansible_role_install_development_workstation
- **Ansible Galaxy:** https://galaxy.ansible.com/bsmeding/install_development_workstation
- **Issue Tracker:** https://github.com/bsmeding/ansible_role_install_development_workstation/issues

## License

MIT

## Author

**Bart Smeding** ([@bsmeding](https://github.com/bsmeding))

Based on best practices from the Ansible and DevOps community.

