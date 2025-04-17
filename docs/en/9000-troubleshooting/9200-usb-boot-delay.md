
# How to Set `kern.cam.boot_delay=10000` in GhostBSD

To give USB storage devices more time to initialize during boot, you can set `kern.cam.boot_delay=10000` in GhostBSD. This introduces a **10-second delay** for the CAM (Common Access Method) subsystem during device probing.

---

## Temporary Change (for current boot only)

1. **Boot from the USB Drive**  
   Insert the GhostBSD USB and boot your system. Select the USB device from the BIOS/UEFI menu if needed.

2. **Interrupt the Boot Countdown**  
   When the bootloader screen appears (with a 10-second countdown), press any key (e.g., spacebar) to pause it.

3. **Enter the Loader Prompt**  
   Choose the option:
   ```
   Escape to loader prompt
   ```
   Or press `Esc` / `Shift+Esc`. You should see:

   ```
   OK
   ```

4. **Set the Boot Delay**
   At the prompt, type:

   ```shell
   set kern.cam.boot_delay=10000
   ```

5. **Continue Booting**  
   Then type:

   ```shell
   boot
   ```

   and press **Enter**.

> **Note**: This setting is temporary and will reset after reboot.

---

## Make the Setting Permanent

To apply this setting persistently:

1. Boot into a live GhostBSD session or another system that can access the USB.

2. Open the following file with root permissions:

   ```
   /boot/loader.conf
   ```

3. Add this line:

   ```shell
   kern.cam.boot_delay="10000"
   ```

4. Save and reboot.

---

## Troubleshooting

If the setting does not help or if you can’t access the loader prompt:

- Recreate the USB using a verified ISO image.
- Test the USB on another machine to rule out hardware issues.
- Try other USB ports, especially rear-panel ones directly connected to the motherboard.

