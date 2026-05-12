```
mkdir -p ~/backup-erp-16
docker cp erp-backend-1:/home/frappe/frappe-bench/sites/frontend/private/backups/. ~/backup-erp-16/
```
U

```
docker exec -it erp-backend-1 bench --site frontend backup --with-files
```

```
# Salin semua file backup dari kontainer ke folder host (pastikan sudah membuat folder backup-nya dulu)
mkdir -p ~/backup-prod-v16/
docker cp erp-backend-1:/home/frappe/frappe-bench/sites/frontend/private/backups/. ~/backup-prod-v16/
```


```
docker exec -it erp-backend-1 bench list-apps
```

Setelah itu, silahkan anda

![[update versi 16.png]]

hapus file .pyc(dari v15)

```
docker exec -it erp-backend-1 find /home/frappe/frappe-bench/apps -name "*.pyc" -delete
```


bersihkan struktur lama

```
docker exec -it erp-backend-1 bench --site frontend clear-cache
```

migrasi database

```
docker exec -it erp-backend-1 bench --site frontend migrate
```

jangan anda lakukan ini kembali
```
docker exec -it erp-backend-1 bench --site frontend clear-cache
```



----

```
docker exec -it erp-backend-1 find /home/frappe/frappe-bench/apps -name "*.pyc" -delete
```

```
docker exec -it erp-backend-1 bench --site frontend clear-cache
```

```
docker exec -it erp-backend-1 bench --site frontend migrate
```



