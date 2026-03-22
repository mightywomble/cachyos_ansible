# Quick Start Guide

## Installation in 3 Steps

### 1. Verify Prerequisites
```bash
# Check Ansible is installed
ansible --version

# Check paru is installed
paru --version

# Check you have sudo access
sudo whoami
```

### 2. Run the Playbook
```bash
# From the cachyos_installer directory
cd /home/david/code/gitrepo/cachyos_installer

# Run with automatic GPU detection
ansible-playbook -i inventory.yml playbook.yml
```

### 3. Wait for Completion
The playbook will:
- ✓ Detect your GPU (AMD, NVIDIA, or none)
- ✓ Verify system requirements (disk space, network)
- ✓ Install 70+ packages across 8 categories
- ✓ Enable Cockpit and Ollama services
- ✓ Display a summary when complete

## What Gets Installed

**70+ packages** including:
- CLI Tools: gping, bandwich, trippy, neoss, etc.
- Communication: Discord, Slack, Spotify, Edge, YouTube Music
- Development: VS Code, Termix, Tabby, Aider, LM Studio
- Productivity: OnlyOffice, Brave, Nextcloud
- AI/ML: Ollama with GPU support, AnythingLLM
- Monitoring: Cockpit + 8 plugins
- Virtualization: virt-manager
- Security: fprintd

## GPU Support

The playbook **automatically detects** your GPU:
- **AMD**: Installs ollama-rocm for ROCm acceleration
- **NVIDIA**: Installs ollama-cuda for CUDA acceleration
- **None**: Installs CPU-only ollama

To override:
```bash
# Force AMD
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=amd"

# Force NVIDIA
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=nvidia"

# Force CPU-only
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=none"
```

## Troubleshooting

### Missing Ansible
```bash
sudo pacman -S ansible
```

### Missing Paru
```bash
sudo pacman -S --needed base-devel git
git clone https://aur.archlinux.org/paru.git
cd paru && makepkg -si
```

### Want to see what will happen first?
```bash
ansible-playbook -i inventory.yml playbook.yml --check
```

### Want verbose output?
```bash
ansible-playbook -i inventory.yml playbook.yml -v
```

### System requirements check fails?
```bash
# Skip requirement verification
ansible-playbook -i inventory.yml playbook.yml -e "skip_failed_requirements=true"
```

## After Installation

### Access Cockpit
Open browser: `https://localhost:9090`

### Start using Ollama
```bash
# Pull a model
ollama pull llama2

# Chat with the model
ollama run llama2
```

### Check what's installed
```bash
# List all packages
pacman -Q | grep -E "neoss|discord|vscode|ollama"

# Check GPU was detected
grep gpu_type group_vars/cachyos.yml
```

## For More Info
See `README.md` for detailed documentation
