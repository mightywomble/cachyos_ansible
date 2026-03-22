# Implementation Guide - CachyOS Software Installer

This guide explains exactly how to run the playbook, what to expect at each stage, and how to interpret the output.

## Quick Start (3 Commands)

```bash
# Navigate to playbook directory
cd /home/david/code/gitrepo/cachyos_installer

# Run with automatic GPU detection and user confirmation prompt
ansible-playbook -i inventory.yml playbook.yml

# OR skip the confirmation prompt (for headless/automated runs)
ansible-playbook -i inventory.yml playbook.yml --extra-vars auto_confirm=true
```

## Expected Execution Stages

### Stage 1: Playbook Initialization (30 seconds)

When you run the command, you'll see:

```
PLAY [Install CachyOS software using paru] ****

TASK [Display playbook introduction] **
ok: [localhost] => {
    "msg": "
    ╔════════════════════════════════════════════════════════════════════╗
    ║                  CACHYOS SOFTWARE INSTALLER v1.0                   ║
    ║                                                                    ║
    ║  Automated installation of 70+ packages for optimal CachyOS setup  ║
    ╚════════════════════════════════════════════════════════════════════╝
    
    This playbook will:
    • Detect your GPU type (AMD/NVIDIA/None)
    • Verify system requirements (disk space, network, hardware)
    • Install packages with automatic error recovery
    • Enable and start system services
    • Display detailed installation summary
    
    Estimated installation time: 15-45 minutes
    "
}
```

### Stage 2: Preflight Checks (1-2 minutes)

The playbook will perform system checks and display results:

```
TASK [PREFLIGHT] Starting GPU detection...
ok: [localhost] => {
    "msg": "Scanning system for compatible GPUs..."
}

TASK [PREFLIGHT] GPU detection complete
ok: [localhost] => {
    "msg": "
    ✓ GPU Detection Result: AMD
    → AMD GPU detected! Ollama will be installed with ROCm acceleration
    → This enables GPU-accelerated LLM inference
    "
}

TASK [PREFLIGHT] Check disk space availability
ok: [localhost]

TASK [PREFLIGHT] Check disk space status
ok: [localhost] => {
    "msg": "
    ✓ Disk Space: 127GB available (EXCELLENT - plenty of room)
    "
}

TASK [PREFLIGHT] Check network connectivity
ok: [localhost]

TASK [PREFLIGHT] Display network status
ok: [localhost] => {
    "msg": "
    ✓ Network: Connected (can reach internet)
    "
}

TASK [PREFLIGHT] Display fingerprint hardware status
ok: [localhost] => {
    "msg": "
    ℹ Fingerprint Device: NOT DETECTED (fprintd will install but may have limited use)
    "
}
```

### Stage 3: Comprehensive Preflight Summary (30 seconds)

The playbook displays a complete summary and asks for confirmation:

```
TASK [PREFLIGHT] Show comprehensive preflight summary
ok: [localhost] => {
    "msg": "
    
    ╔════════════════════════════════════════════════════════════════════╗
    ║           CACHYOS SOFTWARE INSTALLER - PREFLIGHT SUMMARY          ║
    ╚════════════════════════════════════════════════════════════════════╝
    
    📊 SYSTEM INFORMATION:
    ────────────────────────────────────────────────────────────────────
    GPU Type:               AMD
    Disk Space Available:   127GB
    Network:                Connected ✓
    Fingerprint Device:     Not Found ℹ
    
    📦 INSTALLATION SCOPE:
    ────────────────────────────────────────────────────────────────────
    Total Packages:         ~70 packages
    Sources:                Pacman + CachyOS Repos + AUR (via paru)
    Installation Method:    Non-interactive (--noconfirm)
    Installation Time:      15-45 minutes (depending on internet speed)
    
    📋 PACKAGE CATEGORIES:
    ────────────────────────────────────────────────────────────────────
    ✓ CLI Tools              (6 utilities)
    ✓ Communication Apps     (6 apps: Discord, Slack, Spotify, etc.)
    ✓ Development Tools      (7 tools: VS Code, Termix, Tabby, etc.)
    ✓ Productivity Apps      (5 apps: OnlyOffice, Brave, etc.)
    ✓ AI/ML Tools            (5 tools: Ollama + GPU support, etc.)
    ✓ Monitoring Tools       (10 tools: Cockpit + plugins)
    ✓ Virtualization         (1 tool: virt-manager)
    ✓ Security Tools         (1 tool: fprintd)
    
    ⚙️  SERVICES AUTO-ENABLED:
    ────────────────────────────────────────────────────────────────────
    ✓ Cockpit                (Web-based system management)
    ✓ Ollama                 (Local LLM inference service)
    
    ✅ PREFLIGHT STATUS: ALL CHECKS PASSED
    ────────────────────────────────────────────────────────────────────
"
}

TASK [PREFLIGHT] Pause for user confirmation (unless --auto flag used)
Proceed with installation? [Y/n] (default: yes)

NOTE: Installation will take 15-45 minutes depending on internet speed.
      Run with --extra-vars auto_confirm=true to skip this prompt.
```

**At this point, type `Y` or `Enter` to proceed, or `n` to cancel.**

### Stage 4: Package Installation (15-45 minutes)

After confirmation, installation begins. Each role shows detailed progress:

#### CLI Tools Installation (2-3 minutes)

```
TASK [CLI_TOOLS] Installing command-line utilities and system tools
ok: [localhost]

TASK [CLI_TOOLS] Installing Pacman CLI tools...
ok: [localhost] => {
    "msg": "Installing 6 CLI utilities from official repositories: gping, bandwich, trippy, neoss, kmsensors, hwatch-bin"
}

TASK [CLI_TOOLS] Install pacman CLI tools from official repositories
changed: [localhost]

TASK [CLI_TOOLS] Show pacman installation status
ok: [localhost] => {
    "msg": "✓ Pacman CLI tools installation status: Success"
}

TASK [CLI_TOOLS] Installing AUR CLI tools (via paru)...
ok: [localhost] => {
    "msg": "
    Installing 6 AUR CLI utilities. These may take longer as they compile from source.
    Packages: lazyjournal-bin, pacseek-bin, loggo-bin, oatmeal-bin, wifitui-bin
    "
}

TASK [CLI_TOOLS] Install lazyjournal-bin (terminal journal app)
changed: [localhost]

TASK [CLI_TOOLS] Install pacseek-bin (Pacman package searcher)
changed: [localhost]

[...more packages...]

TASK [CLI_TOOLS] CLI tools installation complete
ok: [localhost] => {
    "msg": "
    ✓ CLI Tools installation finished
    Status Summary:
    - lazyjournal-bin: Installed
    - pacseek-bin:     Installed
    - loggo-bin:       Installed
    - oatmeal-bin:     Installed
    - wifitui-bin:     Installed
    "
}
```

**Note:** AUR packages may take longer (2-5 minutes each) as they compile from source.

#### Communication Apps Installation (5-10 minutes)

```
TASK [communication_apps] Install communication and messaging applications
ok: [localhost]

[...packages installing...]

TASK [communication_apps] Show installation status
ok: [localhost] => {
    "msg": "
    ✓ Communication apps installation finished
    Status Summary:
    - discord:                  Installed
    - proton-vpn-gtk-app:       Installed
    - proton-pass:              Installed
    - microsoft-edge-stable:    Installed
    - spotify:                  Installed
    - youtube-music-desktop:    Installed
    - slack:                    Installed
    - netbird-bin:              Installed
    - netbird-ui-bin:           Installed
    "
}
```

#### Development Tools Installation (10-20 minutes)

```
TASK [dev_tools] Installing development tools and code editors
ok: [localhost]

TASK [dev_tools] Installing VS Code and development tools (may take several minutes)...
ok: [localhost] => {
    "msg": "
    Installing 7 development tools. These include VS Code which is large (~300MB).
    Please wait patiently as these compile/download from AUR.
    Expected time: 10-20 minutes depending on internet speed.
    "
}

TASK [dev_tools] Install visual-studio-code-bin (may take 5-10 minutes)
changed: [localhost]

[...more tools...]

TASK [dev_tools] Development tools installation complete
ok: [localhost] => {
    "msg": "
    ✓ Development tools installation finished
    Status Summary:
    - visual-studio-code-bin:  Installed
    - termix-bin:              Installed
    - tabby-bin:               Installed
    - aider-chat:              Installed
    - lmstudio-bin:            Installed
    - owlen:                   Installed
    - tlm:                     Installed
    "
}
```

#### Remaining Installations

```
[communication_apps] → 5-10 minutes
[productivity_apps] → 5-8 minutes
[ai_tools] → 5-15 minutes (Ollama with GPU support can be large)
[monitoring_tools] → 5-8 minutes (Cockpit + plugins)
[virtualization] → 2-3 minutes
[security_utilities] → 1-2 minutes
```

### Stage 5: Completion Summary (30 seconds)

After all packages install, the playbook displays:

```
TASK [Display installation completion summary]
ok: [localhost] => {
    "msg": "
    
    ╔════════════════════════════════════════════════════════════════════╗
    ║          CACHYOS SOFTWARE INSTALLATION - COMPLETE ✓                ║
    ╚════════════════════════════════════════════════════════════════════╝
    
    📦 INSTALLED PACKAGES:
    ────────────────────────────────────────────────────────────────────
    
    ✓ CLI Tools (6 packages)
      gping, bandwich, trippy, neoss, kmsensors, hwatch-bin
    
    ✓ Communication Apps (6 packages)
      Discord, Edge, Spotify, Slack, YouTube Music, Netbird
    
    ✓ Development Tools (7 packages)
      VS Code, Termix, Tabby, Aider, LM Studio, Owlen, TLM
    
    ✓ Productivity Apps (5 packages)
      OnlyOffice, Brave, Nextcloud, LLM Manager
    
    ✓ AI/ML Tools (5 packages)
      Ollama-ROCm, OpenCode, Qwen, LLM Manager
    
    ✓ Monitoring Tools (10 packages)
      Cockpit + 8 plugins, SSH-ggh
    
    ✓ Virtualization (1 package)
      virt-manager
    
    ✓ Security Tools (1 package)
      fprintd
    
    🎯 CONFIGURATION:
    ────────────────────────────────────────────────────────────────────
    GPU Type:           AMD
    Ollama Variant:     ollama-rocm (ROCm acceleration for AMD GPUs)
    
    ⚙️  SERVICES ENABLED:
    ────────────────────────────────────────────────────────────────────
    ✓ Cockpit.socket    (Access at: https://localhost:9090)
    ✓ Ollama            (Local LLM inference service)
    
    📚 NEXT STEPS:
    ────────────────────────────────────────────────────────────────────
    
    1. Access Cockpit Web Interface:
       Open your browser and navigate to: https://localhost:9090
       Login with your system credentials
    
    2. Start Using Ollama:
       # Pull a language model
       ollama pull llama2
       
       # Chat with the model
       ollama run llama2
    
    3. Configure Communication Apps:
       Log into Discord, Slack, Spotify with your credentials
    
    4. Set up Development Tools:
       Open VS Code and install your preferred extensions
    
    5. Verify All Packages:
       # Check installed packages
       pacman -Q | wc -l
    
    💡 TIPS & RESOURCES:
    ────────────────────────────────────────────────────────────────────
    • Cockpit Documentation: https://cockpit-project.org/
    • Ollama Documentation: https://ollama.ai/
    • VS Code Extensions: https://marketplace.visualstudio.com/
    • Troubleshooting: See README.md for detailed documentation
    
    ✅ Installation Summary: ALL PACKAGES INSTALLED SUCCESSFULLY
    
    Thank you for using CachyOS Software Installer!
    "
}

PLAY RECAP *
localhost                  : ok=123  changed=45   unreachable=0    failed=0

===================== CachyOS Software Installation Complete! =====================
```

## Running Modes

### Mode 1: Interactive (Default)

```bash
ansible-playbook -i inventory.yml playbook.yml
```

- Shows all preflight checks with detailed explanations
- Asks user confirmation before proceeding
- User sees detailed progress for each package
- Best for: First-time runs, debugging, seeing what's happening

### Mode 2: Automated (--auto flag)

```bash
ansible-playbook -i inventory.yml playbook.yml --extra-vars auto_confirm=true
```

- Skips the user confirmation prompt
- Proceeds directly to installation
- Still shows all verbose output
- Best for: Scripts, cron jobs, headless systems

### Mode 3: GPU Override

```bash
# Force AMD GPU
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=amd"

# Force NVIDIA GPU
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=nvidia"

# Force CPU-only
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=none"
```

### Mode 4: Dry Run (Preview Only)

```bash
ansible-playbook -i inventory.yml playbook.yml --check
```

- Shows what would happen without making changes
- Useful to preview the installation
- Won't actually install packages

### Mode 5: Verbose Output (Debugging)

```bash
ansible-playbook -i inventory.yml playbook.yml -v
```

Or even more verbose:

```bash
ansible-playbook -i inventory.yml playbook.yml -vv
```

Or maximum verbosity:

```bash
ansible-playbook -i inventory.yml playbook.yml -vvv
```

### Mode 6: Run Only Specific Category

```bash
# Install only CLI tools
ansible-playbook -i inventory.yml playbook.yml --tags cli_tools

# Install only communication apps
ansible-playbook -i inventory.yml playbook.yml --tags communication_apps

# Install only AI tools
ansible-playbook -i inventory.yml playbook.yml --tags ai_tools

# Run multiple categories
ansible-playbook -i inventory.yml playbook.yml --tags "cli_tools,dev_tools"
```

## What Happens on Failures

### Package Install Fails

If a single package fails to install, the playbook **continues** instead of stopping:

```
TASK [cli_tools] Install lazyjournal-bin
failed: [localhost] (rc=2) => {
    "stderr": "error: target not found: lazyjournal-bin"
}

[RECOVERED] Continuing installation despite package failure...

TASK [cli_tools] Install pacseek-bin
changed: [localhost]
```

The playbook tracks failures and reports them in the summary:

```
⚠️  INSTALLATION NOTES:
────────────────────────────────────────────────────────────────────
Failed to install: lazyjournal-bin (package not found in AUR)
                   (This is non-critical and other packages were successfully installed)
```

### Network Failure During Preflight

If there's no internet during preflight, the playbook **stops** because packages can't be downloaded:

```
TASK [preflight] Fail if no network connectivity
fatal: [localhost]: FAILED! =>
    "msg": "No network connectivity detected. Please connect to the internet before proceeding."

PLAY RECAP
localhost  : ok=5  changed=0  unreachable=0  failed=1
```

To bypass this:
```bash
ansible-playbook -i inventory.yml playbook.yml -e "skip_failed_requirements=true"
```

### Disk Space Failure

If there's less than 5GB available:

```
TASK [preflight] Fail if insufficient disk space
fatal: [localhost]: FAILED! =>
    "msg": "Insufficient disk space (< 5GB free). Please free up space before proceeding."
```

## Time Expectations

- **Preflight checks**: 30 seconds - 2 minutes
- **User confirmation wait**: Depends on user (defaults to yes after prompt)
- **CLI tools**: 2-3 minutes (mostly pacman)
- **Communication apps**: 5-10 minutes (Discord, Edge, Slack take time)
- **Development tools**: 10-20 minutes (VS Code is ~300MB binary)
- **Productivity apps**: 5-8 minutes
- **AI/ML tools**: 5-15 minutes (Ollama ROCm/CUDA packages can be large)
- **Monitoring tools**: 5-8 minutes (Cockpit + plugins)
- **Virtualization**: 2-3 minutes
- **Security utilities**: 1-2 minutes
- **Post-installation summary**: 30 seconds

**Total estimated time: 35-75 minutes** (mostly depends on internet speed and package compilation time for AUR packages)

## Interpreting Status Messages

### ✓ (Checkmark)
- Package/check completed successfully
- Service started/enabled successfully

### ⚠ (Warning triangle)
- Non-critical issue (e.g., disk space adequate but not excellent)
- Package installed but with warnings
- Hardware not detected but feature still installed

### ℹ (Information)
- Informational message
- Device not found but not required
- Feature has limited functionality

### ✗ (X mark)
- Critical failure
- System check failed
- Installation blocked

## After Installation

```bash
# Verify all packages were installed
pacman -Q | grep -E "discord|vscode|ollama|cockpit"

# Check services are running
systemctl status cockpit
systemctl status ollama

# Access Cockpit
firefox https://localhost:9090 &

# Start using Ollama
ollama pull llama2
ollama run llama2
```

## Troubleshooting Common Issues

### Playbook hangs at confirmation prompt

This is normal - it's waiting for user input. Type `Y` and press Enter to continue, or `n` to cancel.

### Individual packages fail but installation continues

This is expected behavior. The playbook continues and reports failed packages in the summary.

### Installation is very slow

This is likely AUR packages compiling from source. VS Code, Ollama, and some others are large and take time. Be patient.

### Insufficient disk space error

Free up at least 5GB:
```bash
sudo journalctl --vacuum=500M  # Clean journal logs
sudo pacman -Sc               # Clean old packages
```

Then run the playbook again.

### Network error during installation

Ensure you have internet:
```bash
ping 8.8.8.8
```

If network is interrupted, the playbook will fail on that role. Run it again to resume.

## Getting Help

See these files for more information:
- `README.md` - Detailed documentation of all packages
- `QUICKSTART.md` - Quick reference guide
- `FILE_INDEX.md` - File structure and organization
