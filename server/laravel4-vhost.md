## Apache 服务器

```shell
Options +FollowSymLinks
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [L]
```

## Nginx 服务器

```shell
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```
