# t4n-plymouth
Tema Plymouth untuk T4n OS/Void Linux dengan gaya Mac OS X.

## Declaration

Sumber gambar awalnya diunduh dari <a href="https://www.gnome-look.org/p/1805336">gnome-look.org</a>

Dan saya melakukan beberapa modifikasi kecil padanya.

## Installation
### 1. Install Plymouth

```bash
sudo xbps-install -S plymouth
```

### 2. Set Theme
Cukup salin folder `t4n` ke `/usr/share/plymouth/themes`

Dan gunakan fitur perbarui pengaturan tema Plymouth Anda.
```bash
$ sudo plymouth-set-default-theme -R t4n
```
> `-R` otomatis rebuild initramfs (kalau dracut tersedia)

## 3. Konfigurasi Dracut (PENTING)

Buat config:

```bash
sudo nano /etc/dracut.conf.d/plymouth.conf
```

Isi:

```conf
add_dracutmodules+=" plymouth "
```

Kalau mau lebih clean:

```conf
omit_dracutmodules+=" splash "
add_dracutmodules+=" plymouth "
```

#### TroubleShooting
Jika Anda menggunakan Enkripsi Disk Penuh (LUKS), prompt grafis mungkin hilang, dan prompt kata sandi Anda mungkin tampak tersembunyi di konsol teks. Agar layar pembuka berfungsi dengan lancar bersamaan dengan dekripsi LUKS:

1. Pastikan `modul konfigurasi dracut` Anda secara aktif menyertakan plymouth dengan mengedit `/etc/dracut.conf` atau menambahkan file .conf kustom  di `/etc/dracut.conf.d/`:
```
add_dracutmodules+=" plymouth "
```
2. nano /etc/default/grub :
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash rd.debug rd.loglevel=7 rd.udev.log_priority=3 rd.luks.name=4b644f98-6fd5-4fe7-9ee2-e709f54042ef=luks-home rd.driver.pre=amdgpu"
```

3. Bangun ulang initramfs Anda secara manual untuk menerapkan perubahan:
```
sudo dracut -f
```

4. Jalankan kembali sudo grub-mkconfig -o /boot/grub/grub.cfg dan lakukan reboot.

## 4. Enable Kernel Parameters

Edit bootloader (biasanya GRUB):

```bash
sudo nano /etc/default/grub
```

Tambahkan ke:

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash rd.udev.log_level=3 vt.global_cursor_default=0 plymouth.use-simpledrm"
```

## 5. Rebuild GRUB + Initramfs

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo dracut -f
```

## 6. Enable Service (Void Linux)

Kalau pakai Void:

```bash
sudo ln -s /etc/sv/plymouthd /var/service/
```

## 7. Reboot

```bash
reboot
```
