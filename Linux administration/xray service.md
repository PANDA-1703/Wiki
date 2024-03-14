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
                "domains": [],
                "queryStrategy": ""
            },
            {
                "address": "localhost",
                "domains": [],
                "queryStrategy": ""
            }
        ],
        "tag": "dns"
    },
    "inbounds": [
        {
            "listen": "127.0.0.1",
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
                "enabled": true,
                "metadataOnly": false,
                "routeOnly": true
            },
            "tag": "socks-in"
        },
        {
            "listen": "127.0.0.1",
            "port": 2081,
            "protocol": "http",
            "sniffing": {
                "destOverride": [
                    "http",
                    "tls",
                    "quic"
                ],
                "enabled": true,
                "metadataOnly": false,
                "routeOnly": true
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
            "flow": null,
            "protocol": "vless",
            "settings": {
                "vnext": [
                    {
                        "address": "<ip-address>",
                        "port": <port>,
                        "users": [
                            {
                                "encryption": "none",
                                "flow": "xtls-rprx-vision",
                                "id": "<your-id>"
                            }
                        ]
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp",
                "realitySettings": {
                    "fingerprint": "chrome",
                    "publicKey": "<publicKey>",
                    "serverName": "www.google.com",
                    "shortId": "<shortId>",
                    "spiderX": ""
                },
                "security": "reality"
            },
            "tag": "proxy"
        },
        {
            "domainStrategy": "",
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "domainStrategy": "",
            "protocol": "freedom",
            "tag": "bypass"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        },
        {
            "protocol": "dns",
            "proxySettings": {
                "tag": "proxy",
                "transportLayer": true
            },
            "settings": {
                "address": "8.8.8.8",
                "network": "tcp",
                "port": 53,
                "userLevel": 1
            },
            "tag": "dns-out"
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
                "outboundTag": "direct",
                "protocol": [
                    "bittorrent"
                ],
                "type": "field"
            },
            {
                "inboundTag": [
                    "socks-in",
                    "http-in"
                ],
                "outboundTag": "dns-out",
                "port": "53",
                "type": "field"
            },
            {
                "domain": [
                    "geosite:category-ads-all"
                ],
                "outboundTag": "block",
                "type": "field"
            },
            {
                "domain": [
                    "domain:chat.openai.com",
                    "domain:openai.com",
                    "domain:myip.com",
                    "domain:radarr.com",
                    "domain:jackett.com",
                    "domain:sonarr.com"
                ],
                "outboundTag": "proxy",
                "type": "field"
            },
            {
                "ip": [
                    "geoip:ru",
                    "geoip:private"
                ],
                "outboundTag": "bypass",
                "type": "field"
            },
            {
                "domain": [
                    "domain:2ip.ru",
                    "domain:cn,",
                    "domain:xn--fiqs8s,",
                    "domain:xn--fiqz9s,",
                    "domain:xn--55qx5d,",
                    "domain:xn--io0a7i,",
                    "domain:ru,",
                    "domain:xn--p1ai,",
                    "domain:by,",
                    "domain:xn--90ais,",
                    "domain:ir,",
                    "domain:yandex.net,",
                    "domain:yastatic.net,",
                    "domain:lenta.com,",
                    "domain:gldn.net,",
                    "domain:mycdn.me,",
                    "domain:avito.st,",
                    "domain:gismeteo.st,",
                    "domain:gismeteo.net,",
                    "domain:ozonusercontent.com,",
                    "domain:mradx.net,",
                    "domain:aestatic.net,",
                    "domain:pushwoosh.com,",
                    "domain:vk.com,",
                    "domain:userapi.com,",
                    "domain:otm-r.com,",
                    "domain:2gis.com,",
                    "domain:championat.com,",
                    "domain:rus-tv.su,",
                    "domain:kpcdn.net,",
                    "domain:kp.house,",
                    "domain:kp.kg,",
                    "domain:kaspersky-labs.com,",
                    "domain:yadro.com,",
                    "domain:boosty.to,",
                    "domain:eaglecdn.com,",
                    "domain:livejournal.com,",
                    "domain:livejournal.net,",
                    "domain:edc.sale,",
                    "domain:stellarlabs.ai,",
                    "domain:myminsk.com,",
                    "domain:sputniknews.com,",
                    "domain:sputnikglobe.com,",
                    "domain:digikala.com,",
                    "domain:bale.ai,",
                    "domain:qq.com,",
                    "domain:gtimg.com,",
                    "domain:zhihu.com,",
                    "domain:geetest.com,",
                    "domain:zhimg.com,",
                    "domain:126.net,",
                    "domain:163.com,",
                    "domain:25ku.com,",
                    "domain:sohu.com,",
                    "domain:bcebos.com,",
                    "domain:ifengimg.com,",
                    "domain:taobao.com,",
                    "domain:bdstatic.com,",
                    "domain:ad-survey.com,",
                    "domain:ifeng.com,",
                    "domain:alicdn.com,",
                    "domain:jd.com,",
                    "domain:aliapp.org,",
                    "domain:aliyun.com,",
                    "domain:iqiyi.com,",
                    "domain:mgtv.com,",
                    "domain:iqiyipic.com,",
                    "domain:hdslb.com,",
                    "domain:360buyimg.com,",
                    "domain:bilibili.com,",
                    "domain:alibaba.com,",
                    "domain:xiami.com,",
                    "domain:yinyuetai.com,",
                    "domain:ctrip.com,",
                    "domain:tripcdn.com,",
                    "domain:c-ctrip.com,",
                    "domain:fliggy.com,",
                    "domain:qyerstatic.com,",
                    "domain:baidu.com,",
                    "domain:mafengwo.net,",
                    "domain:meituan.com,",
                    "domain:dianping.com,",
                    "domain:bdimg.com,",
                    "domain:chuimg.com,",
                    "domain:autonavi.com,",
                    "domain:amap.com,",
                    "domain:mmstat.com,",
                    "domain:googleapis.com,",
                    "domain:mozilla.com,",
                    "domain:microsoft.com,",
                    "domain:apple.com,",
                    "domain:habr.com,",
                    "domain:habrastorage.org,",
                    "domain:viqeo.tv,",
                    "domain:telegram.org"
                ],
                "outboundTag": "bypass",
                "type": "field"
            },
            {
                "outboundTag": "proxy",
                "port": "0-65535",
                "type": "field"
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