# File Index

## Core Configuration

### `playbook.yml`
**Main Ansible playbook** - Orchestrates all roles in sequence
- Runs preflight checks first
- Installs packages from 8 specialized roles
- Displays final summary

### `inventory.yml`
**Ansible inventory** - Defines target hosts
- Configured for localhost execution
- Uses local connection (no SSH required)
- Includes localhost group definition

### `group_vars/cachyos.yml`
**Global variables** - Configuration for entire playbook
- `gpu_type: "auto"` - Auto-detect GPU (amd/nvidia/none)
- `pacman_update_cache: true` - Update pacman cache before install
- `enable_cockpit: true` - Enable Cockpit service
- `enable_ollama: true` - Enable Ollama service
- `verify_system_requirements: true` - Check disk/network/hardware
- `skip_failed_requirements: false` - Fail if requirements not met

## Roles

### `roles/preflight/tasks/main.yml`
**Preflight checks and GPU detection**
- Detects AMD GPU via lspci grep
- Detects NVIDIA GPU via lspci grep
- Checks for fingerprint hardware (fprintd requirement)
- Verifies 5GB+ disk space available
- Tests network connectivity (ping 8.8.8.8)
- Displays pre-installation summary
- No packages installed here—checks only

### `roles/cli_tools/tasks/main.yml`
**CLI utilities and system tools**
- Pacman: gping, bandwich, trippy, neoss, kmsensors, hwatch-bin
- AUR: lazyjournal-bin, pacseek-bin, loggo-bin, oatmeal-bin, wifitui-bin

### `roles/communication_apps/tasks/main.yml`
**Messaging, browsers, VPN, and networking apps**
- Pacman: discord, proton-vpn-gtk-app
- CachyOS: proton-pass
- AUR: microsoft-edge-stable-bin, spotify, slack-desktop, youtube-music-desktop-v2-git, netbird-bin, netbird-ui-bin

### `roles/dev_tools/tasks/main.yml`
**Code editors and development tools**
- AUR: visual-studio-code-bin, termix-bin, tabby-bin, aider-chat, lmstudio-bin, owlen, tlm

### `roles/productivity_apps/tasks/main.yml`
**Office suite, browsers, cloud sync**
- Pacman: nextcloud-client, llm-manager
- CachyOS: onlyoffice-bin, brave-bin
- AUR: anythingllm-desktop-bin

### `roles/ai_tools/tasks/main.yml`
**AI/ML frameworks with automatic GPU support**
- Pacman: extra/llm-manager
- CachyOS: ollama (base), opencode, qwen-code
- CachyOS (if AMD detected): ollama-rocm
- CachyOS (if NVIDIA detected): ollama-cuda
- Enables and starts ollama service

### `roles/monitoring_tools/tasks/main.yml`
**System management and monitoring**
- Pacman: cockpit, cockpit-docker, cockpit-files, cockpit-machines, cockpit-storaged, cockpit-file-sharing, cockpit-sensors, cockpit-packman, cockpit-tools
- CachyOS: cockpit (alternative)
- AUR: ssh-ggh
- Enables and starts cockpit.socket service

### `roles/virtualization/tasks/main.yml`
**Virtual machine and virtualization tools**
- Pacman: virt-manager

### `roles/security_utilities/tasks/main.yml`
**Security and authentication tools**
- Pacman: fprintd (fingerprint daemon)

## Documentation

### `README.md`
**Comprehensive documentation**
- Feature overview
- Complete package breakdown by repository
- Directory structure
- Configuration variables explained
- GPU auto-detection details
- Usage examples (basic, manual GPU, specific roles, dry-run)
- Prerequisites and first-time setup
- Detailed troubleshooting section
- 300+ lines of full documentation

### `QUICKSTART.md`
**Quick start guide for immediate use**
- 3-step installation process
- What gets installed summary
- GPU support explanation
- Common troubleshooting
- Post-installation next steps

### `FILE_INDEX.md`
**This file** - Index of all files and their purposes

## How to Use

### Start Here
1. Read `QUICKSTART.md` for immediate setup
2. Review `group_vars/cachyos.yml` to understand variables
3. Run: `ansible-playbook -i inventory.yml playbook.yml`

### For Detailed Info
1. Read `README.md` for full documentation
2. Check `FILE_INDEX.md` (this file) for what each file does
3. Review individual role files in `roles/*/tasks/main.yml`

### For Advanced Use
1. Edit `group_vars/cachyos.yml` to customize
2. Modify specific role files to add/remove packages
3. Use `--tags` to run specific roles
4. Use `-e` to override variables on command line

## Package Summary

| Category | Count | Type |
|----------|-------|------|
| CLI Tools | 12 | 6 pacman + 6 AUR |
| Communication | 6 | 2 pacman + 1 CachyOS + 3 AUR |
| Development | 7 | 7 AUR |
| Productivity | 5 | 2 pacman + 2 CachyOS + 1 AUR |
| AI/ML | 5 | 1 pacman + 3-4 CachyOS |
| Monitoring | 10 | 9 pacman + 1 CachyOS + 1 AUR |
| Virtualization | 1 | 1 pacman |
| Security | 1 | 1 pacman |
| **TOTAL** | **~70** | Across multiple repositories |

## Command Reference

```bash
# Basic installation
ansible-playbook -i inventory.yml playbook.yml

# With AMD GPU
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=amd"

# With NVIDIA GPU
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=nvidia"

# Dry run (no changes)
ansible-playbook -i inventory.yml playbook.yml --check

# Verbose output
ansible-playbook -i inventory.yml playbook.yml -v

# Run only preflight
ansible-playbook -i inventory.yml playbook.yml --tags preflight

# Run only AI tools
ansible-playbook -i inventory.yml playbook.yml --tags ai_tools

# Skip requirement checks
ansible-playbook -i inventory.yml playbook.yml -e "skip_failed_requirements=true"
```

## Maintenance Notes

- All AUR packages use `paru -S --noconfirm` for non-interactive install
- Pacman packages use `update_cache: true` to refresh before install
- Services (Cockpit, Ollama) are enabled with `enabled: yes`
- Services are started with `state: started`
- GPU-specific packages only install based on detected GPU type
- Preflight runs first and can halt execution if requirements fail
