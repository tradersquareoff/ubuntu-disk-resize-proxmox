## Resize Ubuntu VM Disk in Proxmox VE

This guide details the steps to successfully resize the disk space allocated to your Ubuntu VM within the Proxmox VE environment.

## Prerequisites

- Proxmox VE server with an Ubuntu VM
- Basic understanding of Linux commands
- Backup of your VM data (strongly recommended)

## Steps

### 1. Increase Disk Size in Proxmox VE Web Interface

1. Shut down your Ubuntu VM
2. Navigate to the Proxmox VE web interface
3. Select your VM
4. Go to the "Hardware" tab
5. Select the virtual disk you want to resize
6. Click "Resize" under "Disk Actions"
7. Enter the desired new size (e.g., +10G for additional 10GB)
8. Click "Resize" to confirm

### 2. Extend the Partition

1. Start the VM and login
2. Check current disk layout:
```bash
lsblk
df -h
```

3. Install required tools:
```bash
sudo apt update
sudo apt install cloud-guest-utils
```

4. Extend the partition (replace X with your partition number):
```bash
sudo growpart /dev/sda X
```

### 3. Resize the Filesystem

#### For ext4 filesystem:
```bash
sudo resize2fs /dev/sdaX
```

#### For LVM setup:
```bash
sudo pvresize /dev/sdaX
sudo lvextend -l +100%FREE /dev/mapper/vg-name
sudo resize2fs /dev/mapper/vg-name
```

### 4. Verify Changes

Check the new disk size:
```bash
df -h
lsblk
```

## Troubleshooting

- If you get errors about partition overlap, ensure the VM is completely powered off before resizing in Proxmox
- For LVM setups, verify volume group names using `vgs` command
- If resize2fs fails, try running `e2fsck -f /dev/sdaX` first

## Important Notes

- **Always backup your data before performing disk operations**
- Different Ubuntu versions might have slightly different partition layouts
- If using LVM, the steps might need to be adjusted accordingly
- For systems using ZFS or other filesystems, different commands may be required

## Additional Resources

- [Proxmox VE Documentation](https://pve.proxmox.com/wiki/Main_Page)
- [Ubuntu Documentation](https://help.ubuntu.com/)
```
