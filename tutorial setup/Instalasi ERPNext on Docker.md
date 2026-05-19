### Persiapan Awal
Pastikan anda sudah mempersiapkan beberapa bahan berikut:
- Ubuntu Server 24.04.03 LTS Sebagai system operasi linux
- Docker Engine & Docker Compose 
- Docker Erpnext
- Docker Caddy sebagai reverse proxy

### 1. Langkah install docker di Ubuntu 24.04.03 LTS
Hapus terlebih dahulu paket yang memungkinkan untuk untuk menghindari konflik dengan versi yang disertakan dalam Docker Engine
 

```
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)
```

Install menggunakan apt repository
`
```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

Install Paket Dockernya

```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Setelah install paket dockernya kemungkinan ada beberapa yang service yang belum running, jalankan perintah ini 

```
sudo docker run hello-world
```

### Instalasi Docker ERPNext
Buat terlebih dahulu folder docker untuk bookstack dengan perintah

```
mkdir docker && cd docker 
```

Untuk saat ini posisi anda sudah ada di directory Docker, lanjut untuk menjalankan perintah untuk mengcloning bookstack 

```
git clone https://git.ad.agson.co.id/git-lukman/erpnext.git
```

Folder docker Bookstack sudah ada di linux anda. untuk memastikan tinggal ketikan perintah   ls -l untuk memastikan folder bookstack sudah ada di dalam folder docker

Lanjut masuk ke dalam folder bookstack dengan perintah 

```
cd erpnext
```

edit isi file .env dengan perintah nano, seperti berikut

```
# Database Settings
DB_HOST=db
DB_PORT=3306
MYSQL_ROOT_PASSWORD=admin

# Redis Settings
REDIS_CACHE=redis-cache:6379
REDIS_QUEUE=redis-queue:6379

# Site Settings
SOCKETIO_PORT=9000
FRAPPE_SITE_NAME_HEADER=frontend
UPSTREAM_REAL_IP_ADDRESS=127.0.0.1
```
untuk menyimpan tekan tombol ctrl + x , kemudian tekan ctrl + y, kemudian tekan tombol enter

Isi variabel berikut:
- Database Setting: Buat password database yang kuat.
- Redis settings: Masukan setup redis
- Site Settings: Sesuaikan dengan port server

### 2. Setting Caddy (Reverse Proxy)
Buat dahulu folder docker caddynya. Karena sebelumnya kita sudah buat folder docker untuk installasi bookstack, maka kita akan gunakan folder docker tersebut untuk installasi docker caddy

```
cd ~
cd docker
mkdir caddy
cd caddy
```

Selanjutnya buat docker compose untuk caddy seperti berikut:

```
cat <<EOF > docker-compose.yml
services:
  caddy:
    image: iarekylew00t/caddy-cloudflare:latest
    restart: unless-stopped
    ports:
      - 80:80
      - 443:443
      - 443:443/udp
    volumes:
      - ./caddy:/etc/caddy
      - ./data:/data
    networks:
      - net-caddy

networks:
  net-caddy:
    external: true
EOF
```

Ubah settingan networks dan sesuaikan dengan konfigurasi anda atau membuat network baru:

```
docker network ls
docker network create net-caddy
```
Untuk nama networks, sesuaikan juga dengan yang ada pada docker-compose.yml

Setelah itu, silahkan buat folder baru lagi di dalam caddy, dan buat file Caddyfile dalam directory caddy tersebut:

```
mkdir caddy
cd caddy
nano Caddyfile #kemudian tekan enter
```

Isi Caddyfile 

```
erp.ad.agson.co.id {
        reverse_proxy erp:8085
}
```
untuk menyimpan tekan tombol ctrl + x , kemudian tekan ctrl + y, kemudian tekan tombol enter


Selanjutnya, jalankan program docker bookstack dan docker caddy secara bersamaan dengan menjalankan perintah:

```
cd ~
cd docker
docker compose -f erp/docker-compose.yml up && docker compose -f crm/docker-compose.yml up
```

Setelah docker bookstack dan docker caddy berhasil dijalankan, silahkan buka web menggunakan chrome atau yang lainnya, kemudian masuk ke domain bookstack anda yang sudah dibuat. sepertu contoh:

```
https://erp.ad.agson.co.id
```

Jika instalasi berhasil, maka akan menampilkan halaman seperti pada gambar berikut:

[[gambar/erp_login_administrator.png]]

Selanjutnya, untuk tutorial cara setup awal aplikasi erpnext bisa dilihat pada link berikut:
```
https://www.youtube.com/watch?v=pniZB-emt90
```
