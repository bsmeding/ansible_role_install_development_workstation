# Quick Start Guide

## Prerequisites

### macOS
```bash
# Install Ansible
pip3 install ansible

# Note: The playbook will automatically install Homebrew if not present
# This requires sudo access (you'll be prompted for your password)
```

### Ubuntu
```bash
# Install Ansible
sudo apt update
sudo apt install ansible
```

## Running the Playbook

### Local Installation
```bash
# Run on your local machine
# On macOS: Will prompt for sudo password to install Homebrew
ansible-playbook main.yml --ask-become-pass

# Or use shorter form (recommended)
ansible-playbook main.yml -K

# Run with verbose output
ansible-playbook main.yml -K -v

# Run with extra verbose output (for debugging)
ansible-playbook main.yml -K -vvv
```

### Testing the Playbook (Dry Run)
```bash
# Check what would be installed without making changes
ansible-playbook main.yml --check -K
```

## Installed Packages

### macOS (via Homebrew)

**Core Development Tools:**
- Git, Git LFS
- Python 3.12, Node.js, Go, Ruby
- Docker, Docker Compose, Kubernetes CLI, Containerlab
- OpenTofu
- AWS CLI, Azure CLI

**CLI Utilities:**
- curl, wget, jq, yq
- tmux, vim, neovim
- tree, htop, fzf
- ripgrep, bat, exa

**Applications (via Homebrew Cask):**
- Visual Studio Code
- Cursor (AI-powered code editor)
- iTerm2 (automatically set as SSH:// handler)
- Ghostty (modern GPU-accelerated terminal)
- Docker Desktop
- Postman
- Slack
- Google Chrome, Firefox

### Ubuntu (via APT)

**Core Development Tools:**
- Git, Git LFS
- Python 3 (with pip, dev, venv)
- Node.js 20.x
- Docker CE, Docker Compose, Containerlab
- Build essentials (gcc, g++, make, cmake)

**Database Clients:**
- PostgreSQL client
- MySQL client

**CLI Utilities:**
- curl, wget, jq
- tmux, vim, neovim
- tree, htop, ncdu
- ripgrep, bat, silversearcher-ag

**Applications:**
- Visual Studio Code
- Terminator (terminal emulator)
- Tilix (tiling terminal emulator)

### Common (Both Platforms)

**Python Packages (via pip):**
- ansible, boto3, requests
- pytest, black, flake8, pylint
- virtualenv, pipenv
- docker

**Editor Extensions (VSCode & Cursor):**
- Python (with Pylance)
- Black Formatter
- Docker
- Kubernetes Tools
- GitLens
- Prettier
- ESLint
- Vim
- YAML
- Makefile Tools

> **Note:** Extensions are automatically installed to both VSCode and Cursor using the same extension list.

## Customization

### Change Your Default Editor

Edit `roles/preflight_dev_workstation/defaults/main.yml`:
```yaml
# Change from nano to your preferred editor
preferred_editor: "vim"  # Options: nano, vim, nvim, emacs, code, cursor
```

This sets the editor for:
- Shell environment (`$EDITOR`, `$VISUAL`)
- Git commits and operations
- All command-line tools

### Add More Packages

Edit the appropriate file:
- **macOS**: `roles/preflight_dev_workstation/vars/mac.yml`
- **Ubuntu**: `roles/preflight_dev_workstation/vars/debian.yml`
- **Common**: `roles/preflight_dev_workstation/defaults/main.yml`

### Skip Specific Tasks

Use tags (you can add these to tasks):
```bash
# Skip VSCode extensions
ansible-playbook main.yml --skip-tags vscode

# Only install packages
ansible-playbook main.yml --tags packages
```

## Troubleshooting

### macOS Issues

**Homebrew installation fails:**
```bash
# Manually install Homebrew first (interactive mode)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Then run the playbook again
ansible-playbook main.yml
```

**Permission issues:**
- Ensure your user has admin rights (required for Homebrew)
- The playbook needs sudo access to install Homebrew
- Some casks may require manual approval in System Preferences
- After first run, you may need to restart your terminal or run: `exec zsh`

### Ubuntu Issues

**Docker installation fails:**
```bash
# Manually add Docker repository
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

**VSCode installation fails:**
```bash
# Manually install VSCode
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code
```

## Post-Installation

### macOS
1. Restart your terminal to load new shell configuration
2. Log out and back in to apply Docker group changes
3. Open Docker Desktop and complete initial setup

### Ubuntu
1. Restart your terminal: `exec zsh` or `exec bash`
2. Log out and back in to apply Docker group changes
3. Verify Docker: `docker run hello-world`
4. Verify Node.js: `node --version`

## Verification

```bash
# Verify installations
git --version
python3 --version
node --version
docker --version
kubectl version --client

# Verify Python packages
pip3 list

# Verify VSCode extensions
code --list-extensions

# Test shell aliases (after restarting terminal)
gs      # Should show: git status
ll      # Should show: ls -lh
k       # Should show: kubectl help
```

## Shell Aliases Cheat Sheet

After installation, you'll have 60+ productivity aliases:

**Git:**
- `gs` → git status
- `ga` → git add
- `gcm "message"` → git commit -m "message"
- `gp` → git push
- `gpl` → git pull
- `gco branch` → git checkout branch
- `gcb new-branch` → git checkout -b new-branch
- `gl` → git log (graph view)

**Docker:**
- `d` → docker
- `dc` → docker compose
- `dps` → docker ps
- `dex container` → docker exec -it container
- `dlogs container` → docker logs -f container

**Kubernetes:**
- `k` → kubectl
- `kgp` → kubectl get pods
- `klogs pod` → kubectl logs -f pod

**Ansible:**
- `ap playbook.yml` → ansible-playbook playbook.yml
- `av` → ansible-vault
- `ag` → ansible-galaxy

See templates for the complete list!

