Merhabalar bugün karşılaştığım iki hatadan dolayı bu repoyu açmak istedim.
Laptopumda bir dahili bir de harici olmak üzere iki tane ekran kartım var.
Bu kartlarımdan birisi Nvidia harici ekran kartı diğeri ise Amd işlemcimin dahili ekran kartı.
Linux kernelini güncellediğimde ikinci monitorümün çalışmadığını gördüm.
Cihazımda hdmi kablosu ile harici bir monitor kullanıyorum.
Hdmi girişinin bağlı olduğu ekran kartı Nvidia ekran kartı.
Linux kernelini güncellediğimde Nvidia eski kernel dosyaları çalışmadığı için cihazımda harici monitor kullanamadım.
Bu sorunun çözümünü aşağıda anlatıyorum.


1-Öncelikle Nvidia sürücüsünü silmemiz gerekiyor.Ben Nvidia sürücüsünü sitesinden .run uzantılı dosyayı indirerek kurmuştum.
Bundan dolayı sudo ./NVIDIA-Linux-x86_64-XXX.XX.run --uninstall komutu ile silme işlemini yaptım.
Eğer siz apt ile indirdiyseniz sudo apt remove --purge '^nvidia-.*'
sudo apt autoremove komutlarını da kullanabilirsiniz.

2-Daha sonra ister .run uzantılı dosya ile ister apt ile kurabilirsiniz.
sudo apt install nvidia-driver-XXX
ya da
wget https://us.download.nvidia.com/XFree86/Linux-x86_64/XXX.XX/NVIDIA-Linux-x86_64-XXX.XX.run
chmod +x NVIDIA-Linux-x86_64-XXX.XX.run
sudo ./NVIDIA-Linux-x86_64-XXX.XX.run

İkinci karşılaştığım hatayı anlatıyorum.Ben normalde dual boot olarak hem Debian hem de Windows kullanıyorum.
Linux kernel güncellemesinden sonra grub menüde windows çalışmıyordu.Bu sorunun çözümünü de aşağıda anlatıyorum.


1-İlk olarak sudo blkid ile windows'un efi diskinin UUID değerini almamız gerekiyor.
Efi bölümü genellikle fat32 oluyor ve vfat yazıyor.Bu diskin UUID değerini kopyalıyoruz.

2-sudo nano /boot/grub/grub.cfg yazıyoruz.
Açılan dosyada menu entry adında girişleri göreceksiniz. Buradan istediğimiz yere alttaki gibi bir menu entry giriyoruz

menuentry "Windows 11" --class windows --class os {
    insmod part_gpt
    insmod fat
    search --no-floppy --fs-uuid --set=root Disk_UUID
    chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}

3-Son olarak sudo update-grub yazıyoruz ve grub'u güncelliyoruz.



Böylelikle iki sorun da çözülmüş oluyor.
