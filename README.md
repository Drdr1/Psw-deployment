# PSW Container USB Guide

## Overview
This guide explains how to create and use the Portable Secure Workspace (PSW) Container USB solution. The PSW provides a containerized, secure, bootable Linux environment with persistence that you can carry with you.

## Features
- Complete Lubuntu-based secure environment
- Persistent storage for your data
- Pre-installed security tools (VeraCrypt, KeePassXC, etc.)
- VPN support
- Firewall configuration
- Application launcher for common secure tasks

## Prerequisites
- A USB drive (16GB or larger recommended)
- Administrative/root privileges on your computer
- Linux environment with Docker installed
- Internet connection for downloading required components

## Required Tools
The following tools should be installed on your system:
- `docker` - for building the container image
- `parted` - for disk partitioning
- `mkfs.vfat` and `mkfs.ext4` - for formatting partitions
- `mount` - for mounting filesystems
- `wget` - for downloading required files
- Other standard Linux utilities: `df`, `grep`, `cut`

## Flashing Process

### 1. Prepare Your Environment
```bash
# Clone the repository
git clone https://your-repository-url/psw-development.git
cd psw-development/psw-usb
```

### 2. Run the Container Flashing Script
```bash
# Make the script executable
chmod +x flash-container-to-usb.sh

# Run the script with sudo privileges
sudo ./flash-container-to-usb.sh
```

### 3. Follow the Interactive Prompts
The script will guide you through the process:

1. Select your USB device from the list of available devices
2. Confirm that you want to erase all data on the device
3. Choose container build options (if applicable)
4. Wait for the container to build and flash to the USB drive

### 4. Wait for Completion
The script will:
- Build the PSW container image with Docker
- Create a new GPT partition table on the USB drive
- Create and format necessary partitions:
  - EFI partition (FAT32)
  - Boot partition (ext4)
  - Persistence partition (ext4)
- Flash the container image to the USB drive
- Configure the bootloader for UEFI boot
- Set up the persistence partition with necessary directories
- Configure security tools and application launchers

## Testing Your PSW Container USB

### Booting from the USB Drive
1. Insert the USB drive into the target computer
2. Restart the computer and enter the boot menu (usually by pressing F12, F10, or ESC during startup)
3. Select the USB drive from the boot menu
4. The PSW environment will boot automatically

### Verifying Persistence
1. Create a test file in the `/psw/data` directory
2. Restart the computer and boot from the USB again
3. Verify that your test file is still present

### Security Testing
The PSW environment includes several security features that should be tested:

1. **Firewall**: Verify that the UFW firewall is active with `sudo ufw status`
2. **Encryption**: Test VeraCrypt by creating and mounting an encrypted container
3. **VPN**: Test OpenVPN connectivity with a test profile
4. **Password Management**: Verify KeePassXC functionality

## Directory Structure
After successful preparation, your USB drive will have the following structure:

```
/psw/
├── data/           # Persistent data storage
├── tools/          # Security tools and scripts
│   ├── veracrypt/  # VeraCrypt encryption tool
│   ├── keepassxc/  # Password manager
│   └── bleachbit/  # Secure file deletion
├── vpn_profiles/   # VPN configuration profiles
├── logs/           # System and application logs
├── sync/           # Files for synchronization
├── home_agent/     # Home agent configuration
├── app-launcher/   # Application launcher scripts
└── persistence.conf # Persistence configuration
```

## Customizing the PSW Container

### Adding Additional Software
To add more software to the PSW container, modify the Dockerfile before building:

```dockerfile
# Add your additional packages here
RUN apt-get update && apt-get install -y \
    your-package-1 \
    your-package-2 \
    && apt-get clean
```

### Modifying Security Settings
Security settings can be customized in the Dockerfile:

```dockerfile
# Customize firewall rules
RUN ufw allow 443/tcp
```

### Adding Custom Scripts
Add your custom scripts to the `scripts` directory and update the Dockerfile to copy them:

```dockerfile
COPY scripts/your-custom-script.sh /psw/tools/
RUN chmod +x /psw/tools/your-custom-script.sh
```

## Troubleshooting

### Boot Issues
- **USB not booting**: Ensure your BIOS/UEFI is configured to boot from USB and Secure Boot is disabled
- **GRUB errors**: Try reinstalling GRUB with the `--force` option

### Persistence Issues
- **Data not persisting**: Check that the persistence partition is properly mounted
- **Permission errors**: Ensure the correct permissions are set on the persistence directories

### Container Build Issues
- **Docker errors**: Make sure Docker is installed and running correctly
- **Build failures**: Check that all required packages are available in the repositories

## Security Considerations
- The PSW environment is designed for secure computing, but physical security of the USB drive is still important
- Consider encrypting the entire USB drive for additional security
- Regularly update the PSW container to include security patches
- Use strong passwords for all encrypted containers and accounts

## Notes
- The preparation process requires approximately 4GB of temporary space
- The entire process may take 10-30 minutes depending on your system and download speed
- For security reasons, all sensitive data should be encrypted using the provided tools
