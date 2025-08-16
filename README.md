# Panduan Dasar Podman
Disusun oleh: MARIA DOMINIKA MEME (215410112)

## 1. Pengantar
Podman (Pod Manager) adalah alat manajemen container berbasis open-source yang berada di bawah lisensi Apache License 2.0. Artinya, perangkat ini bisa digunakan, dimodifikasi, dan dibagikan secara gratis untuk kebutuhan pribadi maupun komersial. Podman dikembangkan oleh Red Hat dengan tujuan menjadi alternatif Docker yang lebih ringan, aman, dan fleksibel.
Dengan Podman, kita bisa:
- Membuat container dari sebuah image.
- Menjalankan container secara interaktif maupun di latar belakang.
- Mengatur container, image, dan pod (sekumpulan container yang berbagi network namespace).
### 1.1 Perbedaan Mendasar Podman vs Docker
Walaupun mirip secara fungsi, ada perbedaan kunci antara Podman dan Docker:
- Docker menggunakan daemon (proses tunggal yang berjalan terus di latar belakang) dan biasanya memerlukan hak akses root.
- Podman tidak bergantung pada daemon, setiap perintah langsung dijalankan sebagai proses independen. Mode rootless membuat Podman lebih aman untuk sistem multi-pengguna.
### 1.2 Kelebihan Podman
- Rootless Container <br>
  Pengguna biasa dapat membuat dan menjalankan container tanpa hak root, mengurangi risiko keamanan.
- CLI yang Kompatibel dengan Docker <br>
  Perintah Podman hampir identik dengan Docker. Bahkan, kita bisa membuat alias:
    ```bash
    alias docker=podman
    ```
    sehingga perintah Docker lama bisa langsung digunakan.
- Dukungan Pod seperti Kubernetes <br>
  Podman memungkinkan menjalankan beberapa container dalam satu network namespace.
- Terintegrasi Langsung dengan Sistem Operasi <br>
  Tidak ada daemon berarti Podman lebih hemat sumber daya dan bekerja langsung dengan kernel Linux melalui libpod dan runc.

## 2. Tabel Perbandingan Podman dan Docker
| Fitur            | Podman                        | Docker                |
|------------------|--------------------------------|-----------------------|
| Mode Daemon      | Tidak menggunakan daemon       | Menggunakan daemon    |
| Rootless Support | Ya                             | Terbatas              |
| CLI Compatibility| Kompatibel dengan `docker` CLI | Native Docker CLI     |
| Keamanan         | Lebih aman untuk multi-user    | Memerlukan root untuk daemon |

## 3. Instalasi Podman
### 3.1 Menggunakan Arch Linux
```bash
sudo pacman -S podman
```
- pacman
  - Pacman adalah manajer paket bawaan Arch Linux dan turunannya (misalnya Manjaro).
  - Fungsinya mirip seperti apt di Ubuntu atau dnf di Fedora, yaitu mengelola instalasi, pembaruan, dan penghapusan software.
  - Nama "pacman" berasal dari Package Manager.
- -S
  - Opsi -S (sync) digunakan untuk menginstal paket baru atau memperbarui paket yang sudah ada.
  - Saat perintah dijalankan, pacman akan mengecek apakah paket podman tersedia di repository resmi, mengunduh dan menginstalnya, memasang semua dependencies (paket lain yang dibutuhkan Podman).
- podman <br>
  Nama paket yang akan diinstal, dalam hal ini adalah Podman.
### 3.2 Menggunakan Manual
- Unduh installer Windows dari situs resmi Podman atau GitHub.<br>
  https://podman.io/docs/installation<br>
  https://podman-desktop.io/docs/installation/windows-install
- Jalankan .exe installer, bisa juga menggunakan opsi silent (/S), Chocolatey, Scoop, atau Winget.
- Installer akan mengurus setup WSL2 dan mesin virtual untuk menjalankan Linux guest. Setelah selesai, Kita bisa menggunakan GUI untuk mengelola container dan Kubernetes.
### 3.3 Menggunakan Command-Line (MSI + podman machine)
- Download dan jalankan podman-vX.Y.Z.msi dari GitHub Releases.
- Setelah install, jalankan perintah PowerShell:
  ```bash
  podman machine init
  podman machine start
  ```
  Ini membuat mesin virtual yang menjalankan Podman daemon di WSL2.<br>
- Sekarang perintah podman bisa digunakan langsung dari PowerShell atau CMD.
### 3.4 Mengecek Versi
```bash
podman --version
```
- podman
  - Ini adalah command line interface (CLI) Podman.
  - Saat mengetik podman di terminal, kita memanggil program utama Podman yang berfungsi untuk mengelola container, image, dan pod.
- --version
  - --version adalah opsi/flag yang digunakan hampir di semua aplikasi CLI untuk menampilkan informasi versi software.
  - Dalam konteks Podman, perintah ini akan menampilkan nomor versi Podman yang terpasang dan terkadang juga informasi build atau commit hash (tergantung paket dan distro Linux yang digunakan).
- Contoh output
  ```bash
  $ podman --version
  podman version 4.9.3
  ```
## 4. Perintah Dasar Podman
### 4.1 Menarik (Pull) Image
```bash
podman pull nginx
```
Contoh Output
```bash
$ podman pull nginx
Trying to pull docker.io/library/nginx:latest...
Getting image source signatures
Copying blob 5c2aa87436b4 done
Copying blob 7f676cc4b9b1 done
Copying blob e99c654a7e85 done
...
Storing signatures
docker.io/library/nginx:latest
```
Proses yang Terjadi : <br>
- Podman mencari image nginx di registry default.
- Jika image ditemukan, Podman mengunduhnya ke cache lokal.
- Image tersimpan di lokal dan siap digunakan untuk membuat container.

- pull
  - Perintah pull digunakan untuk mengunduh (menarik) image container dari sebuah container registry ke sistem lokal.
  - Container registry adalah repositori tempat image disimpan.
- nginx
  - Nama image yang ingin diunduh.
  - nginx adalah image resmi (official image) web server Nginx yang tersedia di Docker Hub.
  - Jika tidak menyebutkan tag versi (misalnya nginx:1.25), Podman akan mengambil tag default latest.

### 4.2 Menjalankan Container
```bash
podman run -d -p 8080:80 nginx
```
Contoh Output <br>
Output yang muncul biasanya hanya berupa container ID panjang (sekitar 64 karakter hex)
```bash
$ podman run -d -p 8080:80 nginx
4c01db0b339c3d7c90dd7ec9a04915efccf9d4b7cbbd6a3d6e27b7f0e4b20d4b
```
- podman run – Menjalankan container baru.
- -d – Mode detached, container berjalan di latar belakang.
- -p 8080:80 – Port mapping, mengarahkan port 80 di dalam container ke port 8080 di host, sehingga kita bisa mengaksesnya di http://localhost:8080.
- nginx – Nama image yang digunakan (dalam hal ini Nginx web server).
### 4.3 Melihat Daftar Container
```bash
podman ps
podman ps -a
```
Contoh Output
```bash
$ podman ps
CONTAINER ID  IMAGE                           COMMAND               CREATED         STATUS             PORTS                 NAMES
4c01db0b339c  docker.io/library/nginx:latest  nginx -g daemon off;  5 seconds ago   Up 5 seconds ago   0.0.0.0:8080->80/tcp  inspiring_turing
```
- podman ps digunakan untuk melihat container yang sedang berjalan
- podman ps - a digunakan untuk melihat semua container
### 4.4 Menghentikan Container
```bash
podman stop <CONTAINER_ID>
```
- podman stop → Perintah untuk menghentikan container secara teratur (gracefully).
- <CONTAINER_ID> → ID atau nama container yang ingin dihentikan.
### 4.5 Menghapus Container
```bash
podman rm <CONTAINER_ID>
```
Contoh Output
```bash
$ podman rm <CONTAINER_ID>
<CONTAINER_ID>
```
- podman rm menghapus container dari daftar Podman.
- <CONTAINER_ID> → ID atau nama container yang ingin dihentikan.
### 4.6 Menghapus Image
```bash
podman rmi <IMAGE_NAME>
```
Contoh Output
```bash
$ podman rmi <IMAGE_NAME>
Untagged: docker.io/library/nginx:latest
Deleted: 3f8a4339aaddc912eae556e80d6a764f06b1bdb3c988f3ddf7869a4abf123456
Deleted: 0f2a3c42b9fcb6b28d7b689ba5e07e8f3bbdd0b12d0c42f9f6e3f5f2ad789abc
Deleted: 42d3b7e568acbfa9b2bb4a3ab73b1e47e8f0e2c84a0ec3d00a212d34a56789de
```
- Fungsi: Menghapus image dari sistem lokal.
- Harus pastikan tidak ada container yang menggunakan image tersebut.
- <IMAGE_NAME> bisa berupa nama atau ID image.
## 5. Contoh Alur Lengkap
### 5.1 Pull image alpine
```bash
podman pull alpine
```
### 5.2 Jalankan container dengan perintah
```bash
podman run -it alpine sh
```
### 5.3 Di dalam container jalankan
```bash
echo "Hallo Podman"
```
### 5.4 Keluar dari container
```bash
exit
```
## 6. Ringkasan
Podman adalah alternatif Docker yang lebih aman, ringan, dan fleksibel, dengan kemampuan rootless dan kompatibilitas penuh terhadap perintah Docker. Cocok untuk pengembangan maupun produksi, terutama bagi lingkungan yang membutuhkan keamanan tinggi dan kontrol penuh terhadap container.
## 7. Sumber referensi
https://www.redhat.com/en/blog/run-podman-windows
