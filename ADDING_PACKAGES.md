# Adding Packages to CachyOS Installer

This guide explains exactly how to add new packages to the CachyOS software installer playbook.

## Quick Reference

**Where**: Edit the role task file for your desired category  
**Format**: YAML with pacman or paru commands  
**Three sources**: Pacman (official), CachyOS repos, AUR (via paru)

---

## Understanding the Package Structure

The installer is organized into **categories** (called "roles"):

```
roles/
├── cli_tools/              # Command-line utilities
├── communication_apps/     # Discord, Slack, browsers, etc.
├── dev_tools/              # Code editors, IDEs
├── productivity_apps/      # Office suites, cloud sync
├── ai_tools/               # LLMs, AI frameworks
├── monitoring_tools/       # Cockpit, system monitors
├── virtualization/         # VM tools
├── security_utilities/     # Fingerprint, encryption
└── preflight/              # Pre-installation checks
```

Each category has:
- `roles/<category>/tasks/main.yml` - The task file where packages are installed

## Package Sources

### 1. **Pacman** (Official Arch/CachyOS Repositories)
- Fastest to install (pre-compiled binaries)
- Official and tested
- Used with `pacman` module in Ansible

**Example categories**: cli_tools (gping, bandwich, etc.), communication_apps (discord, etc.)

### 2. **CachyOS Extra Repositories** (cachyos-extra-v3)
- Optimized for CachyOS
- Community packages not in Arch official repos
- Also installed via `pacman` module (already in repos)

### 3. **AUR** (Arch User Repository)
- Community-maintained packages
- Often compile from source (slower)
- Installed via `paru` command (AUR helper)

**Example packages**: visual-studio-code-bin, aider-chat, spotify

---

## How to Add a Pacman Package

### Step 1: Choose the Right Category

Look at existing packages in each role to understand what goes where:

- **cli_tools**: gping, bandwich, trippy, neoss, kmsensors, hwatch-bin
- **communication_apps**: discord, proton-vpn-gtk-app, nextcloud-client
- **dev_tools**: visual-studio-code-bin, termix-bin
- **productivity_apps**: onlyoffice-bin, brave-bin, nextcloud-client
- **ai_tools**: ollama, opensnitch, opencode
- **monitoring_tools**: cockpit, cockpit-docker, cockpit-files
- **virtualization**: virt-manager
- **security_utilities**: fprintd

**Pick the category that best matches the package's function.**

### Step 2: Edit the Role's Task File

Open the role file:
```bash
nano roles/<category>/tasks/main.yml
```

### Step 3: Add Your Package

**For Official Pacman Packages**, find the `pacman` task and add to the list:

```yaml
- name: "[CATEGORY] Install <category> packages from official repositories"
  become: true
  pacman:
    name:
      - existing_package_1
      - existing_package_2
      - your_new_package    # ← Add here with comment
    state: present
    update_cache: "{{ pacman_update_cache }}"
  register: pacman_result
  ignore_errors: true
```

**Example - Adding `htop` to CLI tools:**

```yaml
- name: "[CLI_TOOLS] Install pacman CLI tools from official repositories"
  become: true
  pacman:
    name:
      - gping          # Real-time ping visualization tool
      - bandwich       # Bandwidth monitor showing per-process usage
      - trippy         # Network diagnostic tool (like mtr, shows routing)
      - hwatch-bin     # Process monitor with colored output
      - neoss          # System monitor showing CPU/Memory/Disk usage
      - kmsensors      # Hardware sensor monitoring (CPU temp, fan speed)
      - htop           # Interactive process viewer (NEW)
    state: present
    update_cache: "{{ pacman_update_cache }}"
```

### Step 4: Update Documentation

After adding a package, update the README.md in the appropriate section:

**In README.md**, find the package category section and add your package:

```markdown
**CLI Tools (Pacman):**
- `gping` - Graphical ping utility
- `bandwich` - Bandwidth monitoring
- `htop` - Interactive process viewer
```

---

## How to Add an AUR Package

AUR packages are community-maintained and often compile from source. They require the `paru` AUR helper.

### Step 1: Choose the Category

Same process as pacman packages - pick the category that matches the function.

### Step 2: Edit the Role's Task File

Open the role file:
```bash
nano roles/<category>/tasks/main.yml
```

### Step 3: Add Your Package

**For AUR Packages, there are two patterns:**

#### Pattern 1: Multiple AUR Packages (Using loop)

This is preferred for efficiency. Find the loop-based task and add to it:

```yaml
- name: Install AUR <category> tools via paru
  become: true
  become_user: "{{ ansible_user_id }}"
  command: "paru -S --noconfirm {{ item }}"
  loop:
    - aur/existing-package-1
    - aur/existing-package-2
    - aur/your-new-package  # ← Add here
  register: paru_install_result
  failed_when: paru_install_result.rc not in [0, 1]
  changed_when: "'error' not in paru_install_result.stdout | lower"
```

**Example - Adding `bottom-bin` to CLI tools:**

If using a loop pattern, add:
```yaml
loop:
  - aur/lazyjournal-bin
  - aur/pacseek-bin
  - aur/bottom-bin     # ← NEW
```

#### Pattern 2: Individual Packages (One Package Per Task)

Used in cli_tools for finer-grained error tracking. Create a new task:

```yaml
- name: "[CLI_TOOLS] Install your-new-package (package description)"
  become: true
  become_user: "{{ ansible_user_id }}"
  command: "paru -S --noconfirm your-new-package"
  register: paru_your_new_package
  failed_when: false
  changed_when: "'error' not in paru_your_new_package.stdout | lower and paru_your_new_package.rc == 0"
```

**Then update the completion message:**

Find the final debug task that shows installation status:

```yaml
- name: "[CATEGORY] Installation complete"
  debug:
    msg: |
      ✓ Category installation finished
      Status Summary:
      - package-1: {{ 'Installed' if ... }}
      - package-2: {{ 'Installed' if ... }}
      - your-new-package: {{ 'Installed' if 'already' in paru_your_new_package.stdout or paru_your_new_package.rc == 0 else 'Skipped' }}  # ← Add here
```

---

## How to Add a Package from CachyOS Extra Repository

Packages in `cachyos-extra-v3` are installed the same way as official pacman packages:

```yaml
- name: "[CATEGORY] Install <category> packages"
  become: true
  pacman:
    name:
      - existing_package
      - new_package_from_cachyos_extra
    state: present
    update_cache: "{{ pacman_update_cache }}"
```

No special syntax needed - `pacman` automatically searches all configured repositories including `cachyos-extra-v3`.

---

## Step-by-Step Example: Adding Slack

Let's add Slack to communication_apps (it's on AUR):

### 1. Open the communication_apps role:
```bash
nano roles/communication_apps/tasks/main.yml
```

### 2. Find the AUR installation section and add slack-desktop:

**Before:**
```yaml
- name: Install AUR communication tools via paru
  become: true
  become_user: "{{ ansible_user_id }}"
  command: "paru -S --noconfirm {{ item }}"
  loop:
    - aur/microsoft-edge-stable-bin
    - aur/spotify
    - aur/youtube-music-desktop-v2-git
```

**After:**
```yaml
- name: Install AUR communication tools via paru
  become: true
  become_user: "{{ ansible_user_id }}"
  command: "paru -S --noconfirm {{ item }}"
  loop:
    - aur/microsoft-edge-stable-bin
    - aur/spotify
    - aur/youtube-music-desktop-v2-git
    - aur/slack-desktop  # ← NEW
```

### 3. Update README.md:

Find the AUR section in README.md and add:

```markdown
**Communication:**
- `microsoft-edge-stable-bin` - Microsoft Edge browser
- `spotify` - Spotify client
- `youtube-music-desktop-v2-git` - YouTube Music desktop app
- `slack-desktop` - Slack client
```

---

## Best Practices

### 1. Always Add Comments

Include what each package does:

```yaml
- gping          # Real-time ping visualization (shows latency over time)
- htop           # Interactive process monitor (sort by CPU, memory, etc.)
```

### 2. Keep Lists Alphabetically Sorted (Optional but Clean)

```yaml
- aider-chat
- lmstudio-bin
- owlen           # NEW - add in alphabetical position
- termix-bin
- tlm
```

### 3. Test Before Committing

```bash
# Test your changes with a specific role
ansible-playbook -i inventory.yml playbook.yml --tags communication_apps

# Or test just the preflight to ensure no syntax errors
ansible-playbook -i inventory.yml playbook.yml --tags preflight --check
```

### 4. Verify Package Exists

Before adding, verify the package is actually available:

**For Pacman packages:**
```bash
pacman -Ss <package-name>
```

**For AUR packages:**
```bash
paru -Ss <package-name>
# Or check: https://aur.archlinux.org/packages/<package-name>
```

### 5. Update All Documentation

When adding a package, update:
1. ✅ Role task file (`roles/<category>/tasks/main.yml`)
2. ✅ README.md (in appropriate package list section)
3. ✅ Completion message in playbook.yml if necessary

---

## Common Package Sources Cheat Sheet

| Package Type | Repository | Installation Method | File to Edit | Speed |
|---|---|---|---|---|
| System utility | Pacman (official) | pacman module | roles/cli_tools/tasks/main.yml | ⚡ Fast |
| Browser | Pacman/AUR | pacman or paru | roles/communication_apps/tasks/main.yml | 🐢 Slow (AUR) |
| IDE/Editor | AUR | paru command | roles/dev_tools/tasks/main.yml | 🐢 Very slow |
| Office suite | Pacman | pacman module | roles/productivity_apps/tasks/main.yml | ⚡ Fast |
| LLM tool | CachyOS Extra | pacman module | roles/ai_tools/tasks/main.yml | 🐢 Slow |

---

## Troubleshooting

### Package Not Found

Error: `error: target not found: your-package`

**Solution:**
1. Verify the exact package name: `pacman -Ss <name>` or `paru -Ss <name>`
2. Check if it's in a specific repository: `pacman -Ss repo/package-name`
3. Check if it's been renamed or removed

### Pacman vs Paru Confusion

- Use `pacman` module if the package is in **official/CachyOS repositories**
- Use `paru` command if the package is in **AUR only**

Check which one:
```bash
# If this returns results, use pacman module
pacman -Ss exact-package-name

# If this only returns AUR results, use paru command
paru -Ss exact-package-name
```

### Installation Fails During Playbook Run

This is normal for AUR packages. The playbook continues and reports failures in the summary. Common reasons:
- Package was removed from AUR
- Build dependencies not installed
- Network timeout (try running again)

### Accidental Syntax Error

Before committing, validate your YAML:

```bash
# Check for syntax errors
ansible-playbook -i inventory.yml playbook.yml --syntax-check

# Run in check mode (no changes)
ansible-playbook -i inventory.yml playbook.yml --check
```

---

## Adding a New Category (Advanced)

If no existing category fits your packages, create a new one:

### 1. Create a new role directory:
```bash
mkdir -p roles/new_category/tasks
```

### 2. Create `roles/new_category/tasks/main.yml`:
```yaml
---
- name: Install your new category packages
  tags: new_category
  block:
    - name: "Install packages from new_category"
      become: true
      pacman:
        name:
          - package1
          - package2
        state: present
        update_cache: "{{ pacman_update_cache }}"
      ignore_errors: true
```

### 3. Add to `playbook.yml`:
```yaml
roles:
  - role: new_category
    tags: [new_category]
```

### 4. Update README.md and documentation

---

## Summary

| Task | Where | How |
|------|-------|-----|
| Add pacman package | `roles/<category>/tasks/main.yml` | Add to list in pacman task |
| Add AUR package | `roles/<category>/tasks/main.yml` | Add to paru loop or create new task |
| Update docs | `README.md` | Add to appropriate package list |
| Test changes | Terminal | `ansible-playbook -i inventory.yml playbook.yml --tags <category>` |

That's it! Follow these guidelines and your new packages will integrate seamlessly.
