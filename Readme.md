# İki Sorun ve Çözüm Önerisi

Merhabalar, bugün karşılaştığım iki hatadan dolayı bu repoyu açmak istedim. Laptopumda bir dahili bir de harici olmak üzere iki tane ekran kartım var. Bu kartlarımdan birisi Nvidia harici ekran kartı, diğeri ise Amd işlemcimin dahili ekran kartı. Linux kernelini güncellediğimde ikinci monitörümün çalışmadığını gördüm. Cihazımda HDMI kablosu ile harici bir monitör kullanıyorum. HDMI girişinin bağlı olduğu ekran kartı Nvidia ekran kartı. Linux kernelini güncellediğimde Nvidia eski kernel dosyaları çalışmadığı için cihazımda harici monitör kullanamadım. Bu sorunun çözümünü aşağıda anlatıyorum.

## Sorun 1: Harici Monitör Çalışmıyor

### Çözüm Adımları

1. **Öncelikle Nvidia sürücüsünü silmemiz gerekiyor.** Ben Nvidia sürücüsünü sitesinden `.run` uzantılı dosyayı indirerek kurmuştum. Bundan dolayı:
    ```bash
    sudo ./NVIDIA-Linux-x86_64-XXX.XX.run --uninstall
    ```
    Eger siz `apt` ile indirdiyseniz:
    ```bash
    sudo apt remove --purge '^nvidia-.*'
    sudo apt autoremove
    ```

2. **Daha sonra ister `.run` uzantılı dosya ile ister `apt` ile kurabilirsiniz.**
    ```bash
    sudo apt install nvidia-driver-XXX
    ```
    veya
    ```bash
    wget https://us.download.nvidia.com/XFree86/Linux-x86_64/XXX.XX/NVIDIA-Linux-x86_64-XXX.XX.run
    chmod +x NVIDIA-Linux-x86_64-XXX.XX.run
    sudo ./NVIDIA-Linux-x86_64-XXX.XX.run
    ```

## Sorun 2: Grub Menüsünde Windows Çalışmıyor

### Çözüm Adımları

1. **İlk olarak `sudo blkid` ile Windows'un EFI diskini UUID değerini almamız gerekiyor.** EFI bölümü genellikle `fat32` oluyor ve `vfat` yazıyor. Bu diskin UUID değerini kopyalıyoruz.

2. **`/boot/grub/grub.cfg` dosyasını düzenliyoruz.**
    ```bash
    sudo nano /boot/grub/grub.cfg
    ```
    Açılan dosyada `menuentry` adında girişleri göreceksiniz. Buradan istediğimiz yere aşağıdaki gibi bir `menuentry` giriyoruz:
    ```bash
    menuentry "Windows 11" --class windows --class os {
        insmod part_gpt
        insmod fat
        search --no-floppy --fs-uuid --set=root Disk_UUID
        chainloader /EFI/Microsoft/Boot/bootmgfw.efi
    }
    ```

3. **Son olarak `sudo update-grub` yazıyoruz ve Grub'u güncelliyoruz.**
    ```bash
    sudo update-grub
    ```

Böylelikle iki sorun da çözülmüş oluyor.










# Two Issues and Their Solutions

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

