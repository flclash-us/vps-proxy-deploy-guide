# V2Ray WebSocket+TLS+Web 伪装高级配置

本文介绍如何配置一个难以被识别的 V2Ray 节点，使用 WebSocket 传输 + TLS 加密 + Web 伪装。

## 服务端配置

### 1. 安装 V2Ray

```bash
bash <(curl -s https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
```

### 2. 配置 V2Ray

```json
{
  "inbounds": [{
    "port": 10000,
    "listen": "127.0.0.1",
    "protocol": "vmess",
    "settings": {"clients": [{"id": "your-uuid"}]},
    "streamSettings": {
      "network": "ws",
      "wsSettings": {"path": "/v2ray"}
    }
  }],
  "outbounds": [{"protocol": "freedom"}]
}
```

### 3. Nginx 反向代理

```nginx
server {
    listen 443 ssl;
    server_name your-domain.com;
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location /v2ray {
        proxy_pass http://127.0.0.1:10000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }

    location / {
        root /var/www/normal-site;
        index index.html;
    }
}
```

## 性能优化

- 启用 BBR 加速
- 使用 CDN 隐藏真实 IP
- 配置 TLS 1.3

## 常见问题

**连接不稳定？** 检查 Nginx 日志和 V2Ray 日志。

**TLS 证书失效？** 使用 acme.sh 自动续期。

---

推荐工具：

- [Clash for Windows](https://clashforwindows.site/)
- [ClashMI](https://clashmi.site/)
- [FlClash](https://flclash.us/)
