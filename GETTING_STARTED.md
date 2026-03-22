# Getting Started - CachyOS Software Installer

Welcome! This guide will walk you through running the playbook step by step.

## Prerequisites Check (30 seconds)

Before running the playbook, verify you have what you need:

```bash
# Check Ansible is installed
ansible --version

# Check paru is installed
paru --version

# Check you have sudo access
sudo whoami
```

If any of these fail, see the Troubleshooting section at the bottom.

## Step 1: Navigate to the Directory

```bash
cd /home/david/code/gitrepo/cachyos_installer
```

Verify you see these files:
```bash
ls -la
# You should see: playbook.yml, inventory.yml, README.md, group_vars/, roles/
```

## Step 2: Understand What Will Happen

The playbook will:

1. **Check your system** (1-2 minutes)
   - Detect if you have an AMD or NVIDIA GPU (or no GPU)
   - Verify you have enough disk space (at least 5GB)
   - Check that you have internet connectivity
   - Detect if you have fingerprint hardware

2. **Ask you to confirm** (30 seconds)
   - Show you a summary of what will be installed
   - Ask if you want to proceed
   - Default is "yes" - just press Enter

3. **Install 70 packages** (30-60 minutes depending on internet)
   - CLI tools (gping, bandwich, trippy, etc.)
   - Communication apps (Discord, Slack, Spotify, Edge)
   - Development tools (VS Code, Termix, Tabby, Aider, etc.)
   - Productivity apps (OnlyOffice, Brave, Nextcloud)
   - AI/ML tools (Ollama with GPU support)
   - Monitoring tools (Cockpit system admin interface)
   - Virtualization (virt-manager)
   - Security tools (fprintd fingerprint auth)

4. **Enable services** (10 seconds)
   - Start Cockpit (web-based system management)
   - Start Ollama (local AI inference)

5. **Show you completion summary** (30 seconds)
   - List all installed packages
   - Provide next steps for using each tool

## Step 3: Run the Playbook

The easiest way:

```bash
ansible-playbook -i inventory.yml playbook.yml
```

This will:
- Show you the intro banner
- Run all the preflight checks with explanations
- Show you a detailed summary
- Ask: "Proceed with installation? [Y/n] (default: yes)"
- **Just press Enter or type Y to continue**
- Watch as packages install (you'll see progress for each one)
- See the completion summary

## Step 4: Respond to the Confirmation Prompt

When you see:

```
Proceed with installation? [Y/n] (default: yes)
```

You have 3 options:

1. **Press Enter** (recommended)
   - Default is "yes", so just hit Enter
   - Installation will proceed

2. **Type Y and press Enter**
   - Explicitly says yes
   - Installation will proceed

3. **Type n and press Enter**
   - Cancel the installation
   - No packages will be installed
   - You can run the playbook again later

## Step 5: Wait for Installation

Once you confirm, installation begins. This can take 30-60 minutes depending on:
- **Internet speed** - some packages are large
- **AUR compilation** - VS Code, Ollama, etc. compile from source
- **Your CPU speed** - faster CPUs compile packages quicker

You'll see output like:

```
TASK [CLI_TOOLS] Installing Pacman CLI tools...
changed: [localhost]

TASK [CLI_TOOLS] Install lazyjournal-bin (terminal journal app)
changed: [localhost]

TASK [dev_tools] Install visual-studio-code-bin (may take 5-10 minutes)
changed: [localhost]

[... many more packages ...]
```

**This is all normal.** Let it run. Don't interrupt it.

## Step 6: Review the Completion Summary

When done, you'll see:

```
╔════════════════════════════════════════════════════════════════════╗
║          CACHYOS SOFTWARE INSTALLATION - COMPLETE ✓                ║
╚════════════════════════════════════════════════════════════════════╝

📦 INSTALLED PACKAGES:
────────────────────────────────────────────────────────────────────

✓ CLI Tools (6 packages)
✓ Communication Apps (6 packages)
✓ Development Tools (7 packages)
✓ Productivity Apps (5 packages)
✓ AI/ML Tools (5 packages)
✓ Monitoring Tools (10 packages)
✓ Virtualization (1 package)
✓ Security Tools (1 package)

[... more details ...]

✅ Installation Summary: ALL PACKAGES INSTALLED SUCCESSFULLY
```

Success! Everything was installed.

## Step 7: Access Your New Tools

### 1. Open Cockpit (System Admin Interface)

```bash
# Open in your browser
firefox https://localhost:9090 &
```

Login with your system username and password. You can now manage your system from a web interface.

### 2. Start Using Ollama (AI)

```bash
# Pull a language model (choose one)
ollama pull llama2          # General purpose
# ollama pull mistral      # Smaller, faster
# ollama pull neural-chat  # Optimized for chat

# Chat with the model
ollama run llama2
```

### 3. Use Development Tools

```bash
# Open VS Code
code .

# Or open a terminal to use other tools
termix              # Terminal emulator
tabby               # Terminal multiplexer
gping github.com    # Graphical ping
```

### 4. Use Communication Apps

Applications are installed in your app menu:
- Discord
- Slack
- Spotify
- Microsoft Edge
- YouTube Music Desktop
- Proton VPN

Click on the applications menu and search for them.

## Advanced Usage

### Running in Automatic Mode (No Confirmation Prompt)

If you want to skip the confirmation prompt:

```bash
ansible-playbook -i inventory.yml playbook.yml --extra-vars auto_confirm=true
```

Good for:
- Running in scripts
- Headless systems
- Docker containers
- Automated deployments

### Forcing a Specific GPU Type

If auto-detection gets it wrong:

```bash
# Force AMD GPU
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=amd"

# Force NVIDIA GPU
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=nvidia"

# Force CPU-only (no GPU acceleration)
ansible-playbook -i inventory.yml playbook.yml -e "gpu_type=none"
```

### Installing Only Specific Packages

If you don't want everything:

```bash
# Install only CLI tools
ansible-playbook -i inventory.yml playbook.yml --tags cli_tools

# Install only communication apps
ansible-playbook -i inventory.yml playbook.yml --tags communication_apps

# Install multiple categories
ansible-playbook -i inventory.yml playbook.yml --tags "cli_tools,dev_tools"
```

Available tags:
- `cli_tools`
- `communication_apps`
- `dev_tools`
- `productivity_apps`
- `ai_tools`
- `monitoring_tools`
- `virtualization`
- `security_utilities`
- `preflight`

### Preview What Will Happen (Dry Run)

```bash
ansible-playbook -i inventory.yml playbook.yml --check
```

This shows what would happen WITHOUT actually installing anything.

### See More Details (Verbose Mode)

```bash
ansible-playbook -i inventory.yml playbook.yml -v
```

Or for extreme detail:

```bash
ansible-playbook -i inventory.yml playbook.yml -vvv
```

## What if Something Goes Wrong?

### Package Installation Fails

If a single package fails, the playbook continues installing other packages. This is intentional - one failure shouldn't block everything.

**This is OK.** At the end, you'll see which packages failed.

### Network Error During Installation

If your internet cuts out, the playbook stops. Just restart it when you have internet back:

```bash
ansible-playbook -i inventory.yml playbook.yml
```

It will continue from where it left off (mostly).

### Playbook Hangs at Confirmation Prompt

This is normal - it's waiting for you. Just type `Y` and press Enter, or press Enter alone (default is yes).

### "Insufficient Disk Space" Error

Free up some space:

```bash
sudo journalctl --vacuum=500M   # Clean system logs
sudo pacman -Sc                 # Clean old packages
df -h /                         # Check how much space you have
```

Then run the playbook again.

### "No Network Connectivity" Error

Check your internet:

```bash
ping 8.8.8.8
```

Once connected, run the playbook again.

## Troubleshooting Ansible/Paru Issues

### "ansible: command not found"

Install Ansible:

```bash
sudo pacman -S ansible
```

### "paru: command not found"

Install paru:

```bash
sudo pacman -S --needed base-devel git
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

### "error: SUDO password is required"

Run with sudo:

```bash
sudo ansible-playbook -i inventory.yml playbook.yml
```

Or configure passwordless sudo (advanced).

### Playbook shows Python errors

Ensure Python is installed:

```bash
sudo pacman -S python
```

## Getting Help

For more information, see:

- **README.md** - Detailed documentation of all packages
- **QUICKSTART.md** - Quick reference
- **FILE_INDEX.md** - File structure
- **IMPLEMENTATION_GUIDE.md** - Detailed execution walkthrough

## Summary

That's it! The process is:

1. Run: `ansible-playbook -i inventory.yml playbook.yml`
2. See the checks (everything should pass)
3. Press Enter when asked to confirm
4. Wait for installation (30-60 minutes)
5. Enjoy your fully configured CachyOS system!

Questions? See README.md for detailed documentation.

Welcome to your new CachyOS setup!
