# 以 Docker 實作邊緣運算系統

- NTHU AIMS 智慧製造專題與實作報告
- 109005510 簡光正
- 2022.05.07

## 課程內容 Git Repo

https://github.com/kcchien/aims-docker-lab

## 投影片下載

[投影片下載](https://raw.githubusercontent.com/kcchien/aims-docker-lab/main/Docker%E5%AF%A6%E4%BD%9C%E6%99%BA%E6%85%A7%E5%B7%A5%E5%BB%A0%E9%82%8A%E7%B7%A3%E9%81%8B%E7%AE%97%E7%B3%BB%E7%B5%B1.pdf)

## Play with Docker

線上的 docker 測試環境

https://labs.play-with-docker.com/

> 帳號：`aims001` 密碼：`aims12345`

![Play with docker lab](https://i.imgur.com/pIPmBM5.png)

## 安裝 docker engine & docker compose

### docker engine

[Windows installaton](https://docs.docker.com/desktop/windows/install/)

[macOS installation](https://docs.docker.com/desktop/mac/install/)

## Lab #1 Hello World

### 執行容器

```bash=
docker run hello-world
```

### 停止容器運行

```bash=
docker container stop hello-world
```

### 運行已停止的容器

```bash=
docker container start hello-world
```

### 刪除容器

```bash=
docker rm hello-world
```

## Lab #2 Web Server

執行一個 Nginx 的 Web 伺服器，並將電腦上的 5000 port 導到容器的 80 port

### 執行容器

```bash=
docker run --name webserver –p 5000:80 nginx
```

### 停止容器運行

```bash=
docker container stop webserver
```

### 運行已停止的容器

```bash=
docker container start webserver
```

### 刪除容器

```bash=
docker rm webserver
```

## Lab #3 Node-RED + Grafana + InfluxDB (docker-compose 進階應用)

透過事先撰寫好的 docker-compose.yaml 檔案，直接一行指令完成部署多個服務

用 wget 指令將這份 docker-compose.yaml 下載到電腦(linux/macOS)

```bash=
wget https://github.com/kcchien/aims-docker-lab/raw/main/docker-compose.yaml
```

或是下方連結右鍵檔案亦可

[docker-compose.yaml](https://github.com/kcchien/aims-docker-lab/raw/main/docker-compose.yaml)

```bash=
# -d 參數代表要執行在背景的方式
docker-compose up -d

# 停止 docker-compose 執行的所有 Container
docker-compose stop

刪除 docker-compose 的所有 Container
docker-compose rm
```

The services in the app run on the following ports:

| Host Port | Service  |
| --------- | -------- |
| 3000      | Grafana  |
| 8086      | InfluxDB |
| 1880      | Node-RED |

docker-compose.yaml 內容

```yaml=
version: "3"
services:
  nodered:
    image: nodered/node-red:latest
    container_name: nodered
    restart: always
    ports:
      - "1880:1880"
    networks:
      - grafana_network
    volumes:
      - nodered_data:/data
    environment:
      - TZ=Asia/Taipei
    depends_on:
      - grafana

  grafana:
    image: grafana/grafana
    container_name: grafana
    restart: always
    ports:
      - 3000:3000
    networks:
      - grafana_network
    volumes:
      - grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=password
    depends_on:
      - influxdb

  influxdb:
    image: influxdb:latest
    container_name: influxdb
    restart: always
    ports:
      - 8086:8086
    networks:
      - grafana_network
    volumes:
      - influxdb_data:/var/lib/influxdb
    environment:
      - INFLUXDB_DB=grafana
      - INFLUXDB_USER=admin
      - INFLUXDB_USER_PASSWORD=password
      - INFLUXDB_ADMIN_ENABLED=true
      - INFLUXDB_ADMIN_USER=admin
      - INFLUXDB_ADMIN_PASSWORD=password

networks:
  grafana_network:

volumes:
  nodered_data:
  grafana_data:
  influxdb_data:
```

## Extra Lab: Tersorflow with Jupyter server

https://www.tensorflow.org/install/docker?hl=zh-tw

使用 TensorFlow 啟動 Jupyter Notebook 伺服器, 無需 GPU 支援

```bash=
docker run -it --rm -p 8888:8888 tensorflow/tensorflow:nightly-jupyter
```
