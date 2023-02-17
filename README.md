# Script-Configuration

## Konfigurasi Terraform
Konfigurasi terraform dapat diakses pada [file module.tf](module.tf)

## Login Instance dengan SSH
Setelah instance pada aws terbuat, login dengan SSH sesuai alamat instance. Gunakan perintah `ssh -i "identity_file" "user"@"host"`. Kemudian cek koneksi firewall, pastikan port HTTP, HTTPS, dan SSH sudah terbuka, sesuai perintah pada terraform.

## Konfigurasi MySQL
1. Install mySQL dengan perintah `apt install mysql-server`
2. Atur konfigurasi dengan perintah sebagai berikut:
    ```
    Config mySQL
    CREATE USER 'kelompok1'@'localhost' IDENTIFIED BY 'kelompok1';
    CREATE DATABASE kelompok1;
    GRANT ALL ON kelompok1.* TO 'kelompok1'@'localhost';
    FLUSH PRIVILEGES;
    ```

## Deploy Laravel-App in Nginx
1. Pada direktori `/var/www` clone repositori yang diberikan pada soal dengan command
    ```
    git clone https://gitlab.com/kuuhaku86/web-penugasan-individu.git
    ```
2. Install PHP sesuai dengan versi pada repositori dengan menggunakan perintah:
    ```sh
    sudo apt install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql composer unzip
    ```
3. Copy `.env.example` menjadi `.env` dan atur field berikut
    ```
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=kelompok1
    DB_USERNAME=kelompok1
    DB_PASSWORD=kelompok1
    ```
4. Lakukan perintah `composer install` kemudian `php artisan key:generate`
5. Lakukan migrasi tabel dari laravel ke mysql dengan perintah `php artisan migrate`

## Setting Situs pada Nginx
Gunakan konfigurasi berikut untuk setting pada file `/etc/nginx/sites-available/default/`
```sh
server {
        listen 80;
        listen [::]:80;


        # root /var/www/html;
        root /var/www/web-penugasan-individu/public;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html index.php;

        server_name penugasandua.erviana.my.id;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        #
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
        #
        #       # With php-fpm (or other unix sockets):
                fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
        #       # With php-cgi (or other tcp sockets):
        #       fastcgi_pass 127.0.0.1:9000;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #       deny all;
        #}
}
```

## Setting DNS
Setting DNS dilakukan dengan menggunakan third-party yaitu Cloudflare dengan menambahkan A record.

## Konfigurasi SSL
Konfigurasi SSL dapat dilakukan dengan perintah:
1. Install menggunakan `apt install certbot dan python3-nginx-certbot`
2. Tambahkan servername `www.penugasandua.erviana.my.id` pada `/etc/nginx/default`
3. Jalankan perintah `sudo certbot --nginx -d penugasandua.erviana.my.id -d www.penugasandua.erviana.my.id`
4. Certbot akan menambahkan konfigurasi seperti berikut:
    ```sh
    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/penugasandua.erviana.my.id/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/penugasandua.erviana.my.id/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
    ```