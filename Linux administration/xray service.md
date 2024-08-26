# Xray-client в терминале linux.

## Install xray
I used: <u>XTLS</u> <https://github.com/XTLS/Xray-install> and
<u>easy-xray</u> <https://github.com/EvgenyNerush/easy-xray/blob/main/README.ru.md#linux-1>

*  At first, we need to download XTLS from the repository [XTLS/Xray-install](https://github.com/XTLS/Xray-install).


Install & Upgrade Xray-core and geodata with `User=nobody`, but will NOT overwrite `User` in existing service files

```shell 
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
```

Update geoip.dat and `geosite.dat` only

```shell
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install-geodata
```

* Then we need used guide [Clients](https://github.com/EvgenyNerush/easy-xray/blob/main/README.ru.md#клиенты).


Download the file `customgeo.dat` from this repository and copy it to the folder `/usr/local/share/xray`. 

Create our configuration `config.json`:
<details>
<summary>config.json</summary>

```json
{
  "dns": {
    "disableFallback": true,
    "servers": [
      {
        "address": "https://8.8.8.8/dns-query",
        "domains": [
          "domain:youtube.com",
          "domain:chat.openai.com",
          "domain:openai.com",
          "domain:googlevideo.com",
          "domain:ytimg.com",
          "domain:youtu.be",
          "domain:ggpht.com",
          "domain:youtubei.googleapis.com",
          "domain:yt4.ggpht.com",
          "domain:ytimg.l.google.com",
          "domain:nhacmp3youtube.com",
          "domain:googleusercontent.com",
          "domain:googleapis.com",
          "domain:gstatic.com"
        ],
        "queryStrategy": ""
      }
    ],
    "tag": "dns"
  },
  "inbounds": [
    {
      "listen": "0.0.0.0",
      "port": 2080,
      "protocol": "socks",
      "settings": {
        "udp": true
      },
      "sniffing": {
        "destOverride": [
          "http",
          "tls",
          "quic"
        ],
        "enabled": true
      },
      "tag": "socks-in"
    },
    {
      "listen": "0.0.0.0",
      "port": 2081,
      "protocol": "http",
      "sniffing": {
        "destOverride": [
          "http",
          "tls",
          "quic"
        ],
        "enabled": true
      },
      "tag": "http-in"
    }
  ],
  "log": {
    "loglevel": "warning"
  },
  "outbounds": [
    {
      "domainStrategy": "AsIs",
      "protocol": "vless",
      "settings": {
        "vnext": [
          {
            "address": "<ip-server>",
            "port": 443,
            "users": [
              {
                "encryption": "none",
                "flow": "xtls-rprx-vision",
                "id": "<your-id-server>"
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "realitySettings": {
          "fingerprint": "chrome",
          "publicKey": "<your-publicKey-server>",
          "serverName": "www.google.com",
          "shortId": "<your-shortId-server>"
        },
        "security": "reality"
      },
      "tag": "proxy"
    },
    {
      "protocol": "freedom",
      "tag": "direct"
    },
    {
      "protocol": "blackhole",
      "tag": "block"
    }
  ],
  "policy": {
    "levels": {
      "1": {
        "connIdle": 30
      }
    },
    "system": {
      "statsOutboundDownlink": true,
      "statsOutboundUplink": true
    }
  },
  "routing": {
    "domainStrategy": "AsIs",
    "rules": [
      {
        "domain": [
          "domain:youtube.com",
          "domain:chat.openai.com",
          "domain:openai.com",
          "domain:googlevideo.com",
          "domain:ytimg.com",
          "domain:youtu.be",
          "domain:ggpht.com",
          "domain:youtubei.googleapis.com",
          "domain:yt4.ggpht.com",
          "domain:ytimg.l.google.com",
          "domain:nhacmp3youtube.com",
          "domain:googleusercontent.com",
          "domain:googleapis.com",
          "domain:gstatic.com"
        ],
        "outboundTag": "proxy",
        "type": "field"
      },
      {
        "outboundTag": "direct",
        "type": "field",
        "domain": [
          "geosite:category-ads-all",
          "geosite:category-porn"
        ]
      }
    ]
  },
  "stats": {}
}

```
</details>   

and copy config
```shell
sudo cp config.json /usr/local/etc/xray/config.json
```

Finally, we launch the service
```shell
sudo systemctl start xray
```

## Launch and use proxy

```shell
export http_proxy=http://127.0.0.1:2081
export https_proxy=http://127.0.0.1:2081
export frp_proxy=http://127.0.0.1:2081
export socks_proxy=http://127.0.0.1:2080
```
