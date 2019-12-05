# nginx

## 网址重写和代理转发
location /api {
        rewrite ^.+api/?(.*)$ /$1 break;
        proxy_pass http://ip:port;
}

## 413问题处理

注意，只能有一个空格，多空格会导致失败；

可以选择在http{ }中设置： client_max_body_size 20m;

 也可以选择在server{ }中设置： client_max_body_size 20m;


还可以选择在location{ }中设置：client_max_body_size 20m;

三者到区别是：http{} 中控制着所有nginx收到的请求。而报文大小限制设置在server｛｝中，则控制该server收到的请求报文大小，同理，如果配置在location中，则报文大小限制，只对匹配了location 路由规则的请求生效。

 大佬们好，
docker nginx 解决413问题，
1，server里面增加：
client_max_body_size   20m; 
docker无法重启！
2，server里面增加：
client_max_body_size 20m;
重启成功！
为什么，多两个空格就会失败？