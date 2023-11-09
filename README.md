# How-to-install-virtualbox-7-on-ubuntu-with-secure-boot


#Tambahkan repo virtualbox
wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg

#tambahkan repo virtualbox ke ubuntu 22.04
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list

#jalankan update
sudo apt update
#kemudian install virtualbox-7.0

#setelah selesai cek dengan
sudo /sbin/vboxconfig

#jika ada error terkait modprobe, jalankan perintah berikut:
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install mokutil

#buat Signature file
openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=VirtualBox/"
#tambahkan kedalam kernel
sudo /usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 ./MOK.priv ./MOK.der $(modinfo -n vboxdrv)

#daftarkan kedalam secure boot
sudo mokutil --import MOK.der
#Akan ada pertanya pembuatan password, pastikan kamu menginatnya.

#kemudian restart komputer kamu
#saat restart komputer akan ada tampilan perform MOK management
#ikuti ini saja: Enroll MOK > Continue > Yes (lalu masukkan pasword yang telah dibuat sebelumnya) > reboot

#error pada modprobe terjadi karena modul "vboxdrv" tidak ditanda tangani (dianggap tidak sah oleh Secure boot) dengan demikian modul vboxdrv tidak dapat dimuat kedalam kernel. maka kamu perlu menandatangani modul tersebut agar dapat dimuat oleh Secure boot.

#untuk memastikan virtualbox kamu siap digunakan, masukan perintah berikut:
sudo /sbin/vboxconfig

#jika tidak ada yang salah maka akan seperti berikut:

vboxdrv.sh: Stopping VirtualBox services.
vboxdrv.sh: Starting VirtualBox services.
vboxdrv.sh: Building VirtualBox kernel modules.
vboxdrv.sh: Signing VirtualBox kernel modules.


#Kemudian tambahkan virtualbox sebagai user, agar kamu bisa mengakses USB. 
sudo usermod -a -G vboxusers $USER
#($user adalah nama root ubuntu kamu yang dicari otomatis, supaya memudahkan jika usermu panjang)

#untuk Extension Package kamu bisa dengan mudah install langsung melalui aplikasi virtual box
