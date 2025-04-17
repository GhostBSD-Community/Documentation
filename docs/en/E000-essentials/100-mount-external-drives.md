# Mounting External Drives

This guide explains how to mount external drives, such as USB flash drives or external hard drives, in GhostBSD. It covers common filesystems including **exFAT**, **NTFS**, and **UFS**, using both graphical and terminal-based methods. GhostBSD provides user-friendly tools to make this process accessible for all users.

## Prerequisites
- An external drive (e.g., USB flash drive or external hard disk) formatted with exFAT, NTFS, UFS, or another filesystem.
- A GhostBSD system with a desktop environment (e.g., MATE).
- Basic user permissions (no root access required for GUI automounting; sudo required for terminal commands).
- Required packages for specific filesystems (installed as needed, see below).

## Method 1: Automatic Mounting via Desktop Environment
GhostBSD’s desktop environments, such as MATE, often include automounting features for external drives.

1. **Connect the External Drive**:
   - Plug the external drive into a USB port.
   - Wait a few seconds for the system to detect the drive.

2. **Check for Automount**:
   - If automounting is enabled, the drive should appear on the desktop or in the file manager (e.g., Caja for MATE).
   - A notification may indicate the drive is mounted.
   - Supported filesystems (e.g., exFAT, NTFS) typically automount if the necessary drivers are installed.

3. **Access the Drive**:
   - Double-click the drive icon on the desktop or locate it in the file manager.
   - The drive is usually mounted under `/media/username/` or `/mnt/`.

4. **Eject the Drive**:
   - Right-click the drive icon and select "Eject" or "Safely Remove."
   - Wait for confirmation before unplugging the drive.

**Note**: If the drive does not automount, ensure automount settings are enabled in your desktop environment or proceed to Method 2. For exFAT or NTFS, you may need to install additional packages (see Method 2).

## Method 2: Manual Mounting via Terminal
Use the terminal for manual mounting, especially for filesystems like exFAT, NTFS, or UFS, or if automounting fails.

1. **Connect the External Drive**:
   - Plug the drive into a USB port.

2. **Identify the Drive**:
   - Open a terminal and run:
     ```shell
     lsblk
     ```
   - Identify your drive (e.g., `/dev/da0s1` for a USB drive). Note the device name and partition.
   - Alternatively, use `dmesg | tail` to see recent device detection messages.

3. **Create a Mount Point**:
   - Create a directory for mounting:
     ```shell
     sudo mkdir /mnt/drive
     ```

4. **Mount the Drive**:
   - Use the appropriate command based on the filesystem type. Replace `/dev/da0s1` with your drive’s device name.

   ### Mounting exFAT
   - **Install exFAT Support** (if not already installed):
     ```shell
     sudo pkg install exfat-utils fusefs-exfat
     ```
   - Load the FUSE kernel module:
     ```shell
     sudo kldload fuse
     ```
   - Mount the exFAT drive:
     ```shell
     sudo mount.exfat /dev/da0s1 /mnt/drive
     ```

   ### Mounting NTFS
   - **Install NTFS Support** (if not already installed):
     ```shell
     sudo pkg install ntfs-3g
     ```
   - Mount the NTFS drive:
     ```shell
     sudo mount -t ntfs /dev/da0s1 /mnt/drive
     ```
   - For read-write access with NTFS-3G:
     ```shell
     sudo ntfs-3g /dev/da0s1 /mnt/drive
     ```

   ### Mounting UFS
   - UFS (Unix File System) is native to BSD systems and typically does not require additional packages.
   - Mount the UFS drive:
     ```shell
     sudo mount -t ufs /dev/da0s1 /mnt/drive
     ```
   - If the UFS volume is from a non-GhostBSD/FreeBSD system, specify the UFS version if needed:
     ```shell
     sudo mount -t ufs -o ufstype=ufs2 /dev/da0s1 /mnt/drive
     ```

5. **Access the Drive**:
   - Navigate to the mount point:
     ```shell
     cd /mnt/drive
     ls
     ```

6. **Unmount the Drive**:
   - Unmount the drive when done:
     ```shell
     sudo umount /mnt/drive
     ```
   - Remove the mount point if no longer needed:
     ```shell
     sudo rmdir /mnt/drive
     ```

## Troubleshooting
- **Drive Not Detected**:
  - Run `dmesg` to verify the system recognizes the drive:
    ```shell
    dmesg | tail
    ```
  - Ensure the drive is properly connected and powered.
- **exFAT Mounting Fails**:
  - Confirm the `fuse` kernel module is loaded:
    ```shell
    kldstat | grep fuse
    ```
  - If not loaded, load it manually (`sudo kldload fuse`).
- **NTFS Permission Issues**:
  - If the drive is read-only, use `ntfs-3g` for read-write access.
  - Adjust permissions if needed:
    ```shell
    sudo chmod -R u+rw /mnt/drive
    ```
- **UFS Mounting Fails**:
  - Check if the UFS version matches (e.g., UFS1 or UFS2). Use the `-o ufstype=ufs2` option if necessary.
  - Ensure the drive is not corrupted by running:
    ```shell
    sudo fsck_ufs /dev/da0s1
    ```
- **Missing Filesystem Support**:
  - Install required packages for exFAT (`exfat-utils`, `fusefs-exfat`) or NTFS (`ntfs-3g`) as shown above.

## Additional Notes
- **Filesystem Support**:
  - exFAT requires `exfat-utils` and `fusefs-exfat` packages, which rely on FUSE.
  - NTFS requires `ntfs-3g` for full read-write support.
  - UFS is natively supported in GhostBSD, but cross-platform UFS volumes may require specific options.
- **Device Naming**:
  - GhostBSD uses FreeBSD conventions (e.g., `/dev/daX` for USB drives, `/dev/adaX` for SATA drives).
- **Safety**:
  - Always unmount drives before unplugging to prevent data loss.
- **Persistent Mounting**:
  - For drives you frequently use, consider adding entries to `/etc/fstab` for automatic mounting. Example for an exFAT drive:
    ```shell
    /dev/da0s1 /mnt/drive exfat rw 0 0
    ```
  - Consult the GhostBSD documentation for `/etc/fstab` configuration.
- **Performance**:
  - exFAT and NTFS may have slower performance compared to UFS due to FUSE or compatibility layers.

---

*Last Updated: April 2025*
