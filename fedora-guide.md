# Fedora Linux: A Comprehensive Guide for WSL Users

## Table of Contents
1. [Introduction to Fedora](#introduction-to-fedora)
2. [Fedora vs WSL: Key Differences](#fedora-vs-wsl-key-differences)
3. [Getting Started with Fedora](#getting-started-with-fedora)
4. [Package Management with DNF](#package-management-with-dnf)
5. [Understanding SELinux](#understanding-selinux)
6. [File Permissions and Ownership](#file-permissions-and-ownership)
7. [System Management and Configuration](#system-management-and-configuration)
8. [Networking in Fedora](#networking-in-fedora)
9. [Desktop Environment Customization](#desktop-environment-customization)
10. [Hardware Configuration](#hardware-configuration)
11. [Performance Tuning](#performance-tuning)
12. [Advanced Terminal Usage](#advanced-terminal-usage)
13. [Backup and Recovery](#backup-and-recovery)
14. [Virtualization and Containers](#virtualization-and-containers)
15. [Development Environment Setup](#development-environment-setup)
16. [Data Migration from Windows/WSL](#data-migration-from-windowswsl)
17. [Multimedia Setup](#multimedia-setup)
18. [Practical SELinux Scenarios](#practical-selinux-scenarios)
19. [Fedora Release Management](#fedora-release-management)
20. [Fedora Applications: Windows Equivalents and Native Apps](#fedora-applications-windows-equivalents-and-native-apps)
21. [Troubleshooting Common Issues](#troubleshooting-common-issues)
22. [Useful Resources](#useful-resources)

## Introduction to Fedora

Fedora is a Linux distribution sponsored by Red Hat, known for being at the forefront of open-source innovation. It serves as the upstream source for Red Hat Enterprise Linux (RHEL) and is often described as a bleeding-edge distribution that incorporates new technologies early.

> **For Windows Users**: An "upstream source" means that Fedora is where new features are first developed and tested before they make their way into Red Hat Enterprise Linux, a commercial distribution. Think of Fedora as the cutting-edge version where new technologies appear first.

### Fedora's Philosophy

- **Freedom**: Fedora is committed to free and open-source software
- **Innovation**: Early adoption of new technologies and standards
- **Security**: Strong focus on security features, including SELinux by default
- **Release Cycle**: New versions approximately every 6 months, with support for about 13 months

> **For Windows Users**: "Free and open-source" refers both to cost (free as in no cost) and freedom (the source code is available for anyone to view, modify, and distribute). This differs from Windows, which is proprietary software where the source code is not publicly available.

### Fedora Editions

Fedora comes in several editions tailored to specific use cases:

- **Fedora Workstation**: Desktop-oriented edition (most likely what you'll be using)
- **Fedora Server**: Designed for server environments
- **Fedora IoT**: For Internet of Things devices
- **Fedora CoreOS**: Minimal, container-focused operating system
- **Fedora Silverblue**: Immutable desktop with focus on containerized applications

> **For Windows Users**: This is similar to how Windows has different editions (Home, Pro, Enterprise), but Fedora's editions differ more significantly in their intended use cases and included software.

## Fedora vs WSL: Key Differences

Transitioning from WSL to native Fedora involves several significant changes:

### System Architecture
- **WSL**: Runs on top of Windows, with various integration features and limitations
- **Fedora**: Full native Linux operating system with direct hardware access

> **For Windows Users**: Unlike WSL which is a compatibility layer, Fedora runs directly on your hardware without Windows underneath. This means full access to all hardware features but also means you'll need to handle all hardware management yourself.

### File System
- **WSL**: Access to Windows files via `/mnt/c/` (or similar)
- **Fedora**: Native Linux file systems (typically ext4), no direct Windows filesystem access

> **For Windows Users**: ext4 is Linux's default filesystem (similar to NTFS in Windows). In Fedora, you can't directly access Windows files like in WSL - if dual-booting, you'll need to mount Windows partitions specifically.

### System Management
- **WSL**: Limited system management capabilities, reliance on Windows for hardware and service management
- **Fedora**: Full system management, including services, hardware, boot process, etc.

> **For Windows Users**: In Fedora, you'll manage everything from startup services (similar to Windows Services) to hardware drivers yourself, rather than having Windows handle it.

### Security Model
- **WSL**: Windows security model with some Linux permissions
- **Fedora**: Complete Linux security model including SELinux

> **For Windows Users**: SELinux (Security-Enhanced Linux) is an additional security layer beyond standard file permissions. Think of it like a more powerful, system-wide version of Windows Defender with very granular access controls.

### Software Availability
- **WSL**: Package management via distribution's package manager (apt for Ubuntu-based WSL)
- **Fedora**: DNF package manager with access to Fedora repositories

> **For Windows Users**: Instead of downloading .exe installers from websites, most software in Fedora is installed through the DNF package manager (similar to a command-line version of the Microsoft Store, but with many more applications).

## Getting Started with Fedora

### Basic Navigation

If you're familiar with WSL, many basic Linux commands will be the same:

```bash
# Directory navigation
ls              # List files in current directory (similar to 'dir' in Windows)
ls -la          # List all files (including hidden) with details
cd /path/to/dir # Change directory (same as Windows)
pwd             # Print working directory (shows where you are, like 'cd' without parameters in Windows)

# File operations
cp file1 file2              # Copy file1 to file2 (like 'copy' in Windows)
mv file1 file2              # Move/rename file1 to file2 (like 'move' or 'rename' in Windows)
rm file                     # Remove file (like 'del' in Windows)
mkdir directory             # Create directory (like 'mkdir' in Windows)
rmdir directory             # Remove empty directory (like 'rmdir' in Windows)
rm -rf directory            # Remove directory and contents (like 'rd /s /q' in Windows)

# Viewing files
cat file                    # Display file contents (like 'type' in Windows)
less file                   # View file contents page by page (no exact Windows equivalent)
head -n 10 file             # View first 10 lines
tail -n 10 file             # View last 10 lines
```

> **For Windows Users**: 
> - In Linux, forward slashes (`/`) are used for directories instead of backslashes (`\`)
> - File and folder names are case-sensitive (`Documents` and `documents` are different folders)
> - There are no drive letters (like C:). Instead, everything is under a single root directory (`/`)
> - Hidden files start with a dot (`.filename`) instead of having a hidden attribute

### User Management

```bash
# User information
whoami                      # Show current username (same as Windows)
id                          # Show user ID and group information

# User management (requires sudo)
sudo useradd username       # Create new user
sudo usermod -aG wheel username  # Add user to wheel group (sudo access)
sudo passwd username        # Set/change user password
```

> **For Windows Users**: 
> - `sudo` is like "Run as Administrator" in Windows, but for command-line operations
> - The `wheel` group gives users administrative privileges (similar to the Administrators group in Windows)
> - Unlike in Windows, you'll often need to prefix commands with `sudo` to perform system operations

### Getting Help

```bash
man command                 # Display manual for command (like a built-in help system)
command --help              # Display help for command
info command                # Display info documentation for command
```

> **For Windows Users**: Linux has extensive built-in documentation. The `man` (manual) pages are comprehensive references for almost every command and are the first place to look when you need help.

## Package Management with DNF

DNF (Dandified Yum) is Fedora's package manager, replacing the older YUM.

> **For Windows Users**: DNF is Fedora's equivalent to an app store or software installer. Instead of downloading .exe files from websites, you use DNF to install, update, and remove software from centralized repositories (software libraries). This approach is generally safer and more convenient than Windows' traditional software installation method.

### Basic DNF Commands

```bash
# Updating package information
sudo dnf check-update       # Check for updates (like checking for Windows updates)

# Package management
sudo dnf install package    # Install a package (like running an installer in Windows)
sudo dnf remove package     # Remove a package (like uninstall in Windows)
sudo dnf update             # Update all packages (like Windows Update)
sudo dnf update package     # Update specific package

# Package information
dnf info package            # Display information about a package
dnf search keyword          # Search for packages matching keyword
dnf provides file           # Find which package provides a file

# Repository management
dnf repolist                # List enabled repositories (sources of software)
sudo dnf config-manager --add-repo repo_url  # Add a repository
```

### Managing Software Groups

```bash
dnf group list              # List available groups
sudo dnf group install "group name"  # Install a group of packages
sudo dnf group remove "group name"   # Remove a group of packages
```

> **For Windows Users**: Software groups are collections of related packages that can be installed together, similar to feature sets in Windows optional features.

### RPM Fusion

RPM Fusion is a third-party repository that provides software not included in Fedora's repositories due to licensing or patent issues.

> **For Windows Users**: This is similar to adding a trusted third-party app store. RPM Fusion provides software that Fedora can't include by default due to licensing restrictions, similar to how some media codecs aren't included in Windows by default.

```bash
# Install RPM Fusion repositories
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### Flatpak Support

Fedora comes with Flatpak support pre-installed, allowing you to install applications in a sandboxed environment.

> **For Windows Users**: Flatpak is similar to the Microsoft Store, providing containerized applications that are isolated from the rest of the system for security. It's an alternative way to install software that offers additional security.

```bash
# Install an application from Flathub
flatpak install flathub com.application.name

# Run a Flatpak application
flatpak run com.application.name

# Update all Flatpak applications
flatpak update
```

## Understanding SELinux

### What is SELinux?

Security-Enhanced Linux (SELinux) is a security architecture integrated into the Linux kernel that provides mandatory access controls (MAC) beyond the standard discretionary access controls (DAC) of Linux file permissions.

> **For Windows Users**: SELinux is a significant difference from Windows security. Think of it as a system-wide security policy enforcer that's much more granular than Windows Defender or User Account Control (UAC). While Windows primarily relies on file permissions and user rights, SELinux adds context-based controls, meaning it cares not just about who you are, but what your program is doing and in what context.

SELinux operates on the principle of least privilege, allowing processes only the access they need to function properly.

### SELinux Modes

SELinux can operate in three modes:

- **Enforcing**: SELinux policy is enforced. Access denied by policy will be blocked.
- **Permissive**: SELinux policy is not enforced, but violations are logged (useful for troubleshooting).
- **Disabled**: SELinux is completely disabled (not recommended).

> **For Windows Users**: "Permissive mode" is somewhat like running Windows Defender in a monitoring-only mode, where it detects but doesn't block threats. "Enforcing mode" is like having Windows Defender fully active.

```bash
# Check current SELinux mode
getenforce

# Temporarily change SELinux mode to Permissive
sudo setenforce 0

# Temporarily change SELinux mode to Enforcing
sudo setenforce 1

# Permanently change SELinux mode (requires reboot)
sudo vi /etc/selinux/config
# Change SELINUX=enforcing to SELINUX=permissive
```

> **For Windows Users**: `vi` is a text editor. If you're not familiar with it, you can use `nano` instead, which is more beginner-friendly: `sudo nano /etc/selinux/config`.

### SELinux Contexts

Every file, process, and system object has an SELinux context, consisting of:

- **User**: SELinux user identity
- **Role**: SELinux role
- **Type**: SELinux type (most commonly used for policy decisions)
- **Level**: MLS/MCS security level (optional)

> **For Windows Users**: This is similar to how Windows has security descriptors for files and processes, but much more granular. The "type" is especially important - it defines what kind of object something is from a security perspective (e.g., "this is a web server file" vs "this is a system configuration").

```bash
# View file context
ls -Z file

# View process context
ps -Z

# View your current context
id -Z
```

### SELinux Booleans

Booleans are switches that change the behavior of the SELinux policy without modifying the policy itself.

> **For Windows Users**: Think of booleans like Windows Group Policy settings that can quickly enable/disable specific security features without changing the underlying security architecture.

```bash
# List all SELinux booleans
getsebool -a

# Get specific boolean
getsebool httpd_can_network_connect

# Set boolean temporarily
sudo setsebool httpd_can_network_connect on

# Set boolean permanently
sudo setsebool -P httpd_can_network_connect on
```

### Managing SELinux Contexts

```bash
# Change file context
sudo chcon -t httpd_sys_content_t /path/to/file

# Restore default context
sudo restorecon -v /path/to/file

# Recursively restore default context
sudo restorecon -Rv /path/to/directory
```

### Troubleshooting SELinux

```bash
# Install SELinux troubleshooting tools
sudo dnf install setroubleshoot setools

# View SELinux denial messages
sudo ausearch -m AVC,USER_AVC -ts recent

# Get detailed explanation and suggestions for an SELinux denial
sudo sealert -a /var/log/audit/audit.log
```

> **For Windows Users**: When things don't work in Fedora, SELinux is often involved. The tools above help diagnose issues - `sealert` is particularly useful as it gives human-readable explanations and suggested fixes, similar to how Windows troubleshooters provide solutions.

### SELinux and Services

Many services have specific SELinux contexts and policies. When you encounter permission issues, check if SELinux is blocking the action:

```bash
# View SELinux alerts in the system log
sudo journalctl -t setroubleshoot
```

## File Permissions and Ownership

### Standard Linux Permissions

Linux uses a combination of user, group, and other permissions:

```bash
# View file permissions
ls -l file

# Change file permissions
chmod 755 file        # Numeric method: rwxr-xr-x
chmod u+rwx,g+rx,o+rx file  # Symbolic method

# Change file ownership
chown user:group file
```

> **For Windows Users**:
> - Linux permissions are more visible and directly editable than in Windows
> - The numeric method (like 755) is a shorthand where:
>   - First digit (7) = Owner permissions (4=read, 2=write, 1=execute)
>   - Second digit (5) = Group permissions (4+1=read+execute)
>   - Third digit (5) = Others permissions (4+1=read+execute)
> - So 755 means "owner can read/write/execute, group and others can read/execute"
> - This is similar to but more granular than Windows NTFS permissions

### SELinux and Permissions

With SELinux, both standard Linux permissions AND SELinux contexts must allow an action for it to succeed:

1. Standard Linux permissions are checked first
2. If standard permissions allow access, SELinux permissions are checked
3. Access is granted only if both allow it

> **For Windows Users**: This dual permission system is a major difference from Windows. In Windows, if you have NTFS permissions to access a file, you can access it. In Fedora with SELinux, you need both traditional file permissions AND the correct SELinux context. This is why you might set file permissions correctly but still be denied access.

This is why you might have correct standard permissions but still be denied access due to SELinux.

### Common Permission Issues with SELinux

- **Web server cannot access files**: Files need the correct context, typically `httpd_sys_content_t`
- **Service cannot write to a directory**: Directory may need a writable context for that service
- **Application cannot connect to network**: Boolean may need to be enabled

> **For Windows Users**: When troubleshooting permissions issues in Fedora, always check both standard permissions and SELinux contexts. This is different from Windows where checking NTFS permissions is usually sufficient.

## System Management and Configuration

### Systemd Service Management

Fedora uses systemd for service management:

> **For Windows Users**: Systemd is similar to the Windows Service Control Manager, but more powerful. It handles not just services but also the boot process and other system initialization. Think of it as combining Windows Services, Task Scheduler, and boot manager in one system.

```bash
# Start a service
sudo systemctl start service

# Stop a service
sudo systemctl stop service

# Enable service to start at boot
sudo systemctl enable service

# Disable service at boot
sudo systemctl disable service

# Check service status
systemctl status service

# Restart service
sudo systemctl restart service

# Reload service configuration
sudo systemctl reload service

# List all services
systemctl list-units --type=service
```

### System Information

```bash
# System information
hostnamectl                  # System and hostname info
uname -a                     # Kernel information
cat /etc/fedora-release      # Fedora version

# Hardware information
lscpu                        # CPU information
free -h                      # Memory information
df -h                        # Disk usage
lsblk                        # Block devices
lspci                        # PCI devices
lsusb                        # USB devices
```

> **For Windows Users**: These commands provide information similar to what you'd find in Windows Device Manager, System Information, and Task Manager, but via the command line.

### System Logs

```bash
# View system logs with journalctl
journalctl                   # All logs
journalctl -b                # Logs from current boot
journalctl -u service        # Logs for specific service
journalctl -f                # Follow logs in real-time

# Traditional log files
ls /var/log/                 # Log directory
```

> **For Windows Users**: Journalctl is like a command-line version of Windows Event Viewer. It centralizes logs from all system components, making it easier to diagnose problems. Traditional log files in `/var/log/` are also available, similar to how Windows has both Event Viewer and individual log files.

### System Updates and Upgrades

```bash
# Update your system
sudo dnf update

# Upgrade to a new Fedora version
sudo dnf system-upgrade download --releasever=XX  # XX is version number
sudo dnf system-upgrade reboot
```

> **For Windows Users**: Unlike Windows where feature updates are installed through Windows Update, in Fedora you use specific commands to upgrade to a new version. The process is more explicit and controlled.

## Networking in Fedora

### Network Configuration

Fedora uses NetworkManager for network management:

```bash
# Network information
ip addr                     # Show IP addresses
ip route                    # Show routing table
nmcli device show           # Show NetworkManager devices
nmcli connection show       # Show connections

# Configure network with NetworkManager
nmcli connection modify "connection-name" ipv4.addresses "192.168.1.100/24"
nmcli connection modify "connection-name" ipv4.gateway "192.168.1.1"
nmcli connection modify "connection-name" ipv4.dns "8.8.8.8"
nmcli connection up "connection-name"
```

### Firewall Management with firewalld

Fedora uses firewalld for firewall management:

```bash
# Check firewall status
sudo firewall-cmd --state

# List open ports/services
sudo firewall-cmd --list-all

# Open a port
sudo firewall-cmd --add-port=80/tcp --permanent

# Open a service
sudo firewall-cmd --add-service=http --permanent

# Reload firewall configuration
sudo firewall-cmd --reload
```

### SELinux Network Controls

SELinux has policies for network access. Common booleans:

```bash
# Allow httpd to connect to network
sudo setsebool -P httpd_can_network_connect on

# Allow specific service to connect to specific port
sudo setsebool -P service_name_connect_port on
```

## Desktop Environment Customization

Fedora Workstation ships with GNOME as the default desktop environment, but you can customize it extensively or even switch to alternatives.

### GNOME Customization

#### GNOME Extensions

GNOME Extensions add functionality to your desktop:

1. **Install the browser integration**:
   ```bash
   sudo dnf install chrome-gnome-shell
   ```

2. **Visit [extensions.gnome.org](https://extensions.gnome.org)** in Firefox or Chrome

3. **Recommended extensions**:
   - **Dash to Dock/Panel**: Converts the dash into a more traditional dock
   - **AppIndicator Support**: Shows system tray icons
   - **Sound Input & Output Device Chooser**: Quickly switch audio devices
   - **GSConnect**: KDE Connect integration for GNOME
   - **Caffeine**: Prevents screensaver/suspend

4. **Manage extensions**: Use the Extensions app (pre-installed)

#### GNOME Tweaks

GNOME Tweaks allows deeper customization:

```bash
# Install GNOME Tweaks
sudo dnf install gnome-tweaks
```

Using Tweaks, you can:
- Change themes and icons
- Adjust fonts
- Modify window behaviors
- Configure keyboard shortcuts
- Customize the top bar

#### GTK and Shell Themes

Change the look of your desktop:

```bash
# Install theme packages
sudo dnf install arc-theme papirus-icon-theme

# Create themes directory (if it doesn't exist)
mkdir -p ~/.themes ~/.icons
```

Apply themes using GNOME Tweaks.

#### Essential GNOME Keyboard Shortcuts

| Shortcut | Action | Windows Equivalent |
|----------|--------|-------------------|
| Ctrl + Alt + T | Open terminal | None (PowerShell/CMD shortcuts) |
| Super (Windows Key) | Activities overview | Windows key (Start menu) |
| Super + A | Applications menu | Windows key + Q (Search) |
| Super + Tab | Switch between applications | Alt + Tab |
| Super + ` | Switch between windows of the same application | Alt + ` (less common in Windows) |
| Super + Arrow Keys | Snap windows to edges | Windows key + Arrow keys |
| Super + L | Lock screen | Windows key + L |
| Super + V | Notification center | Windows key + A |
| Alt + F2 | Run command dialog | Windows key + R |
| Alt + F4 | Close window | Alt + F4 |
| Alt + Tab | Switch between windows | Alt + Tab |
| Ctrl + Alt + Down/Up | Switch workspaces | None (Virtual Desktops in Windows 10+) |
| Ctrl + Alt + Shift + Arrow | Move window to another workspace | Windows key + Ctrl + Arrow (Windows 10+) |
| Super + Space | Switch keyboard layouts | Alt + Shift |
| Print Screen | Take screenshot of entire screen | Print Screen |
| Alt + Print Screen | Take screenshot of current window | Alt + Print Screen |
| Shift + Print Screen | Take screenshot of selected area | Windows key + Shift + S |
| Ctrl + Alt + Backspace | Force restart X Server (emergency use) | Ctrl + Alt + Delete (Task Manager) |
| Ctrl + Alt + F3-F6 | Switch to text console | None |
| Ctrl + Alt + F2 | Return to graphical interface | None |

> **For Windows Users**: 
> - The "Super" key is the same as the Windows key on your keyboard 
> - Opening a terminal with Ctrl+Alt+T is extremely useful and has no direct Windows equivalent
> - Workspaces (virtual desktops) are more prominent in Linux and used frequently
> - You can customize these shortcuts in Settings → Keyboard → Keyboard Shortcuts

### Alternative Desktop Environments

If GNOME doesn't suit your needs, Fedora supports several alternatives:

```bash
# Install KDE Plasma
sudo dnf group install "KDE Plasma Workspaces"

# Install Xfce
sudo dnf group install "Xfce Desktop"

# Install MATE
sudo dnf group install "MATE Desktop"

# Install Cinnamon
sudo dnf group install "Cinnamon Desktop"
```

Switch between desktop environments at the login screen by clicking the gear icon.

## Hardware Configuration

### Graphics Drivers

#### NVIDIA Drivers

Fedora doesn't include proprietary NVIDIA drivers by default. To install:

1. **Enable RPM Fusion** (if not already done):
   ```bash
   sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
   ```

2. **Install NVIDIA drivers**:
   ```bash
   # Install NVIDIA drivers
   sudo dnf install akmod-nvidia
   
   # For CUDA support
   sudo dnf install xorg-x11-drv-nvidia-cuda
   ```

3. **Reboot your system**:
   ```bash
   sudo reboot
   ```

4. **Verify installation**:
   ```bash
   nvidia-smi
   ```

#### AMD Drivers

AMD drivers are generally included in the Linux kernel. For newer GPUs, you might need the latest Mesa:

```bash
# Update Mesa
sudo dnf update mesa
```

### Bluetooth Configuration

```bash
# Install Bluetooth tools
sudo dnf install bluez bluez-tools

# Start and enable Bluetooth service
sudo systemctl start bluetooth
sudo systemctl enable bluetooth

# Open Bluetooth graphical tool
gnome-control-center bluetooth
```

Command-line Bluetooth management:

```bash
# List Bluetooth devices
bluetoothctl devices

# Scan for devices
bluetoothctl scan on

# Pair with a device
bluetoothctl pair [MAC_ADDRESS]

# Connect to a paired device
bluetoothctl connect [MAC_ADDRESS]
```

### Printer Setup

Fedora uses CUPS for printer management:

```bash
# Install printing support
sudo dnf install cups system-config-printer

# Ensure CUPS is running
sudo systemctl start cups
sudo systemctl enable cups

# Open printer configuration
system-config-printer
```

For network printers, they should be automatically discovered. For USB printers, connect them and they should be detected automatically.

### Power Management

```bash
# Install power management tools
sudo dnf install tlp tlp-rdw

# Start and enable TLP
sudo systemctl start tlp
sudo systemctl enable tlp

# Check power consumption
sudo powertop
```

Modify power settings through GNOME Settings or with TLP configuration:

```bash
# Edit TLP configuration
sudo nano /etc/tlp.conf
```

### Display Configuration

```bash
# Show display information
xrandr

# Set resolution
xrandr --output HDMI-1 --mode 1920x1080

# Set refresh rate
xrandr --output HDMI-1 --rate 60

# Configure multiple displays
xrandr --output HDMI-1 --primary --output DP-1 --right-of HDMI-1
```

GNOME provides a graphical Display settings tool in the Settings app.

## Performance Tuning

### System Monitoring

Monitor system performance with these tools:

```bash
# Basic system monitoring
top                  # Interactive process viewer
htop                 # Enhanced interactive process viewer
sudo dnf install htop

# Graphics monitoring
sudo dnf install glances   # More comprehensive monitoring
glances

# Disk usage
df -h                # Disk space usage
du -sh /path         # Size of directory
ncdu                 # Interactive disk usage analyzer
sudo dnf install ncdu

# I/O monitoring
sudo dnf install iotop
sudo iotop           # Monitor disk I/O

# Network monitoring
sudo dnf install iftop
sudo iftop           # Monitor network usage

# Comprehensive monitoring
sudo dnf install sysstat
sar                  # System activity reporter
```

> **For Windows Users**: 
> - `top` and `htop` are similar to Task Manager in Windows, but in the terminal
> - `df` and `du` show disk usage information like Windows Explorer properties
> - `ncdu` is like a text-based WinDirStat for analyzing disk usage
> - `iotop` shows disk activity similar to Resource Monitor's Disk tab
> - `iftop` displays network usage similar to Resource Monitor's Network tab

### Startup Applications

Manage applications that run at startup:

1. **Using GNOME Tweaks**:
   - Open GNOME Tweaks
   - Go to "Startup Applications"
   - Add or remove applications

2. **Using command line**:
   ```bash
   # System startup services
   systemctl list-unit-files --type=service --state=enabled
   
   # Disable a service
   sudo systemctl disable service-name
   
   # User startup applications
   ls ~/.config/autostart/
   ```

> **For Windows Users**: Managing startup applications in Fedora is similar to using the Startup tab in Task Manager, but with more options. System services are like Windows Services, while user startup applications are like the programs in your Startup folder.

### Swappiness

Adjust how aggressively the system uses swap:

```bash
# Check current swappiness
cat /proc/sys/vm/swappiness

# Set swappiness temporarily (lower values use swap less aggressively)
sudo sysctl vm.swappiness=10

# Set permanently
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.d/99-swappiness.conf
```

> **For Windows Users**: "Swappiness" controls how aggressively Linux uses swap space (virtual memory). It's similar to configuring virtual memory in Windows, but more granular. Lower values (like 10) make the system prefer keeping processes in RAM, which can improve responsiveness but might use more physical memory.

### CPU Frequency Scaling

Control CPU power settings:

```bash
# Install cpupower
sudo dnf install kernel-tools

# Show current CPU frequency settings
sudo cpupower frequency-info

# Set CPU governor
sudo cpupower frequency-set -g performance  # Maximum performance
sudo cpupower frequency-set -g powersave    # Power saving
```

> **For Windows Users**: This is similar to Power Plans in Windows, but with more direct control. The "performance" governor is like Windows "High performance" plan, while "powersave" is similar to "Power saver".

### Filesystem Optimizations

1. **Disable access time updates** (reduces disk I/O):
   ```bash
   sudo nano /etc/fstab
   # Add 'noatime' option to mount options
   ```

2. **Use faster filesystem for specific workloads**:
   ```bash
   # For SSD, use TRIM
   sudo systemctl enable fstrim.timer
   ```

> **For Windows Users**: These optimizations are similar to defragmentation and TRIM operations in Windows, but configured differently. `fstrim` for SSDs is equivalent to Windows' automatic TRIM, ensuring efficient SSD operation.

### Memory Management

```bash
# Clear cache
sudo sh -c "echo 3 > /proc/sys/vm/drop_caches"

# Install earlyoom to prevent system freezes due to memory exhaustion
sudo dnf install earlyoom
sudo systemctl enable --now earlyoom
```

> **For Windows Users**: `drop_caches` manually frees cached memory, similar to using RAM cleaners in Windows (though those are generally not recommended). The "earlyoom" tool prevents complete system freezes when memory runs out by selectively terminating processes, which Windows does through its own memory management.

### Preload Frequently Used Applications

```bash
# Install preload
sudo dnf install preload
sudo systemctl enable --now preload
```

> **For Windows Users**: Preload is like Windows Superfetch/Prefetch, which learns which applications you use frequently and loads parts of them into memory in advance to improve launch times.

## Advanced Terminal Usage

### Terminal Multiplexers

#### Tmux

```bash
# Install tmux
sudo dnf install tmux

# Basic tmux commands
tmux                   # Start a new session
tmux new -s name       # Start a new named session
tmux ls                # List sessions
tmux a                 # Attach to last session
tmux a -t name         # Attach to named session
```

Inside tmux:
- `Ctrl+b c`: Create new window
- `Ctrl+b p` or `Ctrl+b n`: Previous/next window
- `Ctrl+b %`: Split vertically
- `Ctrl+b "`: Split horizontally
- `Ctrl+b arrow`: Navigate between panes
- `Ctrl+b d`: Detach from session

> **For Windows Users**: Terminal multiplexers like tmux have no direct Windows equivalent. Think of them as tabbed command prompts with split-screen capabilities and the ability to disconnect and reconnect without losing your work - similar to Remote Desktop sessions that persist after you disconnect.

#### Screen

```bash
# Install screen
sudo dnf install screen

# Basic screen commands
screen              # Start a new session
screen -S name      # Start a new named session
screen -ls          # List sessions
screen -r           # Resume last session
screen -r name      # Resume named session
```

Inside screen:
- `Ctrl+a c`: Create new window
- `Ctrl+a p` or `Ctrl+a n`: Previous/next window
- `Ctrl+a S`: Split horizontally
- `Ctrl+a |`: Split vertically
- `Ctrl+a tab`: Switch between splits
- `Ctrl+a d`: Detach from session

### Shell Customization

#### Bash Configuration

Customize your bash prompt and functionality:

```bash
# Edit bash configuration
nano ~/.bashrc

# Example PS1 (prompt) customization
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '

# Add useful aliases
alias ll='ls -la'
alias update='sudo dnf update'
```

> **For Windows Users**: This is similar to customizing PowerShell with a profile, but more commonly used in Linux. The `~/.bashrc` file runs each time you open a terminal, allowing you to set up your environment.

#### Zsh and Oh My Zsh

```bash
# Install Zsh
sudo dnf install zsh

# Set as default shell
chsh -s $(which zsh)

# Install Oh My Zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Edit Zsh configuration
nano ~/.zshrc
```

> **For Windows Users**: Zsh is an alternative shell with more features than the default Bash. Oh My Zsh adds themes and plugins, making it more visually appealing and functional - think of it as a shell with more custom options and visual enhancements than the standard Command Prompt or PowerShell.

#### Fish Shell

```bash
# Install Fish
sudo dnf install fish

# Set as default shell
chsh -s $(which fish)

# Start the configuration web interface
fish_config
```

### Command-Line Productivity Tools

```bash
# Install useful utilities
sudo dnf install \
  bat          `# Better cat` \
  exa          `# Better ls` \
  fd-find      `# Better find` \
  ripgrep      `# Better grep` \
  fzf          `# Fuzzy finder` \
  tldr         `# Simplified man pages` \
  ranger       `# File manager` \
  mc           `# Midnight Commander file manager` \
  neofetch     `# System info display` \
  jq           `# JSON processor` \
  progress     `# Show progress of coreutils commands`
```

> **For Windows Users**: These tools add functionality to the standard Linux command line utilities. They're like enhanced versions of basic commands, similar to how PowerShell cmdlets enhance the standard Windows command line.

Example usage:

```bash
# Replace cat with bat
bat file.txt

# Replace ls with exa
exa --long --header --git
exa --tree --level=2

# Replace find with fd
fd pattern

# Replace grep with ripgrep
rg pattern

# Fuzzy searching history
Ctrl+R   # With fzf installed

# Browse files
ranger

# Get quick command examples
tldr tar
```

### Terminal Text Editors

```bash
# Install common text editors
sudo dnf install nano vim neovim emacs

# Set default editor
echo 'export EDITOR=nano' >> ~/.bashrc  # Replace nano with your preference
```

> **For Windows Users**: 
> - `nano` is the simplest editor (similar to Notepad)
> - `vim` is powerful but has a steeper learning curve
> - `neovim` is a modern version of vim
> - `emacs` is a highly extensible editor (and much more)
> 
> These editors all run in the terminal, unlike most Windows text editors which have graphical interfaces.

Basic Vim usage:
- `i`: Enter insert mode
- `Esc`: Exit insert mode
- `:w`: Save
- `:q`: Quit
- `:wq`: Save and quit
- `:q!`: Quit without saving

### SSH Configuration

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Copy public key to server
ssh-copy-id user@hostname

# Configure SSH
nano ~/.ssh/config
```

> **For Windows Users**: SSH in Linux is similar to PuTTY or OpenSSH in Windows, but more integrated into the system. It's the standard way to connect to remote servers securely and can be configured with a consistent interface across all Linux systems.

## Backup and Recovery

### System Snapshots with Timeshift

Timeshift creates system snapshots that can be restored if something breaks:

```bash
# Install Timeshift
sudo dnf install timeshift

# Launch Timeshift (GUI)
sudo timeshift-gtk

# Command-line operations
sudo timeshift --create --comments "Before system update"
sudo timeshift --list
sudo timeshift --restore --snapshot '2023-01-01_00-00-00'
```

> **For Windows Users**: Timeshift is similar to System Restore in Windows, but more comprehensive. It takes snapshots of your entire system that can be restored if something goes wrong. It's especially useful before major system changes or updates.

### File Backup Solutions

#### Déjà Dup (Simple)

```bash
# Install Déjà Dup
sudo dnf install deja-dup

# Launch from Applications menu or CLI
deja-dup
```

> **For Windows Users**: Déjà Dup is a simple backup tool with a graphical interface, similar to Windows Backup but with support for encrypted backups and cloud storage.

#### Restic (Advanced)

```bash
# Install Restic
sudo dnf install restic

# Initialize repository
restic init --repo /path/to/backup/location

# Create backup
restic -r /path/to/backup/location backup /home/user/Documents

# List snapshots
restic -r /path/to/backup/location snapshots

# Restore
restic -r /path/to/backup/location restore latest --target /path/to/restore
```

> **For Windows Users**: Restic is a powerful command-line backup tool with features like encryption, deduplication, and compression. It's more similar to enterprise backup solutions than consumer Windows tools, but with a simpler interface.

#### Rsync (Command-line)

```bash
# Basic backup
rsync -av --delete /source/directory/ /destination/directory/

# With compression
rsync -avz --delete /source/directory/ /destination/directory/

# To remote server
rsync -avz --delete /source/directory/ user@remote:/destination/directory/
```

> **For Windows Users**: Rsync is a powerful file synchronization tool with no direct Windows equivalent (though tools like Robocopy have some similar features). The `--delete` flag makes it remove files in the destination that are no longer in the source, creating a true mirror.

### Automated Backups

Using systemd timers for automated backups:

1. **Create a backup script**:
   ```bash
   nano ~/scripts/backup.sh
   chmod +x ~/scripts/backup.sh
   ```

2. **Create a systemd service**:
   ```bash
   nano ~/.config/systemd/user/backup.service
   ```
   
   Content:
   ```
   [Unit]
   Description=Backup important files
   
   [Service]
   Type=simple
   ExecStart=/home/username/scripts/backup.sh
   ```

3. **Create a systemd timer**:
   ```bash
   nano ~/.config/systemd/user/backup.timer
   ```
   
   Content:
   ```
   [Unit]
   Description=Run backup daily
   
   [Timer]
   OnCalendar=daily
   Persistent=true
   
   [Install]
   WantedBy=timers.target
   ```

4. **Enable the timer**:
   ```bash
   systemctl --user enable backup.timer
   systemctl --user start backup.timer
   ```

> **For Windows Users**: This method uses systemd timers, which are similar to Windows Task Scheduler but integrated into the Linux init system. It's a powerful way to schedule tasks with precise timing options.

### Recovery Options

#### Rescue Mode

1. **Boot into rescue mode**:
   - Interrupt the boot process and select "Troubleshooting" from the GRUB menu
   - Choose "Rescue a Fedora System"

2. **Fix common issues**:
   ```bash
   # Fix file system issues
   fsck /dev/sdaX
   
   # Mount root file system
   mount /dev/sdaX /mnt
   
   # Chroot into the system
   chroot /mnt
   ```

> **For Windows Users**: 
> - This is similar to using Windows Recovery Environment, but more powerful and flexible
> - `fsck` is like running `chkdsk` in Windows
> - `chroot` lets you "enter" your installed system even when booted from recovery media - there's no direct Windows equivalent

#### Boot Repair

```bash
# Install boot repair
sudo dnf install grub2-tools os-prober

# Rebuild GRUB configuration
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# Reinstall GRUB (for BIOS)
sudo grub2-install /dev/sda

# Reinstall GRUB (for UEFI)
sudo grub2-install --target=x86_64-efi --efi-directory=/boot/efi
```

> **For Windows Users**: These tools repair the GRUB bootloader, which is Linux's equivalent to the Windows Boot Manager. If your system won't boot, these commands can often fix it, similar to using `bootrec /fixmbr` or `bootrec /rebuildbcd` in Windows Recovery.

## Virtualization and Containers

### Virtual Machines with GNOME Boxes

GNOME Boxes provides a simple way to manage virtual machines:

```bash
# Install GNOME Boxes
sudo dnf install gnome-boxes
```

Launch from the Applications menu and follow the GUI to create and manage VMs.

> **For Windows Users**: GNOME Boxes is similar to Hyper-V or VirtualBox on Windows, but with a simpler interface. It lets you create and run virtual machines with other operating systems inside Fedora.

### KVM/QEMU for Advanced Virtualization

```bash
# Install virtualization packages
sudo dnf group install --with-optional virtualization

# Start and enable libvirtd
sudo systemctl start libvirtd
sudo systemctl enable libvirtd

# Install virt-manager (GUI)
sudo dnf install virt-manager
```

> **For Windows Users**: KVM/QEMU with virt-manager is Fedora's equivalent to Hyper-V Manager in Windows, but with more features. It's the professional-grade virtualization platform built into the Linux kernel.

Key virtualization commands:

```bash
# List virtual machines
virsh list --all

# Start VM
virsh start vm-name

# Stop VM
virsh shutdown vm-name

# Force stop
virsh destroy vm-name

# Edit VM configuration
virsh edit vm-name
```

### Docker/Podman Containers

Fedora uses Podman as a Docker-compatible container engine:

> **For Windows Users**: Containers are lightweight alternatives to virtual machines. If you're familiar with Docker in Windows, Podman works similarly but doesn't require a daemon running in the background. Think of containers as super-efficient application packaging that includes all dependencies.

```bash
# Install Podman
sudo dnf install podman podman-docker

# Pull an image
podman pull fedora

# Run a container
podman run -it fedora bash

# List containers
podman ps -a

# Build from Dockerfile
podman build -t my-image .

# Docker compatibility
alias docker=podman
```

For Docker (instead of Podman):

```bash
# Install Docker
sudo dnf install docker-ce
sudo systemctl start docker
sudo systemctl enable docker

# Add user to docker group
sudo usermod -aG docker $USER
# Log out and back in
```

### Docker Compose

```bash
# Install Docker Compose
sudo dnf install docker-compose-plugin

# For Podman
sudo dnf install podman-compose

# Run docker-compose
docker compose up -d

# For Podman
podman-compose up -d
```

> **For Windows Users**: Docker Compose is a tool for defining and running multi-container applications, similar to how you might use application service definitions in Windows environments.

### LXC/LXD Containers

```bash
# Install LXC
sudo dnf install lxc lxc-templates lxc-extra

# Start and enable LXC service
sudo systemctl start lxc
sudo systemctl enable lxc

# Create container
sudo lxc-create -t fedora -n my-container

# Start container
sudo lxc-start -n my-container

# Access container
sudo lxc-attach -n my-container

# Stop container
sudo lxc-stop -n my-container
```

> **For Windows Users**: LXC/LXD containers are another container technology, somewhat like Docker but more focused on system containers (running entire operating systems) rather than application containers.

## Development Environment Setup

### General Development Tools

```bash
# Install development tools
sudo dnf group install "Development Tools"

# Install common build dependencies
sudo dnf install cmake automake libtool pkgconf
```

### Python Development

```bash
# Install Python and development tools
sudo dnf install python3 python3-devel python3-pip python3-virtualenv

# Set up a virtual environment
python3 -m venv myenv
source myenv/bin/activate

# Install packages
pip install package-name

# Create requirements.txt
pip freeze > requirements.txt

# Install from requirements
pip install -r requirements.txt
```

### Java Development

```bash
# Install OpenJDK
sudo dnf install java-17-openjdk java-17-openjdk-devel

# Set Java home
echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk' >> ~/.bashrc
source ~/.bashrc

# Install Maven
sudo dnf install maven

# Install Gradle
sudo dnf install gradle
```

### Node.js Development

```bash
# Install Node.js and npm
sudo dnf install nodejs npm

# Install nvm (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
source ~/.bashrc

# Install specific Node.js version with nvm
nvm install 18
nvm use 18

# Install Yarn
npm install -g yarn
```

### Go Development

```bash
# Install Go
sudo dnf install golang

# Set up Go workspace
mkdir -p ~/go/{bin,src,pkg}
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export PATH=$PATH:$GOPATH/bin' >> ~/.bashrc
source ~/.bashrc
```

### C/C++ Development

```bash
# Install GCC, G++, and development tools
sudo dnf group install "C Development Tools and Libraries"

# Install Clang
sudo dnf install clang clang-tools-extra

# Install debugger
sudo dnf install gdb
```

### Database Setup

#### PostgreSQL

```bash
# Install PostgreSQL
sudo dnf install postgresql postgresql-server

# Initialize database
sudo postgresql-setup --initdb

# Start and enable service
sudo systemctl start postgresql
sudo systemctl enable postgresql

# Create database user
sudo -u postgres createuser --interactive
```

#### MySQL/MariaDB

```bash
# Install MariaDB
sudo dnf install mariadb mariadb-server

# Start and enable service
sudo systemctl start mariadb
sudo systemctl enable mariadb

# Secure installation
sudo mysql_secure_installation

# Access MySQL
mysql -u root -p
```

#### SQLite

```bash
# Install SQLite
sudo dnf install sqlite
```

### Git Configuration

```bash
# Install Git
sudo dnf install git

# Configure user
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Configure editor
git config --global core.editor "nano"

# Generate SSH key for GitHub/GitLab
ssh-keygen -t ed25519 -C "your.email@example.com"
cat ~/.ssh/id_ed25519.pub
# Add this key to your GitHub/GitLab account
```

### IDE Installation

```bash
# Visual Studio Code
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
sudo dnf install code

# JetBrains Toolbox
# Download and install from JetBrains website

# Eclipse
sudo dnf install eclipse-platform
```

## Data Migration from Windows/WSL

### Transferring Files from Windows/WSL

#### Using External Storage

1. **Format a USB drive or external hard drive with a file system both Windows and Linux can read (NTFS, exFAT)**

2. **From Windows**:
   - Copy files to the external drive

3. **From Fedora**:
   ```bash
   # Mount the drive (if not automounted)
   sudo mount /dev/sdX1 /mnt

   # Copy files
   cp -r /mnt/path/to/files ~/destination
   ```

#### Using Network Shares

1. **Create a network share on Windows**:
   - Right-click a folder -> Properties -> Sharing -> Share

2. **Mount from Fedora**:
   ```bash
   # Install Samba client
   sudo dnf install samba-client cifs-utils

   # Create mount point
   mkdir ~/windows-share

   # Mount the share
   sudo mount -t cifs //WINDOWS-PC/share ~/windows-share -o username=windows-username
   ```

3. **Create an entry in fstab for automatic mounting**:
   ```bash
   echo '//WINDOWS-PC/share /home/user/windows-share cifs credentials=/home/user/.smbcredentials,uid=1000,gid=1000 0 0' | sudo tee -a /etc/fstab
   ```

#### Using SCP/SFTP

1. **Install SSH server on Windows** (OpenSSH or similar)

2. **From Fedora**:
   ```bash
   # Copy from Windows to Fedora
   scp -r windows-user@windows-ip:/path/to/files ~/destination

   # Use SFTP for interactive transfer
   sftp windows-user@windows-ip
   ```

### Accessing Windows Partitions

If you have a dual-boot setup with Windows:

```bash
# Install NTFS support
sudo dnf install ntfs-3g

# List partitions
sudo fdisk -l

# Mount Windows partition
sudo mount /dev/sdXY /mnt
```

Create an entry in fstab for automatic mounting:

```bash
echo 'UUID=XXXX-XXXX /media/windows ntfs defaults 0 0' | sudo tee -a /etc/fstab
```

Replace `XXXX-XXXX` with the actual UUID, which you can find using `blkid`.

### Importing Browser Profiles

#### Firefox

1. **In Windows Firefox**: 
   - Navigate to `about:support`
   - Find "Profile Directory" and click "Open Directory"
   - Copy the entire profile folder

2. **In Fedora Firefox**:
   - Navigate to `about:profiles`
   - Create a new profile
   - Close Firefox
   - Replace the new profile folder with the Windows profile
   - Restart Firefox

#### Chrome/Chromium

1. **In Windows Chrome**:
   - Navigate to `%LOCALAPPDATA%\Google\Chrome\User Data\`
   - Copy the "Default" folder

2. **In Fedora Chrome/Chromium**:
   - Navigate to `~/.config/google-chrome/` or `~/.config/chromium/`
   - Replace the "Default" folder with the Windows one
   - Restart Chrome/Chromium

### Email Import

#### Thunderbird

1. **In Windows Thunderbird**:
   - Go to `Help > Troubleshooting Information`
   - Find "Profile Directory" and click "Open Directory"
   - Back up the entire profile folder

2. **In Fedora Thunderbird**:
   - Close Thunderbird
   - Replace the profile at `~/.thunderbird/` with your Windows profile
   - Start Thunderbird

#### Evolution

Import from Outlook PST files:

```bash
# Install readpst
sudo dnf install libpst

# Convert PST to mbox
readpst -o /path/to/output outlook.pst

# Import the mbox files in Evolution
```

## Multimedia Setup

### Audio Configuration

```bash
# Install PulseAudio tools
sudo dnf install pavucontrol pulseaudio-utils

# Launch PulseAudio Volume Control
pavucontrol

# List audio devices
pactl list sinks short
pactl list sources short

# Set default output device
pactl set-default-sink [device-name]
```

### Installing Media Codecs

Fedora doesn't include some proprietary codecs by default. To install them:

```bash
# Enable RPM Fusion if not already done
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# Install multimedia codecs
sudo dnf install gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-bad-freeworld gstreamer1-plugins-ugly gstreamer1-plugin-libav

# Install hardware acceleration for Intel
sudo dnf install libva libva-intel-driver intel-media-driver

# For AMD
sudo dnf install libva libva-mesa-driver mesa-vdpau-drivers

# For NVIDIA
sudo dnf install libva libva-nvidia-driver vdpauinfo
```

### Video Acceleration

Check if hardware acceleration is working:

```bash
# Install tools
sudo dnf install libva-utils vdpauinfo

# Check VAAPI status
vainfo

# Check VDPAU status
vdpauinfo
```

Configure Firefox for hardware acceleration:

1. Navigate to `about:config`
2. Set `media.ffmpeg.vaapi.enabled` to `true`
3. Set `media.navigator.mediadatadecoder_vpx_enabled` to `true`

### Setting Up Media Players

```bash
# Install popular media players
sudo dnf install vlc mpv celluloid

# Install browser plugins
sudo dnf install mozilla-openh264 ffmpeg-libs
```

## Practical SELinux Scenarios

### Scenario 1: Web Server Document Root

Problem: Apache can't access files in a custom document root.

Solution:

```bash
# Check current context
ls -Z /path/to/custom/docroot

# Set the correct context
sudo semanage fcontext -a -t httpd_sys_content_t "/path/to/custom/docroot(/.*)?"
sudo restorecon -Rv /path/to/custom/docroot

# If files need to be writable by Apache
sudo semanage fcontext -a -t httpd_sys_rw_content_t "/path/to/custom/docroot/writable(/.*)?"
sudo restorecon -Rv /path/to/custom/docroot/writable
```

### Scenario 2: Custom Service Port

Problem: You need to run a service on a non-standard port.

Solution:

```bash
# List current port types
sudo semanage port -l | grep http

# Add the custom port to http_port_t
sudo semanage port -a -t http_port_t -p tcp 8080

# Verify the change
sudo semanage port -l | grep http
```

### Scenario 3: Home Directory Service Access

Problem: A service needs to access files in your home directory.

Solution:

```bash
# Option 1: Label the specific files with the correct context
sudo chcon -t service_home_t /home/user/service-files

# Option 2: Create a boolean to allow service to access home directories
sudo setsebool -P service_allow_home_dir 1
```

### Scenario 4: NFS Mount Issues

Problem: SELinux is preventing access to NFS mounts.

Solution:

```bash
# Allow NFS to be mountable
sudo setsebool -P nfs_export_all_rw 1

# Allow NFS file sharing
sudo setsebool -P nfs_export_all_ro 1

# If you need to mount with specific context
mount -o context=system_u:object_r:nfs_t:s0 server:/share /mnt
```

### Scenario 5: Docker/Container Access to Host Files

Problem: Containers can't access mounted host directories.

Solution:

```bash
# For Docker/Podman with SELinux
# When mounting volumes
podman run -v /host/path:/container/path:Z ...

# The :Z option relabels the content for private container use

# Allow container access to specific host directories
chcon -Rt container_file_t /host/path/for/containers
```

### Scenario 6: Troubleshooting SELinux Denials

Process:

1. **Check for denials**:
   ```bash
   sudo ausearch -m AVC,USER_AVC -ts recent
   ```

2. **Get detailed explanations**:
   ```bash
   sudo sealert -a /var/log/audit/audit.log
   ```

3. **Follow recommended solutions in sealert output**:
   - Common solutions include:
     - Setting booleans
     - Changing file contexts
     - Adding ports to SELinux types

4. **Create a custom policy module** (advanced):
   ```bash
   # Generate a policy module from denials
   ausearch -m AVC -ts recent | audit2allow -M my-module
   
   # Install the module
   semodule -i my-module.pp
   ```

### Scenario 7: Enabling httpd to Send Email

Problem: Your web application needs to send emails, but SELinux is blocking it.

Solution:
```bash
# Allow httpd to sendmail
sudo setsebool -P httpd_can_sendmail 1

# Or for connecting to an SMTP server
sudo setsebool -P httpd_can_network_connect 1
```

### Scenario 8: Allowing SSH on Non-Standard Port 

Problem: You've configured SSH to run on a non-standard port but SELinux is blocking it.

Solution:
```bash
# Add the custom port to ssh_port_t
sudo semanage port -a -t ssh_port_t -p tcp 2222

# Verify the change
sudo semanage port -l | grep ssh_port_t
```

### Scenario 9: Database Directory Access

Problem: Your database service can't access its data directory in a custom location.

Solution:
```bash
# For PostgreSQL
sudo semanage fcontext -a -t postgresql_db_t "/path/to/custom/pgdata(/.*)?"
sudo restorecon -Rv /path/to/custom/pgdata

# For MySQL/MariaDB
sudo semanage fcontext -a -t mysqld_db_t "/path/to/custom/mysql(/.*)?"
sudo restorecon -Rv /path/to/custom/mysql
```

### Scenario 10: Troubleshooting a Custom Application

Problem: Your custom application is being blocked by SELinux.

Solution:
1. **Run in permissive mode to gather denials**:
   ```bash
   # Create a domain-specific permissive mode
   sudo semanage permissive -a myapp_t
   
   # If you don't have a custom policy yet, use:
   sudo ausearch -m AVC -c "myapp" -ts recent | audit2allow -M myapp
   sudo semodule -i myapp.pp
   ```

2. **Analyze logs and create policy**:
   ```bash
   # Analyze the denials
   sudo ausearch -m AVC -c "myapp" -ts recent | audit2allow -a

   # Create a more comprehensive policy
   sudo ausearch -m AVC -c "myapp" -ts recent | grep -v cgroup | audit2allow -M myapp-detailed
   sudo semodule -i myapp-detailed.pp
   ```

## Fedora Release Management

### Understanding Fedora's Release Cycle

Fedora releases a new version approximately every 6 months with support for about 13 months.

- **Standard Releases**: Regular Fedora Workstation, Server, etc.
- **Fedora CoreOS**: Follows a different, faster release cycle
- **Fedora Silverblue**: Immutable OS with atomic updates

### Checking Your Fedora Version

```bash
# Check version
cat /etc/fedora-release

# Detailed system information
hostnamectl
```

### System Updates Best Practices

1. **Regular updates**:
   ```bash
   # Update package information
   sudo dnf check-update
   
   # Apply updates
   sudo dnf update
   ```

2. **Update specific packages**:
   ```bash
   sudo dnf update package-name
   ```

3. **Update security patches only**:
   ```bash
   sudo dnf update --security
   ```

4. **Create a system snapshot before major updates**:
   ```bash
   sudo timeshift --create --comments "Before system update"
   ```

### Upgrading to a New Fedora Version

#### DNF System Upgrade (Recommended)

```bash
# Install the DNF upgrade plugin
sudo dnf install dnf-plugin-system-upgrade

# Download new release packages
sudo dnf system-upgrade download --releasever=XX

# Reboot into upgrade process
sudo dnf system-upgrade reboot
```

Replace `XX` with the target Fedora version number.

#### Best Practices for Upgrades

1. **Back up important data first**
2. **Create a system snapshot with Timeshift**
3. **Ensure your system is fully updated before upgrading**
4. **Remove unused packages**:
   ```bash
   sudo dnf autoremove
   ```
5. **Check for orphaned packages**:
   ```bash
   sudo dnf list orphans
   ```
6. **Have a recovery plan** (bootable USB with Fedora)

### Testing New Releases Safely

#### Using Virtual Machines

```bash
# Create a VM with the new release
sudo dnf install gnome-boxes
# Use Boxes to install new Fedora version
```

#### Using Fedora Test Days

Participate in Fedora Test Days to try new features before release:
- Visit [Fedora QA](https://fedoraproject.org/wiki/QA) for information
- Test specific components and report bugs

#### Using Koji Builds

```bash
# Install a test package from Koji
sudo dnf install koji
koji download-build package-name
sudo dnf localinstall package-name*.rpm
```

## Troubleshooting Common Issues

### SELinux Denials

If an application isn't working as expected, check for SELinux denials:

```bash
# Check for SELinux denials
sudo ausearch -m AVC,USER_AVC -ts recent

# Use the setroubleshoot utility
sudo sealert -a /var/log/audit/audit.log
```

Follow the recommendations provided by sealert to resolve the issue.

### Permission Issues

For permission problems, check both standard Linux permissions and SELinux contexts:

```bash
# Check standard permissions
ls -la /path/to/file

# Check SELinux context
ls -Z /path/to/file
```

### Service Failures

If a service fails to start:

```bash
# Check service status
systemctl status service

# Check service logs
journalctl -u service
```

### Fixing Failed Updates or Installations

```bash
# Clean DNF cache
sudo dnf clean all

# Fix broken dependencies
sudo dnf distro-sync

# Check for package conflicts
sudo dnf check
```

### GNOME Shell Issues

If you're using the default GNOME desktop environment:

```bash
# Reset GNOME settings
dconf reset -f /org/gnome/

# Restart GNOME Shell (press Alt+F2, type 'r', and press Enter)
```

## Fedora Applications: Windows Equivalents and Native Apps

For users transitioning from Windows, finding equivalent applications is an important part of making yourself at home in Fedora. Here's a guide to help you navigate the Linux application ecosystem.

### Installing Applications

Fedora offers several ways to install applications:

1. **GNOME Software Center**: Graphical app store (similar to Microsoft Store)
   - Launch from Activities menu
   - Browse categories or search for applications
   - Click "Install" to download and install

2. **DNF Command Line**: 
   ```bash
   sudo dnf install application-name
   ```

3. **Flatpak**:
   - Pre-installed on Fedora
   - Access applications from Flathub through Software Center
   - Or command line: `flatpak install flathub app.id`

4. **AppImage**: Self-contained application files that can be run directly
   - Download AppImage file
   - Make executable: `chmod +x application.AppImage`
   - Run: `./application.AppImage`

5. **Snap**:
   - Not installed by default on Fedora
   - Install with: `sudo dnf install snapd`
   - Enable: `sudo systemctl enable --now snapd.socket`
   - Install apps: `sudo snap install application`

### Windows Applications and Fedora Equivalents

| Category | Windows Application | Fedora/Linux Equivalent | Install Command |
|----------|---------------------|-------------------------|-----------------|
| **Office Suite** | Microsoft Office | LibreOffice | `sudo dnf install libreoffice` |
| | | OnlyOffice | `flatpak install flathub org.onlyoffice.desktopeditors` |
| | | WPS Office | Download from website |
| **Web Browser** | Edge/Internet Explorer | Firefox | Pre-installed |
| | Chrome | Chrome | Download from Google |
| | | Chromium | `sudo dnf install chromium` |
| **Email Client** | Outlook | Thunderbird | `sudo dnf install thunderbird` |
| | | Evolution | `sudo dnf install evolution` |
| | | Geary | `sudo dnf install geary` |
| **File Explorer** | File Explorer | Nautilus (Files) | Pre-installed |
| | | Dolphin | `sudo dnf install dolphin` |
| | | Thunar | `sudo dnf install thunar` |
| **Media Player** | Windows Media Player | VLC | `sudo dnf install vlc` |
| | | Rhythmbox | `sudo dnf install rhythmbox` |
| | | Celluloid | `sudo dnf install celluloid` |
| **Image Viewer/Editor** | Photos | Image Viewer | Pre-installed |
| | Paint | Drawing | `sudo dnf install drawing` |
| | Photoshop | GIMP | `sudo dnf install gimp` |
| | | Krita | `sudo dnf install krita` |
| **PDF Reader** | Adobe Reader | Evince (Document Viewer) | Pre-installed |
| | | Okular | `sudo dnf install okular` |
| **Video Editor** | Movie Maker | Kdenlive | `sudo dnf install kdenlive` |
| | | Shotcut | `flatpak install flathub org.shotcut.Shotcut` |
| | | OpenShot | `sudo dnf install openshot` |
| **Audio Editor** | Audacity | Audacity | `sudo dnf install audacity` |
| **Archive Manager** | WinRAR/7-Zip | Archive Manager | Pre-installed |
| | | File Roller | `sudo dnf install file-roller` |
| **Terminal** | Command Prompt/PowerShell | GNOME Terminal | Pre-installed |
| | | Konsole | `sudo dnf install konsole` |
| | | Terminator | `sudo dnf install terminator` |
| **Text Editor** | Notepad | Gedit (Text Editor) | Pre-installed |
| | Notepad++ | VSCode | `sudo dnf install code` |
| | | Atom | `sudo dnf install atom` |
| | | Sublime Text | Download from website |
| **Remote Desktop** | Remote Desktop | Remmina | `sudo dnf install remmina` |
| **Torrent Client** | uTorrent | Transmission | `sudo dnf install transmission` |
| | | qBittorrent | `sudo dnf install qbittorrent` |
| **Gaming** | Steam | Steam | `sudo dnf install steam` |
| | | Lutris | `sudo dnf install lutris` |

### Popular Native Linux Applications by Category

#### Development Tools
- **VS Code**: Popular code editor
  - `sudo dnf install code`
- **Eclipse**: IDE for Java and other languages
  - `sudo dnf install eclipse`
- **JetBrains IDEs**: Professional IDEs (PyCharm, IntelliJ, etc.)
  - Download from JetBrains website
- **Geany**: Lightweight IDE
  - `sudo dnf install geany`
- **GitKraken**: Git client
  - `flatpak install flathub com.axosoft.GitKraken`

#### System Utilities
- **GParted**: Partition editor
  - `sudo dnf install gparted`
- **Stacer**: System optimizer
  - `sudo dnf install stacer`
- **Timeshift**: System backup tool
  - `sudo dnf install timeshift`
- **Flatseal**: Flatpak permissions manager
  - `flatpak install flathub com.github.tchx84.Flatseal`

#### Productivity
- **Planner**: Project management
  - `sudo dnf install planner`
- **FreeOffice**: Office suite
  - Download from website
- **Zotero**: Reference management
  - `flatpak install flathub org.zotero.Zotero`
- **StandardNotes**: Note-taking app
  - `flatpak install flathub org.standardnotes.standardnotes`

#### Graphics and Design
- **Inkscape**: Vector graphics editor
  - `sudo dnf install inkscape`
- **Blender**: 3D creation suite
  - `sudo dnf install blender`
- **Darktable**: Photography workflow
  - `sudo dnf install darktable`
- **Pinta**: Simple image editor
  - `sudo dnf install pinta`

#### Communication
- **Discord**: Chat and voice communication
  - `flatpak install flathub com.discordapp.Discord`
- **Slack**: Team communication
  - `flatpak install flathub com.slack.Slack`
- **Telegram**: Messaging
  - `sudo dnf install telegram-desktop`
- **Signal**: Secure messaging
  - `flatpak install flathub org.signal.Signal`

#### Media
- **Spotify**: Music streaming
  - `flatpak install flathub com.spotify.Client`
- **OBS Studio**: Screen recording/streaming
  - `sudo dnf install obs-studio`
- **Clementine**: Music player
  - `sudo dnf install clementine`
- **HandBrake**: Video transcoder
  - `sudo dnf install handbrake-gui`

### Fedora-Specific Applications

- **Firewall-config**: Graphical firewall configuration
  - `sudo dnf install firewall-config`
- **SELinux Troubleshooter**: Graphical SELinux management
  - `sudo dnf install policycoreutils-gui`
- **Fedora Media Writer**: Create bootable USB drives
  - `sudo dnf install mediawriter`
- **DNFDragora**: Graphical package manager
  - `sudo dnf install dnfdragora`

### Running Windows Applications on Fedora

If you need to run Windows applications that don't have Linux alternatives:

1. **Wine**: Compatibility layer for running Windows applications
   - Install: `sudo dnf install wine`
   - Run: `wine program.exe`

2. **PlayOnLinux**: Front-end for Wine that simplifies installation
   - Install: `sudo dnf install playonlinux`

3. **Bottles**: Modern and user-friendly Wine front-end
   - Install: `flatpak install flathub com.usebottles.bottles`

4. **Virtual Machine**: Run Windows in a virtual machine
   - GNOME Boxes: `sudo dnf install gnome-boxes`
   - VirtualBox: `sudo dnf install VirtualBox`

## Useful Resources

### Official Documentation

- [Fedora Documentation](https://docs.fedoraproject.org/)
- [Fedora Magazine](https://fedoramagazine.org/)
- [Fedora Wiki](https://fedoraproject.org/wiki/)

### SELinux Resources

- [Red Hat SELinux Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/using_selinux/index)
- [SELinux User's and Administrator's Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/index)

### Community Support

- [Ask Fedora](https://ask.fedoraproject.org/)
- [Fedora Discussion](https://discussion.fedoraproject.org/)
- [Fedora Subreddit](https://www.reddit.com/r/Fedora/)

### Essential Fedora-Related Commands Reference

| Command | Description |
|---------|-------------|
| `dnf` | Package manager |
| `firewall-cmd` | Firewall management |
| `getenforce` | Show SELinux mode |
| `setenforce` | Change SELinux mode |
| `getsebool` | Show SELinux boolean settings |
| `setsebool` | Change SELinux boolean settings |
| `chcon` | Change SELinux file context |
| `restorecon` | Restore default SELinux context |
| `systemctl` | Manage system services |
| `journalctl` | View system logs |
| `nmcli` | Network management |

### Important Directories

| Directory | Description |
|-----------|-------------|
| `/etc` | System configuration files |
| `/var/log` | Log files |
| `/usr/share/selinux` | SELinux policy files |
| `/etc/selinux` | SELinux configuration |
| `/var/lib/rpm` | RPM database |
| `/etc/dnf` | DNF configuration |
| `/etc/systemd` | Systemd configuration |
