# Họ tên: Vũ Việt Anh
# Lớp: K58KTP.K01
# MSSV: K225480106082
# MÔN HỌC: PHÁT TRIỂN ỨNG DỤNG VỚI MÃ NGUỒN MỞ - TEE0421

# Bài Làm
# A. Lý thuyết
## 1. Docker là gì? 
- Docker là một nền tảng mã nguồn mở cho phép đóng gói ứng dụng và toàn bộ môi trường chạy của nó (thư viện, cấu hình, dependencies) vào trong một đơn vị gọi là Container.
- Các khái niệm cơ bản:

| Khái niệm | Ý nghĩa |
|-----------|---------|
| **Image** | Khuôn mẫu chỉ đọc chứa mọi thứ cần để chạy app (code, runtime, thư viện) |
| **Container** | Instance đang chạy của một Image — môi trường cô lập |
| **Dockerfile** | File script để build Image tự động |
| **Docker Hub** | Kho lưu trữ Image công khai trên internet |
| **Docker Daemon** | Tiến trình nền quản lý Container, Image, Network, Volume |
| **Docker Client** | Công cụ CLI để người dùng giao tiếp với Docker Daemon |
| **Volume** | Cơ chế lưu trữ dữ liệu bền vững, độc lập với vòng đời Container |
| **Network** | Mạng ảo cho phép các Container giao tiếp với nhau |

## 2. Các keyword được sử dụng trong docker-compose.yml
```
version: "3.8"          # Phiên bản cú pháp của file compose

services:               # Khai báo các container (service) cần chạy
  ten_service:          # Tên service tự đặt
    image:              # Image sử dụng từ Docker Hub
    build:              # Build image từ Dockerfile local
    container_name:     # Đặt tên cụ thể cho container
    ports:              # Map port host:container
    volumes:            # Mount thư mục/volume vào container
    environment:        # Biến môi trường
    env_file:           # Đọc biến môi trường từ file .env
    depends_on:         # Service này phụ thuộc service khác (khởi động sau)
    networks:           # Gắn service vào network nào
    restart:            # Chính sách restart (no/always/on-failure)
    command:            # Lệnh chạy khi container khởi động
    entrypoint:         # Override entrypoint của image
    expose:             # Mở port nội bộ (không ra ngoài host)
    healthcheck:        # Kiểm tra sức khoẻ container

networks:               # Khai báo các mạng ảo
  ten_network:
    driver: bridge      # Loại driver (bridge/host/overlay)

volumes:                # Khai báo các volume dùng chung
  ten_volume:
    driver: local
```

| Keyword | Ý nghĩa | Ví dụ |
|---------|---------|-------|
| **version** | Phiên bản cú pháp của file compose | `version: "3.8"` |
| **services** | Khối khai báo tất cả các container cần chạy | `services:` |
| **image** | Chỉ định Image có sẵn từ Docker Hub để tạo container | `image: nginx:latest` |
| **build** | Build Image từ Dockerfile local thay vì dùng image có sẵn | `build: ./app` |
| **container_name** | Đặt tên cụ thể cho container | `container_name: my_app` |
| **ports** | Map port từ máy host vào container theo dạng `host:container` | `ports: - "8080:80"` |
| **volumes** | Mount thư mục hoặc volume vào container để lưu dữ liệu bền vững | `volumes: - ./data:/data` |
| **environment** | Khai báo biến môi trường cho container | `environment: - DB_PASS=123` |
| **env_file** | Đọc biến môi trường từ file .env bên ngoài | `env_file: - .env` |
| **depends_on** | Xác định thứ tự khởi động, service này sẽ chạy sau service kia | `depends_on: - db` |
| **networks** | Gắn service vào một hoặc nhiều network | `networks: - mynetwork` |
| **restart** | Chính sách tự khởi động lại container khi bị lỗi hoặc stop | `restart: always` |
| **command** | Lệnh chạy khi container khởi động, override CMD trong Dockerfile | `command: python app.py` |
| **entrypoint** | Override entrypoint mặc định của image | `entrypoint: /bin/sh` |
| **expose** | Mở port nội bộ giữa các container, không ra ngoài host | `expose: - "3000"` |
| **healthcheck** | Kiểm tra định kỳ xem container có hoạt động bình thường không | `healthcheck: test: ["CMD", "curl", "-f", "http://localhost"]` |
| **networks** (top-level) | Khai báo các mạng ảo dùng chung cho nhiều service | `networks: mynetwork: driver: bridge` |
| **volumes** (top-level) | Khai báo các volume dùng chung và lưu trữ lâu dài | `volumes: db_data: driver: local` |
| **driver: bridge** | Loại network mặc định, các container cùng bridge có thể nói chuyện với nhau | `driver: bridge` |
| **driver: local** | Loại volume lưu dữ liệu trên máy host | `driver: local` |

## 3. Ưu điểm khi triển app sử dụng docker là gì?
- Nhất quán môi trường (Environment Consistency): Docker đóng gói toàn bộ ứng dụng cùng với môi trường chạy (thư viện, cấu hình, dependencies) vào trong Container. Nhờ đó, app chạy giống hệt nhau trên mọi máy: máy developer, máy test, máy chủ production — không còn tình trạng "máy tôi chạy được nhưng máy bạn không chạy được".
- Cô lập (Isolation): Mỗi Container hoạt động độc lập với nhau và với hệ điều hành host. Nếu một Container bị lỗi hay crash, các Container khác vẫn hoạt động bình thường. Nhiều ứng dụng dùng các phiên bản thư viện khác nhau cũng không xung đột.
- Triển khai nhanh (Fast Deployment)
  + Thay vì cài đặt thủ công từng thành phần, chỉ cần một lệnh duy nhất là toàn bộ hệ thống chạy lên: `docker compose up -d`
  + Tiết kiệm hàng giờ cài đặt thủ công xuống còn vài phút.
- Nhẹ hơn Máy Ảo (Lightweight): Container chia sẻ kernel của hệ điều hành host thay vì chạy một OS riêng như máy ảo (VM). Do đó Container khởi động trong vài giây, tiêu tốn ít RAM và CPU hơn VM rất nhiều.
- Dễ Backup và Khôi Phục
Có thể lưu toàn bộ Container ra file, chuyển sang máy khác và khôi phục lại dễ dàng:
```
# Lưu ra file
docker save -o myapp.tar myapp_image

# Khôi phục từ file
docker load -i myapp.tar
```
- Dễ Scale (Mở rộng): Khi cần tăng tải, chỉ cần tăng số lượng Container của service đó lên mà không ảnh hưởng các thành phần khác.
- Hoạt động Offline: Sau khi đã pull Image về máy, app chạy không cần internet. Phù hợp với môi trường máy chủ nội bộ, không có kết nối mạng ngoài.
- Tích hợp CI/CD dễ dàng: Docker tích hợp tốt với các công cụ CI/CD như GitHub Actions, Jenkins, GitLab CI — giúp tự động hoá quy trình build, test, deploy.

## 4. Dùng docker: tạo app, test app OK trên laptop cá nhân giờ muốn triển khai app này trên máy chủ thật ko có internet thì các bước cần làm là?
- Bình thường khi chạy docker compose up, Docker sẽ tự động tải Image từ internet (Docker Hub). Nhưng máy chủ không có internet → không tải được → app không chạy được.
### BƯỚC 1 — Trên laptop (có internet): Kiểm tra các Image đang dùng
```
# Xem danh sách tất cả Image đang có
docker images
```
- Ghi lại tên và tag của tất cả Image mà app đang dùng. Ví dụ:
```
nodered/node-red       latest
mariadb                10.6
influxdb               2.0
grafana/grafana        latest
nginx                  latest
```

### BƯỚC 2 — Trên laptop: Lưu tất cả Image ra file nén
```
# Lưu tất cả Image vào 1 file nén duy nhất
docker save nodered/node-red mariadb:10.6 influxdb:2.0 grafana/grafana nginx | gzip > all_images.tar.gz
```
- Hoặc lưu từng Image riêng lẻ:
```
docker save -o nodered.tar nodered/node-red
docker save -o mariadb.tar mariadb:10.6
docker save -o influxdb.tar influxdb:2.0
docker save -o grafana.tar grafana/grafana
docker save -o nginx.tar nginx:latest
```

### BƯỚC 3 — Trên laptop: Chuẩn bị toàn bộ file cần thiết
- Gom tất cả vào 1 thư mục để dễ chuyển:
```
📁 deploy_package/
  ├── all_images.tar.gz     ← file chứa tất cả Images
  ├── docker-compose.yml    ← file cấu hình
  ├── .env                  ← file biến môi trường (nếu có)
  └── configs/              ← thư mục cấu hình (nginx, grafana,...)
```

### BƯỚC 4 — Chuyển toàn bộ file sang máy chủ
- Dùng USB, SCP, hoặc phương tiện truyền file khác:
```
# Nếu có thể dùng SCP qua mạng nội bộ
scp -r deploy_package/ user@192.168.1.100:/home/user/

# Hoặc copy vào USB rồi cắm vào máy chủ
```

### BƯỚC 5 — Trên máy chủ: Load Images từ file vào Docker
```
# Di chuyển vào thư mục chứa file
cd /home/user/deploy_package

# Load tất cả Images từ file nén
docker load -i all_images.tar.gz

# Hoặc load từng file riêng
docker load -i nodered.tar
docker load -i mariadb.tar
docker load -i influxdb.tar
docker load -i grafana.tar
docker load -i nginx.tar
```

### BƯỚC 6 — Trên máy chủ: Kiểm tra Images đã load thành công chưa
```
docker images
```
- Phải thấy đủ tất cả các Image đã load:
```
REPOSITORY           TAG       IMAGE ID       SIZE
nodered/node-red     latest    abc123...      500MB
mariadb              10.6      def456...      400MB
influxdb             2.0       ghi789...      300MB
grafana/grafana      latest    jkl012...      250MB
nginx                latest    mno345...      150MB
```

### BƯỚC 7 — Trên máy chủ: Chạy app
```
# Chạy toàn bộ app
docker compose up -d
```

### Kiểm tra app đang chạy
```
# Xem trạng thái các container
docker compose ps

# Xem log nếu có lỗi
docker compose logs

# Xem log của từng service cụ thể
docker compose logs nodered
docker compose logs grafana
```

















