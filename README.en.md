# The solution to the issues encountered after the Linux kernel update.

Hello, I wanted to open this repo due to two issues I encountered today. I have two graphics cards in my laptop, one internal and one external. One of these cards is an Nvidia external graphics card, and the other is an AMD integrated graphics card in my processor. When I updated the Linux kernel, I noticed that my second monitor was not working. I use an external monitor with an HDMI cable on my device. The HDMI input is connected to the Nvidia graphics card. When I updated the Linux kernel, I could not use the external monitor on my device because the Nvidia drivers for the old kernel were not working. Here is the solution to this issue.

## Issue 1: External Monitor Not Working

### Solution Steps

1. **First, we need to remove the Nvidia driver.** I had installed the Nvidia driver by downloading the `.run` file from the website. Therefore, I used the following command to uninstall it:
    ```bash
    sudo ./NVIDIA-Linux-x86_64-XXX.XX.run --uninstall
    ```
    If you installed it with `apt`:
    ```bash
    sudo apt remove --purge '^nvidia-.*'
    sudo apt autoremove
    ```

2. **Then, you can install it either with the `.run` file or with `apt`.**
    ```bash
    sudo apt install nvidia-driver-XXX
    ```
    or
    ```bash
    wget https://us.download.nvidia.com/XFree86/Linux-x86_64/XXX.XX/NVIDIA-Linux-x86_64-XXX.XX.run
    chmod +x NVIDIA-Linux-x86_64-XXX.XX.run
    sudo ./NVIDIA-Linux-x86_64-XXX.XX.run
    ```

## Issue 2: Windows Not Working in Grub Menu

### Solution Steps

1. **First, we need to get the UUID value of the EFI disk for Windows using `sudo blkid`.** The EFI partition is usually `fat32` and shows `vfat`. We copy the UUID value of this disk.

2. **Edit the `/boot/grub/grub.cfg` file.**
    ```bash
    sudo nano /boot/grub/grub.cfg
    ```
    In the opened file, you will see entries named `menuentry`. Add a `menuentry` like below in the desired location:
    ```bash
    menuentry "Windows 11" --class windows --class os {
        insmod part_gpt
        insmod fat
        search --no-floppy --fs-uuid --set=root Disk_UUID
        chainloader /EFI/Microsoft/Boot/bootmgfw.efi
    }
    ```

3. **Finally, run `sudo update-grub` to update Grub.**
    ```bash
    sudo update-grub
    ```

This way, both issues are resolved.
