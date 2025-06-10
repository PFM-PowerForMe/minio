# Minio

[![PFM-Upstream-Sync](https://github.com/PFM-PowerForMe/minio/actions/workflows/fork-sync.yml/badge.svg)](https://github.com/PFM-PowerForMe/minio/actions/workflows/fork-sync.yml)

## 简介
Minio是一个高性能、兼容S3的对象存储，在GNU AGPLv3许可下开源。 

## 如何部署?
1. 前置条件:
```shell
mkdir -p /etc/containers/systemd
podman network create deploy
```

2. 部署 Minio
```
mkdir -p /opt/podman-data/env
nvim /opt/podman-data/env/minio.env

```
```
MINIO_ROOT_USER=user
MINIO_ROOT_PASSWORD=passwd
MINIO_SERVER_URL=https://example.com
MINIO_BROWSER_REDIRECT_URL=https://console.example.com

```
```
nvim /etc/containers/systemd/minio.container
```
```
# /etc/containers/systemd/minio.container

[Unit]
Description=The minio container
Wants=network-online.target
After=network-online.target

[Container]
AutoUpdate=registry
ContainerName=minio
Timezone=local
Network=deploy
Volume=minio-data:/data
Volume=minio-conf:/root/.minio
EnvironmentFile=/opt/podman-data/env/minio.env
Exec=server /data --console-address ":9090" -address ":9000"
PublishPort=127.0.0.1:6002:9000
PublishPort=127.0.0.1:6001:9090
Image=docker.io/minio/minio:RELEASE.2025-04-22T22-12-26Z

[Service]
Restart=on-failure
RestartSec=30s
StartLimitInterval=30
TimeoutStartSec=900
TimeoutStopSec=70

[Install]
WantedBy=multi-user.target default.target
```