Buat folder di dalam direktori proyek
```
mkdir -p /opt/stacks/erp/backup-updatev16
```

Jalankan Backup & Tarik File
```
# Jalankan backup di kontainer
docker exec -it erp-backend-1 bench --site frontend backup --with-files

# Copy ke folder di dalam direktori erp
docker cp erp-backend-1:/home/frappe/frappe-bench/sites/frontend/private/backups/. /opt/stacks/erp/backup-updatev16/
```

Mengambil backup `encryption_key` secara otomatis
```
docker exec -it erp-backend-1 cat sites/frontend/site_config.json | jq -r '.encryption_key' > /opt/stacks/erp/backup-updatev16/key.txt
```

Cek isi folder

[[gambar/Pasted%20image%2020260507161608.png]]

Setelah muncul data tersebut, maka saat ini file backup berhasil dibuat
