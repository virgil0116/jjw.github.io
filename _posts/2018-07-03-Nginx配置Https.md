---
layout: post
title:  "Nginx配置Https"
date:   2018-07-03
categories: 系统安全
---

## Nginx配置https访问

1. 在阿里云上申请免费的https证书，先点击Symantec然后选择一个域名，选择免费DV SSL。
2. 在订单页面，点击补全信息。填写域名、填写个人信息(域名验证我选的文件验证方式)、系统生成scr(保存好证书和私钥)。
3. 将审核进度中的fileauth.txt文件下载下来，传到服务器的域名访问路径下。等待审核通过...
4. 审核通过以后，点击进入下载。按照文档配置...
5. 将系统生成文件上传到nginx安装目录下的cert目录中(新建cert目录)。
6. 配置nginx安装目录下的配置文件如下 -> ``
7. 测试配置成功。

```
server {
    listen 443;
    server_name localhost;
    ssl on;
    root html;
    index index.html index.htm;
    ssl_certificate   cert/214817160580059.pem;
    ssl_certificate_key  cert/214817160580059.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        root html;
        index index.html index.htm;
    }
}
```

### 已有项目迁移

1. 修改server基本配置，即上面代码。
2. 修改websockt。

```
# 修改websockt
location /cable {
    proxy_pass https://pdca_api_dev/cable;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
}
location @pdca_api_dev{
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Client-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass https://pdca_api_dev;
}
```

