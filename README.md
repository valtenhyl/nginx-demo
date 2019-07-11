# 

# nginx-demo

>启动json-server

> 指定端口8081



## 1、Nginx介绍

Nginx（`engine x`）是一个高性能的HTTP服务器（其实不止HTTP服务器），一般主要用作**负载均衡**和**反向代理**



## 2、Nginx配置

### **反向代理配置：**

```sh
server {
    listen 80;
    location / {
        proxy_pass http://192.168.0.112:8080; # 应用服务器HTTP地址
    }
}
```

### **负载均衡配置：**

```sh
upstream myapp {
    server 192.168.0.111:8080; # 应用服务器1
    server 192.168.0.112:8080; # 应用服务器2
}
server {
    listen 80;
    location / {
        proxy_pass http://myweb;
    }
}
```

**upstream 每个设备的状态:**

- down ：表示单前的server暂时不参与负载。
- weight ：默认为1，weight越大，负载的权重就越大。
- max_fails ：允许请求失败的次数默认为1，当超过最大次数时，返回proxy_next_upstream 模块定义的错误。
- fail_timeout：max_fails 次失败后，暂停的时间。
- backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。



### 举个栗子:

#### *nginx.conf*

```sh
    upstream mycluster{
        server 192.168.40.25:8080;
        server 192.168.40.26:8080;
    }
    server {
        listen       80;
        server_name  localhost;
         
        location / {
            proxy_pass http://mycluster;
            include D:\Program\Nginx\proxy.conf;
        }

        #静态文件交给nginx处理
        location ~ .*\.(html|js|gif|jpg|jpeg|png|bmp|rar|zip|txt|css|xls|mp4)$ {
            root E:\htmis\webapp;
            expires 3d;
            index index.html;
        }
    }
```

#### *proxy.conf*

```sh
proxy_redirect off; 
proxy_set_header Host $host; 
proxy_set_header X-Real_IP $remote_addr; 
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
client_max_body_size 10m; 
client_body_buffer_size 128k; 
proxy_connect_timeout 90; 
proxy_send_timeout 90;
proxy_read_timeout 90; 
proxy_buffers 8 8k;
```

### 虚拟主机配置

```sh
server {
    listen 80 default_server;
    server_name _;
    return 444; # 过滤其他域名的请求，返回444状态码
}
server {
    listen 80;
    server_name www.aaa.com; # www.aaa.com域名
    location / {
        proxy_pass http://localhost:8080; # 对应端口号8080
    }
}
server {
    listen 80;
    server_name www.bbb.com; # www.bbb.com域名
    location / {
        proxy_pass http://localhost:8081; # 对应端口号8081
    }
}
```



## 3、nginx常用命令

| 命令            | 注释                                 |
| --------------- | ------------------------------------ |
| nginx           | 启动nginx，按照默认路径              |
| nginx -t        | 测试配置正确性、也可查询默认配置路径 |
| nignx -c 路径   | 按照指定路径启动                     |
| nginx -s reload | 平滑重启                             |
| nginx -s stop   | 停止nginx                            |
| nginx -v        | 显示 nginx 的版本                    |
| nginx -V        | nginx 的版本，编译器版本和配置参数。 |


