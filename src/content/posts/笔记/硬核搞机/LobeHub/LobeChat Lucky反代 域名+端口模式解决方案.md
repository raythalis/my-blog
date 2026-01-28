---
title: LobeHub Lucky反代 域名+端口模式解决方案
published: 2026-01-28
description: ""
image: ""
tags:
  - AI
  - Lucky
  - Lobe
category: 硬核搞机
draft: false
updated: 2026-01-29
---

# LobeChat Lucky反代 域名+端口模式解决方案

Lobe目前支持的模式有三种
- [本地模式（默认）](https://lobehub.com/zh/docs/self-hosting/platform/docker-compose#%E6%9C%AC%E5%9C%B0%E6%A8%A1%E5%BC%8F)：仅能在本地访问，不支持局域网 / 公网访问，适用于初次体验；
- [端口模式](https://lobehub.com/zh/docs/self-hosting/platform/docker-compose#%E7%AB%AF%E5%8F%A3%E6%A8%A1%E5%BC%8F)：支持局域网 / 公网的 `http` 访问，适用于无域名或内部办公场景使用；
- [域名模式](https://lobehub.com/zh/docs/self-hosting/platform/docker-compose#%E5%9F%9F%E5%90%8D%E6%A8%A1%E5%BC%8F)：支持局域网 / 公网在使用反向代理下的 `http/https` 访问，适用于个人或团队日常使用

那么有的伙伴想要部署lobehub在自己的NAS，然后通过Lucky（或其他反代工具）绑定域名，并监听一个固定的端口实现，会遇到各种问题
类似的问题有：
[# [Request] 提供域名模式部署、外网带端口https访问教程](https://github.com/lobehub/lobehub/issues/5936)
[# [Bug] 使用docker-compose部署端口模式，nginx反向代理后 无法登录](https://github.com/lobehub/lobehub/issues/6575)

![](<./assets/LobeChat Lucky反代 域名+端口模式解决方案/file-20260129034928458.jpg>)
日志报错：
```
-------------------------------------

[Database] Start to migration...

✅ database migration pass.

-------------------------------------

   ▲ Next.js 15.1.5

   - Local:        http://localhost:3210

   - Network:      http://0.0.0.0:3210



 ✓ Starting...

 ✓ Ready in 70ms

{

  allowDangerousEmailAccountLinking: true,

  clientId: undefined,

  clientSecret: undefined,

  platformType: 'WebsiteApp',

  profile: [Function: profile]

}

(node:28) [DEP0040] DeprecationWarning: The `punycode` module is deprecated. Please use a userland alternative instead.

(Use `node --trace-deprecation ...` to show where the warning was created)

{

  allowDangerousEmailAccountLinking: true,

  clientId: undefined,

  clientSecret: undefined,

  platformType: 'WebsiteApp',

  profile: [Function: profile]

}

Error getting changelog lists: TypeError: fetch failed

    at async m.getChangelogIndex (.next/server/app/@modal/(.)changelog/modal/page.js:74:15441)

    at async m.getLatestChangelogId (.next/server/app/@modal/(.)changelog/modal/page.js:74:15300)

    at async g (.next/server/app/(main)/chat/(workspace)/page.js:1208:16149) {

  [cause]: [AggregateError: ] { code: 'ECONNREFUSED' }

}

Error: fetch failed

    at context.fetch (/app/node_modules/.pnpm/next@15.1.5_@babel+core@7.26.0_@opentelemetry+api@1.9.0_@playwright+test@1.49.1_react-dom@19._gjluzxyayy7ntgi7rjyylzka3q/node_modules/next/dist/server/web/sandbox/context.js:303:38)

    at /app/.next/server/edge-chunks/24.js:1:115797

    at W (/app/.next/server/edge-chunks/24.js:1:120818)

    at /app/.next/server/edge-chunks/24.js:1:123586

    at /app/.next/server/edge-chunks/24.js:1:130198

    at e.with (/app/.next/server/edge-chunks/24.js:1:10880)

    at e.with (/app/.next/server/edge-chunks/24.js:1:11948)

    at e.startActiveSpan (/app/.next/server/edge-chunks/24.js:1:14064)

    at e.startActiveSpan (/app/.next/server/edge-chunks/24.js:1:14362)

    at /app/.next/server/edge-chunks/24.js:1:129720 {

  

}

[auth][error] TypeError: fetch failed

    at node:internal/deps/undici/undici:13484:13

    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)

    at async iY (/app/.next/server/chunks/18702.js:368:46924)

    at async iQ (/app/.next/server/chunks/18702.js:368:49798)

    at async i5 (/app/.next/server/chunks/18702.js:368:52440)

    at async i4 (/app/.next/server/chunks/18702.js:368:56596)

    at async tr.do (/app/node_modules/.pnpm/next@15.1.5_@babel+core@7.26.0_@opentelemetry+api@1.9.0_@playwright+test@1.49.1_react-dom@19._gjluzxyayy7ntgi7rjyylzka3q/node_modules/next/dist/compiled/next-server/app-route.runtime.prod.js:18:17582)

    at async tr.handle (/app/node_modules/.pnpm/next@15.1.5_@babel+core@7.26.0_@opentelemetry+api@1.9.0_@playwright+test@1.49.1_react-dom@19._gjluzxyayy7ntgi7rjyylzka3q/node_modules/next/dist/compiled/next-server/app-route.runtime.prod.js:18:22212)

    at async doRender (/app/node_modules/.pnpm/next@15.1.5_@babel+core@7.26.0_@opentelemetry+api@1.9.0_@playwright+test@1.49.1_react-dom@19._gjluzxyayy7ntgi7rjyylzka3q/node_modules/next/dist/server/base-server.js:1452:42)

    at async responseGenerator (/app/node_modules/.pnpm/next@15.1.5_@babel+core@7.26.0_@opentelemetry+api@1.9.0_@playwright+test@1.49.1_react-dom@19._gjluzxyayy7ntgi7rjyylzka3q/node_modules/next/dist/server/base-server.js:1822:28)

[NextAuth] Error: {

  cause: 'Configuration',

  message: 'Wrong configuration, make sure you have the correct environment variables set. Visit https://lobehub.com/docs/self-hosting/advanced/authentication for more details.',

  name: 'NextAuth Error'

}
```

原因是：**lobehub在交互时会用到各种回调，如果你全部都填写了域名+端口的模式，回调时你docker本身向域名（外网）发出请求，但会被路由器解析成发往内网的，从而失败。**

issue中有人提到了：
![](<./assets/LobeChat Lucky反代 域名+端口模式解决方案/file-20260129034809701.jpg>)
## 解决方案

解决方案也很简单，分两步骤
1. docker-compose中将发往外网的**请求直接发给内网**（回流）
2. 针对于反代端口写死的情况，docker-compose中要部署一个**子反代**来映射到不同服务的端口
### 回流
docker-compose中添加一个**extra-hosts**
```
name: lobe-chat-database
services:
  network-service:
    image: alpine
    container_name: lobe-network
    restart: always
    ports:
      - '${MINIO_PORT}:${MINIO_PORT}' # MinIO API
      - '9004:9001' # MinIO Console
      - '${CASDOOR_PORT}:${CASDOOR_PORT}' # Casdoor
      - '${LOBE_PORT}:3210' # LobeChat
      - '3000:3000' # Grafana
      - '4318:4318' # otel-collector HTTP
      - '4317:4317' # otel-collector gRPC
    command: tail -f /dev/null
    networks:
      - lobe-network
    extra_hosts:
      - "lobe-casdoor.example.com:127.0.0.1"
      - "lobe-minio.example.com.cn:127.0.0.1"
      - "lobe.example.com.cn:127.0.0.1"
```

### 子反代
docker-compose中添加一个**nginx服务**
```
internal-proxy:
    image: nginx:alpine
    container_name: lobe-internal-proxy
    network_mode: 'service:network-service'
    restart: always
    volumes:
      - ./internal_nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./example.crt:/etc/nginx/ssl/example.crt:ro
      - ./example.key:/etc/nginx/ssl/example.key:ro
```

internal_nginx.conf文件（换成你自己的前缀和端口）
```
server {
    listen 16666 ssl;
    server_name lobe-casdoor.example.com;

    ssl_certificate /etc/nginx/ssl/example.crt;
    ssl_certificate_key /etc/nginx/ssl/example.key;

    location / {
        proxy_pass http://127.0.0.1:8007;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https; # 告诉后端这是加密请求
    }
}

server {
    listen 16666 ssl;
    server_name lobe-minio.example.com;

    ssl_certificate /etc/nginx/ssl/example.crt;
    ssl_certificate_key /etc/nginx/ssl/example.key;

    location / {
        proxy_pass http://127.0.0.1:9005;
        # 这一行非常关键：强制将 Host 设置为你在 Casdoor 里填写的域名
        proxy_set_header Host lobe-minio.example.com:16666; 

        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;

        # 解决签名匹配中可能存在的双重斜杠问题
        proxy_redirect off;
        client_max_body_size 0;
    }
}

server {
    listen 16666 ssl;
    server_name lobe.example.com;

    ssl_certificate /etc/nginx/ssl/example.crt;
    ssl_certificate_key /etc/nginx/ssl/example.key;

    location / {
        proxy_pass http://127.0.0.1:3210;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

这里要注意，证书你可以用自签命令生成，但是不推荐，后面minio的时候会有证书问题，这个有时间再开一个教程讲。我这里用的是lucky的**Lets_Encrypt**证书下载下来使用的。

### 最后一点
lobe最好加上环境变量：
```
      - 'NODE_TLS_REJECT_UNAUTHORIZED=0'
```

本文已向github仓库提交
