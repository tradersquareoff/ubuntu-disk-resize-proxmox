# Resize Ubuntu VM Disk in Proxmox VE

This guide details the steps to successfully resize the disk space allocated to your Ubuntu VM within the Proxmox VE environment.

**Prerequisites:**

- Proxmox VE server with an Ubuntu VM
- Basic understanding of Linux commands (optional, but helpful)

**Steps:**

1. **Increase Disk Size in Proxmox VE Web Interface:**

   - Shut down your Ubuntu VM.
   - Navigate to the Proxmox VE web interface and select your VM.
   - Go to the "**Hardware**" tab.
   - Click on the virtual disk you want to resize.
   - Under "**Disk Actions**," choose "**Resize**."
   - Enter the desired new disk size and click "**Resize**" to confirm.

2. **Extend Physical Drive Partition (if LVM is not used):**

   **Caution:** These steps involve modifying partitions. Proceed with caution and ensure you have a backup of your VM data in case of any issues.

   - Log in to your Proxmox VE host via SSH.
   - Verify the current disk layout using:

     ```bash
     sudo fdisk -l
     ```

   - Identify the partition you want to extend (usually the third partition, `/dev/sda3`).
   - Use `growpart` to extend the partition size:

     ```bash
     sudo growpart /dev/sda 3
     ```

     Follow the on-screen prompts to specify the desired new size.

3. **Extend Logical Volume (if LVM is used):**

   - If your Ubuntu VM uses Logical Volume Management (LVM), follow these steps:

     - Check the current Logical Volume information:

       ```bash
       lvdisplay
       ```

     - View the physical drive information using `pvdisplay`:

       ```bash
       sudo pvdisplay
       ```

     - Identify the physical volume name associated with your extended partition (e.g., `/dev/sda3`).

     - Instruct LVM that the disk size has changed:

       ```bash
       sudo pvresize /dev/sda3
       ```

     - Verify the updated physical drive information:

       ```bash
       sudo pvdisplay
       ```

     - Extend the Logical Volume using its name (e.g., `/dev/ubuntu-vg/ubuntu-lv`):

       ```bash
       sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
       ```

       The `-l +100%FREE` option utilizes all available free space within the Volume Group.

4. **Resize Filesystem:**

   - Resize the filesystem on the extended partition to utilize the additional space:

     ```bash
     resize2fs /dev/ubuntu-vg/ubuntu-lv
     ```

5. **Verify Changes:**

   - Confirm that the disk and filesystem have been resized successfully:

     ```bash
     fdisk -l
     ```

   - You can also boot your Ubuntu VM and use tools like `df -h` to check the available disk space.

**Additional Notes:**

- Consider backing up your VM before proceeding with these steps.
- If you encounter issues, consult the Proxmox VE documentation or seek help from the Proxmox community forums.
