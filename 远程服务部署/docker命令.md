# docker命令集合---相关操作参考菜鸟教程整理一份

## 基础命令  
docker rename 原容器名  新容器名
systemctl start docker // 启动docker
sudo systemctl daemon-reload // 守护进程重启
systemctl restart  docker // 重启docker服务
sudo service docker restart // 重启docker服务
service docker stop // 关闭docker
systemctl stop docker // 关闭docker
docker rmi [镜像名字] // 删除docker镜像
docker image rm [镜像名字]// 删除docker镜像
docker ps // 查询正在运行的container容器
dokcer ps -a // 查询所有container容器
docker stop containerId/Name // 暂停某个容器
docker rm contanerId/Name // 删除某个容器
docker stop $(docker ps -a -q) // 停止所有容器 
docker rm $(docker ps -a -q) // 删除所有容器
docker container restart egmp-console-test // 重启某个容器
docker exec -it [容器ID] /bin/bash // 进入某个container容器
exit // 退出container容器
// 进入container内部修改nginx配置的方法
'但是我们想要改变配置文件nginx.conf ，进入容器,命令：
docker exec -it nginx bash
nginx.conf配置文件在 /etc/nginx/ 下面，但是你使用vim nginx.conf 或者vi nginx.conf会发现vi或者vim命令没有用
解决办法：apt-get  update  完成之后 apt-get install vim
此时你就可以自己定制nginx.con文件了
p.s.同理，进入container以后，ll命令一样不能使用，但是vim ng加tab会默认填充到vim nginx.conf'

## nginx容器命令  

1,以下命令使用 NGINX 默认的配置来启动一个 Nginx 容器实例：
docker run --name runoob-nginx-test -p 8081:80 -d nginx
2,首先，创建目录 nginx, 用于存放后面的相关东西。
mkdir -p /sourceNginx/www /sourceNginx/logs /sourceNginx/conf
3,拷贝容器内 Nginx 配置文件到本地当前目录下的 sourceNginx 目录：
如下三选一，建议3，注意www挂在
docker cp runoob-nginx-test:/etc/nginx/nginx.conf /sourceNginx/conf
docker cp runoob-nginx-test:/etc/nginx/conf.d /sourceNginx
docker cp runoob-nginx-test:/etc/nginx /sourceNginx
说明：
 www: 目录将映射为 nginx 容器配置的虚拟目录。
 logs: 目录将映射为 nginx 容器的日志目录。
 conf: 目录里的配置文件将映射为 nginx 容器的配置文件。
提醒：如果想把/etc/nginx/配置文件全部挂载出来，需要注意新建启动容器时的挂载目录层次要对上，比如如下命令，会在/sourceNginx下新增nginx文件夹，实现nginx配置目录全部挂载/usr/share/nginx/html，而不要挂在到nginx里面的html，nginx挂在也要对应上
docker cp runoob-nginx-test:/etc/nginx /sourceNginx
4,部署命令1
docker run -d -p 90:80 --name source-nginx -v /sourceNginx/www:/usr/share/nginx/html -v /sourceNginx/conf/conf.d:/etc/nginx/conf.d -v /sourceNginx/logs:/var/log/nginx nginx
命令2
docker run -d -p 8091:80 --name echarts-map -v /echarts-map/www:/usr/share/nginx/html -v /echarts-map/nginx:/etc/nginx -v /echarts-map/logs:/var/log/nginx nginx
说明：
-p 90:80： 将容器的 80 端口映射到主机的 90 端口。
--name source-nginx：将容器命名为 source-nginx。
-v /sourceNginx/www:/usr/share/nginx/html：将我们自己创建的 www 目录挂载到容器的 /usr/share/nginx/html。
-v /sourceNginx/conf/nginx.conf:/etc/nginx/nginx.conf：将我们自己创建的 nginx.conf 挂载到容器的 /etc/nginx/nginx.conf。
-v /sourceNginx/logs:/var/log/nginx：将我们自己创建的 logs 挂载到容器的 /var/log/nginx。
提醒：挂载时必须保证映射双方的内容一致，www和logs初始都是空目录，所以不用预先copy，nginx.conf必须预先copy，因为容器内非空；

## docker nginx配置https--感悟，善用logs用于调错
1,以下命令使用 NGINX 默认的配置来启动一个 Nginx 容器实例：
docker run --name runoob-nginx-test -p 8081:80 -d nginx
2,首先，创建目录 moXiang, 用于存放后面的相关东西。
mkdir -p /moXiang/www /moXiang/logs
3,拷贝容器内 Nginx 配置文件到本地当前目录下的 /moXiang 目录：
docker cp runoob-nginx-test:/etc/nginx/conf.d /moXiang
此时/moXiang下有三个目录：www logs moXiang
4，进入/moXiang/conf.d 新建cert文件夹，存放证书3430736_www.moxiang.online.key 和 3430736_www.moxiang.online.pem
5，进入/moXiang/conf.d 新建ssl.conf，键入如下内容

# 挂载位置补充说明
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
对应 -v /root/networkControl/www:/usr/share/nginx/html

vim ssl.conf
注意：代理转发配置在ssl中，例如
  location / {
    # 这个路径对应/etc/nginx/html
    root html;   #站点目录。
    index index.html index.htm;
  }
  location /api {
      rewrite ^.+api/?(.*)$ /$1 break;
      proxy_pass https://v.952100.com:9028;
  }
  location /fonts {
      proxy_pass http://119.23.111.171:9004;
  }
  location /map {
      proxy_pass http://119.23.111.171:9004;
  }

## 以下属性中以ssl开头的属性代表与证书配置有关，其他属性请根据自己的需要进行配置。
server {
  #SSL协议访问端口号为443。此处如未添加ssl，可能会造成Nginx无法启动。
  #这里指定的是容器内部nginx的https服务端口，外部主机可以随便写
  listen 443 ssl;
  server_name www.moxiang.online;  #将localhost修改为您证书绑定的域名，例如：www.example.com。
  root html;
  index index.html index.htm;
  ssl_certificate conf.d/cert/3430736_www.moxiang.online.pem;   #将domain name.pem替换成您证书的文件名。
  ssl_certificate_key conf.d/cert/3430736_www.moxiang.online.key;   #将domain name.key替换成您证书的密钥文件名。
  ssl_session_timeout 5m;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;  #使用此加密套件--此套件和证书有关。
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;   #使用该协议进行配置。
  ssl_prefer_server_ciphers on;
  location / {
    # 这个路径对应/etc/nginx/html
    root html;   #站点目录。
    index index.html index.htm;
  }
}
esc-->:-->wq(保存并退出，退出不保存时q!)
增加备注：
也可以在default.conf同级建立ssl.conf,参考如下配置：
server {
  listen 443 ssl;   #SSL协议访问端口号为443。此处如未添加ssl，可能会造成Nginx无法启动。
  server_name www.v.952100.com;  #将localhost修改为您证书绑定的域名，例如：www.example.com。
  root html;
  index index.html index.htm;
  ssl_certificate conf.d/cert/v.952100.com.pem;   #将domain name.pem替换成您证书的文件名。
  ssl_certificate_key conf.d/cert/v.952100.com.key;   #将domain name.key替换成您证书的密钥文件名。
  ssl_session_timeout 5m;
  ssl_ciphers HIGH:!RC4:!MD5:!aNULL:!eNULL:!NULL:!DH:!EDH:!EXP:+MEDIUM;  #使用此加密套件。
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;   #使用该协议进行配置。
  ssl_prefer_server_ciphers on;
  location / {
    # 这个路径对应/etc/nginx/html
    root html;   #站点目录。
    index index.html index.htm;
  }
  location /api {
      rewrite ^.+api/?(.*)$ /$1 break;
      proxy_pass https://v.952100.com:9028;
  } 
  location /fonts {
      proxy_pass http://119.23.111.171:9004;
  }
  location /map {
      proxy_pass http://119.23.111.171:9004;
  }
}
4,部署命令
docker run -d -p 443:443 --name https-nginx -v /moXiang/www:/etc/nginx/html  -v /moXiang/conf.d:/etc/nginx/conf.d -v /moXiang/logs:/var/log/nginx nginx
docker run -d -p 443:443 --name https-easywork -v /xiaoa/www:/etc/nginx/html  -v /xiaoa/conf.d:/etc/nginx/conf.d -v /xiaoa/logs:/var/log/nginx nginx
docker run -d -p 444:443 --name https-test -v /moXiangTest/www:/etc/nginx/html  -v /moXiangTest/conf.d:/etc/nginx/conf.d -v /moXiangTest/logs:/var/log/nginx nginx
说明：1，和普通80服务的区别在于：‘挂在目录的配置不同’,这个不同是有location root决定的
      2，-p 443:443 前面的443指向外部主机，后面443指向容器内部nginx的https服务端口，外部主机的端口可以不用443
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        # 这里规定了全路径的启动位置
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}


## php容器命令

https://www.runoob.com/docker/docker-install-php.html

## mySQL容器命令+mySQL连接

https://www.runoob.com/docker/docker-install-mysql.html

## docker nginx建立静态资源服务器  ---应该把log挂在出来方便调错

1，环境保证centOS 7 安装docker 拉取nginx镜像
2，创建一个存放静态资源的目录，我的是/source/data目录
mkdir -p /source/data
3,创建并编辑nginx.conf，我的nginx.conf放在/source路径下
vim nginx.conf
写入文章底部的内容
4，执行 docker 实例创建命令
docker run --name source-nginx -p 90:80 -v /source/data:/data -v /source/nginx.conf:/etc/nginx/nginx.conf -d nginx
说明：
-p 90:80： 将容器的 80 端口映射到主机的 90 端口。
--name source-nginx：将容器命名为 source-nginx。
-v /source/data:/data：将我们自己创建的data目录挂载容器内的data目录(容器内的data目录是我们在如下root配置中生成的，目录名字随意)。
    location / {
        root   /data;
        index  index.html index.htm;
    }
-v /source/nginx.conf:/etc/nginx/nginx.conf：将我们自己创建的 nginx.conf 挂载到容器的 /etc/nginx/nginx.conf,这里我们提前在写入了nginx.conf的内容，用于覆盖容器的配置。
提醒：以上location配置是容易出错的地方
1，里面的root配置，代表资源启动位置,资源'示例图片.png'放入data后，访问时直接'http://ip:端口/实例图片.png'就可以了，即跨过data这个目录访问才对。
2，第二个data是我们root配置产生的资源启动位置，外部挂载时一定要挂载到这个位置
-v /source/data:/data

nginx.conf的内容：
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /data;
            index  index.html index.htm;
        }
    }

    include /etc/nginx/conf.d/*.conf;
}

