> [!TIP]
> nginx的配置

```
server {
    listen       80;
    server_name  ccbhdcrmdev.kerlala.com;
    root  /var/www/html/ccbhdcrm.kerlala.com/public;
    index  index.php index.html index.htm;
    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location / {      
       try_files $uri $uri/ /index.php?$query_string;
    }


    location /app {
        proxy_pass https://ccbresbch.kerlala.com/projects/hd/index.html;
    }

    location ^~ /res-edit/ {
        proxy_pass http://ccbresbch.kerlala.com/projects/hd/edit/;
    }
    
    location ^~ /apihdcrm/ {
        proxy_pass https://ccbhdcrmbch.kerlala.com/;
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