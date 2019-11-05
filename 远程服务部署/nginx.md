# nginx

## 网址重写和代理转发
location /api {
        rewrite ^.+api/?(.*)$ /$1 break;
        proxy_pass http://ip:port;
}