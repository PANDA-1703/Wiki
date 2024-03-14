# Xray-client в терминале linux.

## Install xray
I used: <u>XTLS</u> <https://github.com/XTLS/Xray-install> and
<u>easy-xray</u> <https://github.com/EvgenyNerush/easy-xray/blob/main/README.ru.md#linux-1>

*  At first, we need to download XTLS from the repository [XTLS/Xray-install][XTLS].

[XTLS]: https://github.com/XTLS/Xray-install
Install & Upgrade Xray-core and geodata with `User=nobody`, but will NOT overwrite `User` in existing service files

```shell 
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
```

Update geoip.dat and `geosite.dat` only

```shell
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install-geodata
```

* Then we need used guide [Clients][clients].

[clients]:https://github.com/EvgenyNerush/easy-xray/blob/main/README.ru.md#клиенты
Download the file `customgeo.dat` from this repository and copy it to the folder `/usr/local/share/xray`. 

