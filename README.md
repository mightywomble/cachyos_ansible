# CachyOS Software Installer

Ansible playbook to install and configure all your CachyOS software using the `paru` AUR helper.

> 📚 **New to this playbook?** Start with **[START_HERE.md](START_HERE.md)** for navigation and quick overview!

## Documentation Guide

Choose the guide that matches your needs:

| Guide | Purpose | Read Time |
|-------|---------|----------|
| **[START_HERE.md](START_HERE.md)** | Navigation hub, quick overview, common questions | 5 min |
| **[GETTING_STARTED.md](GETTING_STARTED.md)** | Step-by-step walkthrough for complete beginners | 10 min |
| **[IMPLEMENTATION_GUIDE.md](IMPLEMENTATION_GUIDE.md)** | Detailed execution guide with expected output | 15 min |
| **[README.md](README.md)** | This file - package details, configuration, troubleshooting | 20 min |
| **[ADDING_PACKAGES.md](ADDING_PACKAGES.md)** | How to add new packages to the installer | 10 min |
| **[QUICKSTART.md](QUICKSTART.md)** | Quick reference for common commands | 5 min |
| **[FILE_INDEX.md](FILE_INDEX.md)** | Project structure and file descriptions | 10 min |
| **[ENHANCEMENTS_SUMMARY.md](ENHANCEMENTS_SUMMARY.md)** | What's new and how it was enhanced | 10 min |

### Quick Paths

- 🚀 **Just want to run it?** → Read [GETTING_STARTED.md](GETTING_STARTED.md) then run `ansible-playbook -i inventory.yml playbook.yml`
- 🤔 **Want to understand the process?** → Read [IMPLEMENTATION_GUIDE.md](IMPLEMENTATION_GUIDE.md)
- ⚙️ **Need troubleshooting?** → See Troubleshooting section below
- 📦 **Want package details?** → Continue reading this file
- ➕ **Want to add new packages?** → Read [ADDING_PACKAGES.md](ADDING_PACKAGES.md)

---

## Features

✅ **Automatic GPU Detection** - Detects AMD, NVIDIA, or no GPU and installs appropriate packages  
✅ **System Requirements Verification** - Checks disk space, network, and hardware support  
✅ **Comprehensive Package Coverage** - 72+ packages organized into logical roles  
✅ **Full Automation** - No selective toggles; installs everything in one pass  
✅ **Service Management** - Auto-enables Cockpit and Ollama services

## Package Breakdown by Repository

### Pacman (Official Arch Repositories)
Packages from `extra/` repository:
- `discord` - Communication platform
- `proton-vpn-gtk-app` - VPN client
- `nextcloud-client` - Cloud sync client
- `llm-manager` - LLM management tool
- `fprintd` - Fingerprint authentication daemon
- `virt-manager` - Virtual machine management GUI
- `cockpit` - System management web interface
- `cockpit-docker` - Cockpit Docker plugin
- `cockpit-files` - Cockpit file manager
- `cockpit-machines` - Cockpit VM manager
- `cockpit-storaged` - Cockpit storage manager
- `cockpit-file-sharing` - Cockpit file sharing (AUR)
- `cockpit-sensors` - Cockpit sensors monitoring (AUR)
- `cockpit-pacman` - Cockpit package manager (AUR)
- `cockpit-tools` - Cockpit tools (AUR)

**Development Tools (Pacman):**
- `ansible` - Infrastructure automation and configuration management
- `terraform` - Infrastructure as Code tool for cloud resource provisioning

**CLI Tools (Pacman):**
- `gping` - Graphical ping utility
- `bandwich` - Bandwidth monitoring
- `trippy` - Network diagnostic tool

### CachyOS Repositories
Packages from `cachyos/` and `cachyos-extra-v3/`:

**cachyos/ repository:**
- `proton-pass` - Password manager
- `brave-bin` - Brave browser
- `onlyoffice-bin` - Office suite

**cachyos-extra-v3/ repository:**
- `ollama` - LLM inference engine (base)
- `ollama-rocm` - Ollama with AMD GPU support (auto-installed for AMD)
- `ollama-cuda` - Ollama with NVIDIA GPU support (auto-installed for NVIDIA)
- `opensnitch` - Application firewall
- `opencode` - Code editor
- `qwen-code` - Qwen code models
- `cockpit` - System management (alternative)

### AUR Packages (via paru)

**CLI Tools:**
- `lazyjournal-bin` - Terminal journal app
- `pacseek-bin` - Pacman package search
- `loggo-bin` - Log viewer
- `oatmeal-bin` - Terminal graphics tool
- `wifitui-bin` - WiFi TUI
- `hwatch` - Process monitor (⚠️ Compiles from source - takes 5-15 minutes)

**Communication:**
- `microsoft-edge-stable-bin` - Microsoft Edge browser
- `spotify` - Spotify client
- `youtube-music-desktop-v2-git` - YouTube Music desktop app
- `slack-desktop` - Slack client
- `netbird-bin` - Network management
- `netbird-ui-bin` - Network UI

**Development & Editors:**
- `visual-studio-code-bin` - VS Code editor
- `termix-bin` - Terminal emulator
- `tabby-bin` - Terminal multiplexer
- `aider-chat` - AI coding assistant (⚠️ Compiles from source - takes 10-20 minutes)
- `lmstudio-bin` - LM Studio interface
- `owlen` - Development tool
- `tlm` - Development tool

**Productivity & AI:**
- `anythingllm-desktop-bin` - LLM chat application

**Monitoring:**
- `ssh-ggh` - SSH key management

## Directory Structure

```
cachyos_installer/
├── playbook.yml              # Main playbook
├── inventory.yml             # Ansible inventory
├── README.md                 # This file
├── group_vars/
│   └── cachyos.yml          # Global variables
└── roles/
    ├── preflight/           # GPU detection & system requirements
    ├── cli_tools/           # CLI utilities
    ├── communication_apps/  # Messaging and browsers
    ├── dev_tools/           # Code editors and dev tools
    ├── productivity_apps/   # Office and productivity
    ├── ai_tools/            # AI/ML tools with GPU support
    ├── monitoring_tools/    # System monitoring (Cockpit)
    ├── virtualization/      # VM and virtualization tools
    └── security_utilities/  # Security tools
```

## Variables

Edit `group_vars/cachyos.yml` to customize:

```yaml
# GPU Configuration - 'auto' for automatic detection, or 'amd' | 'nvidia' | 'none'
gpu_type: "auto"

# Package management
pacman_update_cache: true

# Service activation
enable_cockpit: true
enable_ollama: true

# Preflight checks
verify_system_requirements: true
skip_failed_requirements: false
```

### GPU Auto-Detection

The playbook will automatically detect your GPU when `gpu_type: "auto"`:
- **AMD GPUs**: Detects AMD Radeon, RDNA, and ROCm-compatible cards
- **NVIDIA GPUs**: Detects NVIDIA GeForce and Tesla cards
- **No GPU**: Falls back to CPU-only Ollama

To manually override:
```yaml
gpu_type: "amd"      # Force AMD GPU packages
gpu_type: "nvidia"   # Force NVIDIA GPU packages
gpu_type: "none"     # Force CPU-only
```

### System Requirements Verification

The preflight role checks:
- ✓ **Disk Space**: Requires minimum 5GB free
- ✓ **Network Connectivity**: Verifies internet access
- ✓ **Fingerprint Hardware**: Warns if fprintd lacks device support
- ✓ **GPU Detection**: Identifies available GPU

Set `skip_failed_requirements: true` to continue even if checks fail.

## Prerequisites

- Ansible installed on your system
- CachyOS Linux installed
- Sudo access (for package installation)
- Internet connection
- `paru` AUR helper (script will verify it's installed)
- `lspci` and `lsusb` utilities (usually pre-installed)

## Usage

### Basic Installation

```bash
# Run full installation with auto GPU detection
ansible-playbook -i inventory.yml playbook.yml
```

### Manual GPU Configuration

```bash
# Override GPU detection for AMD
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=amd"

# Override GPU detection for NVIDIA
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=nvidia"

# Force CPU-only Ollama
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=none"
```

### Run Specific Roles

```bash
# Run only preflight checks
ansible-playbook -i inventory.yml playbook.yml --tags preflight

# Install only CLI tools
ansible-playbook -i inventory.yml playbook.yml --tags cli_tools

# Install only communication apps
ansible-playbook -i inventory.yml playbook.yml --tags communication_apps

# Install only AI tools
ansible-playbook -i inventory.yml playbook.yml --tags ai_tools
```

### Dry Run

```bash
# Preview changes without making them
ansible-playbook -i inventory.yml playbook.yml --check
```

### Skip Failed Requirements

```bash
# Continue installation even if system requirements fail verification
ansible-playbook -i inventory.yml playbook.yml -e "skip_failed_requirements=true"
```

## Running the Playbook

### First Run

1. Clone/download this playbook
2. Ensure you're in the `cachyos_installer` directory
3. Run: `ansible-playbook -i inventory.yml playbook.yml`

### With Manual GPU Selection

If you know your GPU type:
```bash
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=amd"
```

### Verbose Output

For debugging or to see what's happening:
```bash
ansible-playbook -i inventory.yml playbook.yml -v
```

## What Gets Installed

The playbook installs:

1. **CLI Tools** (6 pacman + 6 AUR packages)
2. **Communication Apps** (Discord, Slack, Spotify, Edge, YouTube Music, etc.)
3. **Development Tools** (VS Code, Termix, Tabby, LM Studio, etc.)
4. **Productivity Apps** (OnlyOffice, Brave, Nextcloud, etc.)
5. **AI/ML Tools** (Ollama with GPU support, LM Studio, AnythingLLM, etc.)
6. **Monitoring Tools** (Cockpit with all plugins)
7. **Virtualization** (virt-manager)
8. **Security Utilities** (fprintd for fingerprint auth)

## Notes

- The playbook uses `become: true` for pacman and systemd operations
- AUR packages are installed with `paru -S --noconfirm` for non-interactive installation
- Cockpit and Ollama services are automatically enabled and started
- GPU-specific ollama packages are installed based on auto-detection or manual setting
- System requirements are verified at the start with helpful warnings

## Troubleshooting

### Ansible not found
```bash
sudo pacman -S ansible
```

### Paru not installed
The playbook assumes paru is already installed. If not:
```bash
sudo pacman -S --needed base-devel git
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

### AUR packages fail to install
Ensure you have base development tools:
```bash
sudo pacman -S base-devel git
```

### Cockpit not accessible
Enable the socket manually:
```bash
sudo systemctl enable --now cockpit.socket
```

### GPU detection failed
Check available GPUs:
```bash
lspci | grep -i vga
```

Manually set GPU type:
```bash
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=amd"
```

### Disk space error
Free up at least 5GB:
```bash
# Check disk usage
df -h /

# Skip requirement check if needed
ansible-playbook -i inventory.yml playbook.yml -e "skip_failed_requirements=true"
```

### Network connectivity check fails
Ensure you have internet access, then add:
```bash
ansible-playbook -i inventory.yml playbook.yml -e "skip_failed_requirements=true"
```

## Co-Author Attribution

Created with Oz
