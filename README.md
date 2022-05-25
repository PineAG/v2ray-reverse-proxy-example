使用三个v2ray实例实现有简单身份验证功能的NAT穿透效果。

在内网服务器、外网中继服务器与客户机分别用v2ray运行下列文件，即可通过客户机的10003端口访问内网服务器的10000端口。

* client.json: 客户端配置文件
* forward.json: 中继服务器配置文件
* server.json: 内网服务器配置文件

**注意内网服务器要先于中继服务器启动**

启动v2ray:
```bash
v2ray -c client.json/forward.json/server.json
```

## 技术细节

内网服务器与客户机分别通过vless连接外网的中继服务器，中继服务器通过v2ray的反向代理功能连接两台机器，接着客户机的dokodemo-door将流量发往内网服务器的freedom。

## 更改配置

* `2fb9eaac-6eae-4a2e-89a6-a9bc956204d9`: 内网服务器-中继服务器连接口令
* `<server-key>`: 内网服务器-中继服务器加密密钥
* `5d8bbf9d-f173-49bb-a3a9-29daf0f76634`: 中继服务器-客户机连接口令
* `3a5b8ab6-5369-4c28-979c-23f09141928b`: 客户端-内网服务器的端对端加密密钥
* `<client-key>`: 中继服务器-客户机加密密钥
* `10000`: 目标端口
* `10001`: 内网服务器-中继服务器连接端口
* `10002`: 中继服务器-客户机连接端口
* `10003`: 客户机向本地开放的端口，流量通向目标端口
* `10004`: 用于解密端对端加密的Inbound端口
* `outbounds/vnext/address`: 中继服务器IP

## 测试配置文件

### 启动目标网页服务器

```bash
cd public
python3 -m http.server 10000
```

此时访问`127.0.0.1:10000`可看到`Success`字样。

### 启动v2ray服务器

在三个独立终端中分别运行

#### 内网服务器
```bash
./v2ray -c server.json
```

#### 外网中继服务器
```bash
./v2ray -c forward.json
```

#### 客户端
```bash
./v2ray -c client.json
```

此时访问`127.0.0.1:10003`可看到`Success`字样。
