# ml307r4g访问公网ip的websocket通讯服务器。  
4g模块无法直接进行不加密的WebSocket通讯，需要为服务器地址配置https。这是一个简单的思路，每一步可以去搜索相对应的扩展教程。  

1、申请ssl证书并下载；申请域名的网站基本都有免费试用的ssl，我用的阿里云。  

2、安装nginx，搭服务器少不了经常用这个软件，建议网关处直接用openwrt，然后将nginx安装在openwrt上，图省劲也可以直接在服务器上安装。  

3、配置nginx，在配置文件的http中加入以下代码，并根据自己情况修改，配置教程一般在安装教程里都有，这里不再赘述。  

server {  
    listen port ssl; #port替换成你改小智固件时 wss://website:port/xiaozhi/v1 里的port。  
    server_name website; #website是你申请ssl的域名，就是小智后台的网址，这里不要带port。  
    root html;  
    index index.html index.htm;  
    ssl_certificate /cert/**.pem; #  
    ssl_certificate_key /cert/**.key; #这个和上面都是ssl的证书文件地址，nginx安装目录下新建的一个cert文件夹，下载下来放到里面，名字替换到**上。  
    ssl_session_timeout 5m;  
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;  
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;  
    ssl_prefer_server_ciphers on;  
    location / {  
        proxy_pass http://ip:port;  #这里的ip就是你搭建小智的服务器局域网地址，如果nginx安装在服务器上，直接用127.0.0.1就可以，port是你搭建服务器时开放的端口。  
        proxy_http_version 1.1;  
        proxy_set_header Upgrade $http_upgrade;  
        proxy_set_header Connection "upgrade";  
        proxy_set_header Host $host;  
        proxy_set_header X-Real-IP $remote_addr;  
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
        proxy_set_header X-Forwarded-Proto $scheme;  
    }  
}  
4、重启nginx生效。  

不出意外，你的小智就可以用4g访问自己的服务器了。
