# MQTT Service with Docker

## Giới thiệu
MQTT Service là một dịch vụ sử dụng giao thức MQTT (Message Queuing Telemetry Transport) để truyền nhận dữ liệu giữa các thiết bị. Dự án này cung cấp môi trường MQTT đóng gói sẵn trong Docker để dễ dàng triển khai.

## Yêu cầu
- Docker
- Docker Compose

## Cài đặt

### Cách 1: Sử dụng Eclipse Mosquitto với Docker Compose

1. Tạo thư mục cấu hình và dữ liệu:
```bash
mkdir -p mosquitto/config
mkdir -p mosquitto/data
mkdir -p mosquitto/log
```

2. Tạo file `mosquitto/config/mosquitto.conf` với nội dung:
```
listener 1883
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
allow_anonymous true
```

3. Tạo file `docker-compose.yml` với nội dung:
```yaml
version: '3'
services:
  mosquitto:
    image: eclipse-mosquitto:latest
    container_name: mqtt-broker
    ports:
      - "1883:1883"
  
    volumes:
      - ./mosquitto/config:/mosquitto/config
      - ./mosquitto/data:/mosquitto/data
      - ./mosquitto/log:/mosquitto/log
    restart: always
```

4. Khởi chạy MQTT broker:
```bash
docker-compose up -d
```

### Cách 2: Sử dụng image Docker đơn lẻ

```bash
docker run -d --name mqtt-broker \
  -p 1883:1883 \
  eclipse-mosquitto:latest
```

## Kiểm tra hoạt động

Cài đặt MQTT client để kiểm tra:

```bash
# Cài đặt mosquitto-clients (trên Ubuntu/Debian)
sudo apt install mosquitto-clients

# Hoặc trên Windows, tải về từ https://mosquitto.org/download/

# Subscribe để nhận tin nhắn
mosquitto_sub -h localhost -t "test/topic" -v

# Mở terminal khác và publish tin nhắn
mosquitto_pub -h localhost -t "test/topic" -m "Hello MQTT"
```

## Bảo mật MQTT (Nâng cao)

Để bảo mật MQTT broker, cập nhật file cấu hình `mosquitto.conf`:

```
listener 1883
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
allow_anonymous false
password_file /mosquitto/config/password
```

Tạo file password:

```bash
docker exec -it mqtt-broker mosquitto_passwd -c /mosquitto/config/password username
# Nhập password khi được yêu cầu
```

Khởi động lại container:

```bash
docker-compose restart
```

## Tài liệu tham khảo
- [Eclipse Mosquitto](https://mosquitto.org/)
- [MQTT Protocol](https://mqtt.org/)
- [Mosquitto Docker Hub](https://hub.docker.com/_/eclipse-mosquitto)