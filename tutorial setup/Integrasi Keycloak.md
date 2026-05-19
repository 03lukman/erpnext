### 1. Create client realm dalam keycloak
mendapatkan client secret dari keycloak
Masuk kedalam web keycloak dengan login menggunakan browser 

```
https://login.ad.agson.co.id/
```

- Untuk akun keycloak silahkan menghubungi admin atau administrator agar bisa dibuatkan akses login

Saat sudah login, masuk ke manage realms, dan pilih agson

[[gambar/Keycloak_realms.png]]

Kemudian, klik menu clients dan create client seperti pada gambar dibawah:

[[gambar/Create%20client_general%20setting.png]]

Untuk general setting, silahkan isi dengan
- Client type: Silahkan gunakan OpenID Connect
- Untuk Client ID, nama dan deskripsi silahkan disesuaikan dengan selera anda, kemudian klik tombol Next

Kemudian, untuk tahap selanjutnya adalah setting Capability config, dengan mengaktifkan:
- Client authentication menjadi status ON
- Authentication flow ( checklist di bagian Standard flow & Direct access grants), kemudian klik tombol Next 

![[Create client_capability config.png]]

Selanjutnya, tahapan login setting:
- Root URL : isi dengan root URL aplikasi yang sudah di daftarkan di docker caddy

![[Create client_login settings.png]]

Setelah clients berhasil dibuat, selanjutnya klik menu credentials untuk copy dan simpan client secret

![[keycloak_client secret.png]]

### 2. Menghubungkan KeyCloak ke ERPNext

Masuk aplikasi erpnext dengan role administrator, kemudian cari menu **Social Login Key List**

![[erp_sosial login.png]]

Langkah selanjutnya, buat New Social Login Key dan masukkan konfigurasi Custom dengan mengikuti detail parameter yang tertera pada gambar berikut:

![[erp_sosial login key setup.png]]
![[erp_sosial login setup.png]]

Pastikan nilai pada Base URL, Client ID, dan Redirect URL disesuaikan dengan realm yang sudah dibuat pada keycloak anda sebelumnya. dan untuk konfigurasi lainnya sebagai berikut:

```
- Provider name: <anything you want> (in my case keycloak)

- Client ID: <from your IdP> (in my case erpnext)
- Client Secret: from your IdP

- Base URL: https://<idp domain>/realms/<realm name>

- Authorize URL: /protocol/openid-connect/auth
- Access Token URL: /protocol/openid-connect/token
- Redirect URL:https://<erpnext domain>/api/method/frappe.integrations.oauth2_logins.custom/<Provider name> (so for my case is /api/method/frappe.integrations.oauth2_logins.custom/keycloak)
- API Endpoint: https://<idp domain>/realms/<realm name>/protocol/openid-connect/userinfo

- Auth URL Data: {"response_type": "code", "scope": "openid profile email"}

- User ID Property: preferred_username
```

Setelah sudah dilakukan setup pada social login key, silahkan simpan dan refresh aplikasi erpnext. Kemudian masuk kembali kedalam doamain erpnext yang sudah anda buat, seperti contoh: 

```
https://erp.ad.agson.co.id
```

Jika instalasi berhasil, maka akan menampilkan halaman seperti pada gambar berikut:

![[erp_login with keycloak.png]]

Silahkan anda klik tombol Login with Login Agson, dan masukan username dan password sesuai dengan akun windows kantor anda.

![[erp_login keycloak.png]]

Kemudian, jika berhasil maka akan menampilkan halaman akun erpnext anda, setelah ini silahkan hubungi admin atau administrator untuk meminta akses aplikasi.

![[erp_login user1.png]]

Dengan ini, maka aplikasi erpnext telah berhasil terinstall pada komputer anda. Jika anda memiliki error pada langkah-langkah diatas, silahkan hubungi tim IT.
