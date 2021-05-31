
> [!TIP]
> nginx的配置

```
server {
    listen       80;
    server_name  ccbhddev.kerlala.com;
    root  /var/www/html/ccbhd.kerlala.com/public;
    index  index.php index.html index.htm;
 
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location / {  
       try_files $uri $uri/ /index.php?$query_string;
    }

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        fastcgi_pass   php74:9000;
        fastcgi_index  index.php;
        include        fastcgi_params;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    }
}


```