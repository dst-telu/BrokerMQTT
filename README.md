# Instalasi Docker Broker MQTT Mosquitto/HiveMQ

## 1. Instal Docker

```
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF
sudo apt update
```

```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
## 2. HiveMQ

```
sudo docker run -p 8080:8080 -p 1883:1883 hivemq/hivemq4
```

## 3. Mosquitto

```
sudo docker run -it -d --name mosquitto_broker -p 1883:1883 -v mosquitto_data:/mosquitto/data -v mosquitto_log:/mosquitto/log -v mosquitto_config:/mosquitto/config eclipse-mosquitto:latest
```
## 4. Telegraf
```
cat <<EOF | sudo tee /etc/apt/sources.list.d/influxdata.list
deb https://repos.influxdata.com/ubuntu $(lsb_release -cs) stable
EOF
```
```
curl -fsSL https://repos.influxdata.com/influxdata-archive_compat.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/influxdata.gpg
```
```
sudo apt update
```
```
sudo apt install telegraf
```
### Setting telegraf ketika Sudah install InfluxDB dan Grafana
```
sudo nano /etc/telegraf/telegraf.d/mqtt_sys.conf
```
### Masukan ini
```
[[inputs.mqtt_consumer]]
  servers = ["tcp://localhost:1883"]
  topics = [
    "$SYS/#"
  ]
  username = ""
  password = ""
  data_format = "value"
  qos = 0

[[outputs.influxdb_v2]]
  urls = ["http://localhost:8086"]
  token = "TOKEN_KAMU"
  organization = "ORG_KAMU"
  bucket = "telegraf"
```

## 5. Mematikan dan menyalakan Container Broker MQTT

### Mematikan
```
sudo docker ps
```
cari container HiveMQ/Mosquitto
```
sudo docker container stop [NAMA CONTAINER]
```
### Menghidupkan
```
sudo docker container ls -a
```
cari container HiveMQ/Mosquitto
```
sudo docker container start [NAMA CONTAINER]
```
