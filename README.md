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

# B. Thực hành áp dụng
## 1. Tạo cấu trúc thư mục dự án (Ubuntu)
- Mở Terminal Ubuntu rồi chạy lần lượt từng lệnh:
```
mkdir -p ~/bt5-monitor-app/{nodered,flask-api,nginx/html}
cd ~/bt5-monitor-app
ls -R
```
<img width="974" height="512" alt="image" src="https://github.com/user-attachments/assets/bde07cbb-05f2-4519-a78f-17fdf9c109a7" />

## 2. Tạo file `docker-compose.yml`
- Chạy lệnh sau để tạo và mở file: `nano docker-compose.yml`
```
version: "3.8"

services:

  nodered:
    image: nodered/node-red:latest
    container_name: nodered
    restart: always
    ports:
      - "1880:1880"
    volumes:
      - nodered_data:/data
    networks:
      - monitor_net

  mariadb:
    image: mariadb:10.11
    container_name: mariadb
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: monitor_db
      MYSQL_USER: monitor
      MYSQL_PASSWORD: monitor123
    volumes:
      - mariadb_data:/var/lib/mysql
    networks:
      - monitor_net

  influxdb:
    image: influxdb:2.7
    container_name: influxdb
    restart: always
    ports:
      - "8086:8086"
    environment:
      DOCKER_INFLUXDB_INIT_MODE: setup
      DOCKER_INFLUXDB_INIT_USERNAME: admin
      DOCKER_INFLUXDB_INIT_PASSWORD: admin123456
      DOCKER_INFLUXDB_INIT_ORG: myorg
      DOCKER_INFLUXDB_INIT_BUCKET: monitor_bucket
      DOCKER_INFLUXDB_INIT_ADMIN_TOKEN: mytoken123456
    volumes:
      - influxdb_data:/var/lib/influxdb2
    networks:
      - monitor_net

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: always
    ports:
      - "3000:3000"
    environment:
      GF_SECURITY_ALLOW_EMBEDDING: "true"
      GF_AUTH_ANONYMOUS_ENABLED: "true"
      GF_AUTH_ANONYMOUS_ORG_ROLE: Viewer
    volumes:
      - grafana_data:/var/lib/grafana
    networks:
      - monitor_net

  flask-api:
    build: ./flask-api
    container_name: flask-api
    restart: always
    ports:
      - "5000:5000"
    networks:
      - monitor_net

  nginx:
    image: nginx:latest
    container_name: nginx
    restart: always
    ports:
      - "80:80"
    volumes:
      - ./nginx/html:/usr/share/nginx/html
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
    networks:
      - monitor_net

networks:
  monitor_net:
    driver: bridge

volumes:
  nodered_data:
  mariadb_data:
  influxdb_data:
  grafana_data:
```
<img width="1143" height="657" alt="image" src="https://github.com/user-attachments/assets/e787cdab-7d92-4a78-9330-80c5c1b92506" />

- Kiểm tra file vừa tạo: `cat docker-compose.yml`
<img width="1143" height="656" alt="image" src="https://github.com/user-attachments/assets/034d71c4-d1d3-491f-8dc4-1ba5654bb8ea" />

## 3. Tạo Flask API
- Chạy lần lượt các lệnh:
```
cd ~/bt5-monitor-app/flask-api
nano app.py
```
- Paste nội dung này vào `nano app.py`
```
from flask import Flask, jsonify
import pymysql

app = Flask(__name__)

DB_CONFIG = {
    'host': 'mariadb',
    'user': 'monitor',
    'password': 'monitor123',
    'database': 'monitor_db',
    'charset': 'utf8mb4'
}

@app.route('/api/latest', methods=['GET'])
def get_latest():
    try:
        conn = pymysql.connect(**DB_CONFIG)
        cursor = conn.cursor(pymysql.cursors.DictCursor)
        cursor.execute("SELECT * FROM sensor_data ORDER BY id DESC LIMIT 1")
        row = cursor.fetchone()
        conn.close()
        if row:
            return jsonify({"status": "ok", "data": row})
        else:
            return jsonify({"status": "ok", "data": None})
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500

@app.route('/api/history', methods=['GET'])
def get_history():
    try:
        conn = pymysql.connect(**DB_CONFIG)
        cursor = conn.cursor(pymysql.cursors.DictCursor)
        cursor.execute("SELECT * FROM sensor_data ORDER BY id DESC LIMIT 20")
        rows = cursor.fetchall()
        conn.close()
        return jsonify({"status": "ok", "data": rows})
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```
<img width="892" height="595" alt="image" src="https://github.com/user-attachments/assets/a3499f8d-4824-4e02-8091-fed779dfbadd" />

- Tiếp theo tạo file requirements.txt: `nano requirements.txt`
<img width="901" height="597" alt="image" src="https://github.com/user-attachments/assets/c46064ec-b1a5-4915-9f7a-33ff7a722a82" />

- Tiếp theo tạo Dockerfile: `nano Dockerfile`
```
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```
<img width="901" height="595" alt="image" src="https://github.com/user-attachments/assets/502240bb-437f-4362-a6d9-e71e8333bdaa" />

- Để xuất hiện 3 file: app.py, requirements.txt, Dockerfile
<img width="902" height="602" alt="image" src="https://github.com/user-attachments/assets/9684d2f6-9841-4f8b-8bdc-def2fb43c444" />

## 4. Tạo file cấu hình Nginx
```
cd ~/bt5-monitor-app/nginx
nano nginx.conf
```
- Paste nội dung:
```
server {
    listen 80;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }

    location /api/ {
        proxy_pass http://flask-api:5000/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
<img width="901" height="593" alt="image" src="https://github.com/user-attachments/assets/68f3b4c9-3b35-4c59-8508-3aa7d68fab13" />

- Tạo file `index.html` cho frontend: `nano html/index.html`
```
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Monitor Dashboard</title>
  <style>
    body { font-family: Arial, sans-serif; background: #1a1a2e; color: #eee; margin: 0; padding: 20px; }
    h1 { text-align: center; color: #00d4ff; }
    .card { background: #16213e; border-radius: 12px; padding: 20px; margin: 20px auto; max-width: 500px; text-align: center; }
    .value { font-size: 48px; font-weight: bold; color: #00d4ff; }
    .label { font-size: 14px; color: #aaa; margin-top: 8px; }
    .status-ok { color: #00ff88; }
    .status-alert { color: #ff4444; }
    iframe { width: 100%; height: 400px; border: none; border-radius: 12px; margin-top: 20px; }
  </style>
</head>
<body>
  <h1>📊 Monitor & Alert Dashboard</h1>

  <div class="card">
    <div class="value" id="current-value">--</div>
    <div class="label">Giá Vàng (USD/oz) - Cập nhật tự động</div>
    <div class="label" id="update-time"></div>
    <div class="label" id="alert-status"></div>
  </div>

  <div class="card">
    <h3>📈 Biểu đồ lịch sử (Grafana)</h3>
    <iframe src="http://localhost:3000/d-solo/monitor/gold-price?orgId=1&panelId=1&refresh=5s"
            allowfullscreen></iframe>
  </div>

  <script>
    const ALERT_LOW = 3000;
    const ALERT_HIGH = 3500;

    function fetchLatest() {
      fetch('/api/latest')
        .then(res => res.json())
        .then(json => {
          if (json.status === 'ok' && json.data) {
            const val = json.data.value;
            const time = json.data.timestamp;
            document.getElementById('current-value').textContent = parseFloat(val).toFixed(2);
            document.getElementById('update-time').textContent = 'Lúc: ' + time;

            const statusEl = document.getElementById('alert-status');
            if (val < ALERT_LOW) {
              statusEl.textContent = '⚠️ ALERT LOW: Giá dưới ngưỡng!';
              statusEl.className = 'label status-alert';
            } else if (val > ALERT_HIGH) {
              statusEl.textContent = '⚠️ ALERT HIGH: Giá vượt ngưỡng!';
              statusEl.className = 'label status-alert';
            } else {
              statusEl.textContent = '✅ Bình thường';
              statusEl.className = 'label status-ok';
            }
          }
        })
        .catch(err => console.error('Lỗi fetch:', err));
    }

    fetchLatest();
    setInterval(fetchLatest, 5000);
  </script>
</body>
</html>
```

- Kiểm tra:
```
ls -la ~/bt5-monitor-app/nginx/
ls -la ~/bt5-monitor-app/nginx/html/
```
<img width="904" height="579" alt="image" src="https://github.com/user-attachments/assets/b48732ef-03bd-4307-8e41-73b2be7f37d6" />

## 5. Khởi động toàn bộ Docker Compose
- Trở về thư mục gốc chạy:
```
cd ~/bt5-monitor-app
docker compose up -d --build
```
<img width="1919" height="1025" alt="image" src="https://github.com/user-attachments/assets/f6cc3044-c39b-4306-a3ab-e958f202c2aa" />

- Kiểm tra trạng thái: `docker compose ps`
<img width="1923" height="702" alt="image" src="https://github.com/user-attachments/assets/961c9680-72a4-44fa-aa40-9efa6f40eff5" />

## 6. Tạo bảng trong MariaDB
- Trước khi cấu hình Node-RED, cần tạo bảng lưu dữ liệu trong MariaDB:
```
docker exec -it mariadb mariadb -u monitor -pmonitor123 monitor_db
```

- Chạy lệnh SQL:
```
CREATE TABLE IF NOT EXISTS sensor_data (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    value FLOAT NOT NULL,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
);
```
<img width="911" height="597" alt="image" src="https://github.com/user-attachments/assets/a2929232-bd5e-40c0-80b6-1b84402ad910" />

- Kiểm tra bảng đã tạo: `SHOW TABLES;`
<img width="905" height="528" alt="image" src="https://github.com/user-attachments/assets/6adeb64c-a1ba-42b7-9bc4-9dca939916a3" />

## 7. Cấu hình Node-RED
- Truy cập: `http://localhost:1880`
<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/e186ae8c-b993-4bcd-89e5-39a6ab4de2c0" />

- Cần cài thêm 2 thư viện:
  + Nhấn vào menu ☰ (góc trên phải) → chọn Manage palette
  + Chọn tab Install
  + Tìm và cài lần lượt 2 package sau: `node-red-node-mysql` và `node-red-contrib-influxdb` → nhấn Install
<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/b7bf739c-a3b3-401b-900c-644f642b82b6" />

<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/c4ae251e-04b2-4ae6-9811-2f923392026d" />

## 8. Tạo Flow trong Node-RED
- Nhấn menu ☰ góc trên phải → chọn Import
- Copy nội dung JSON rồi paste vào ô Import:
```
[
  {"id":"inject1","type":"inject","name":"Mỗi 30 giây","repeat":"30","once":true,"x":130,"y":100,"wires":[["http_gold"]]},
  {"id":"http_gold","type":"http request","name":"Lấy giá vàng","method":"GET","ret":"obj","url":"https://data-asg.goldprice.org/dbXRates/USD","x":330,"y":100,"wires":[["parse_gold"]]},
  {"id":"parse_gold","type":"function","name":"Parse giá vàng","func":"var price = msg.payload.items[0].xauPrice;\nmsg.payload = { name: 'gold', value: price };\nmsg.value = price;\nreturn msg;","x":540,"y":100,"wires":[["mysql_node","influx_node","check_alert"]]},
  {"id":"mysql_node","type":"mysql","name":"Lưu MariaDB","mysqlConfig":"mysql_config","sql":"INSERT INTO sensor_data (name, value) VALUES ('{{{payload.name}}}', {{{payload.value}}});","x":540,"y":220,"wires":[[]]},
  {"id":"influx_node","type":"influxdb out","name":"Lưu InfluxDB","influxdb":"influx_config","measurement":"gold_price","x":540,"y":300,"wires":[]},
  {"id":"check_alert","type":"function","name":"Kiểm tra ngưỡng","func":"var val = msg.value;\nvar LOW = 3000;\nvar HIGH = 3500;\nif(val < LOW){\n  msg.alert = '⚠️ ALERT LOW: Giá vàng = ' + val.toFixed(2) + ' USD/oz - Dưới ngưỡng ' + LOW;\n  return msg;\n} else if(val > HIGH){\n  msg.alert = '⚠️ ALERT HIGH: Giá vàng = ' + val.toFixed(2) + ' USD/oz - Vượt ngưỡng ' + HIGH;\n  return msg;\n}\nreturn null;","x":540,"y":400,"wires":[["telegram_node"]]},
  {"id":"telegram_node","type":"http request","name":"Gửi Telegram","method":"GET","ret":"txt","url":"https://api.telegram.org/bot{{{BOT_TOKEN}}}/sendMessage?chat_id={{{CHAT_ID}}}&text={{{alert}}}","x":760,"y":400,"wires":[[]]}
]
```
<img width="1919" height="989" alt="image" src="https://github.com/user-attachments/assets/c1a6c052-a16c-470b-a27e-32e49a6e6e67" />

- Sau khi import xong sẽ thấy các node xuất hiện
<img width="1919" height="987" alt="image" src="https://github.com/user-attachments/assets/f1ff5c4c-6cdc-4eb1-b7be-df7c55bc1dd8" />

## 9. Cấu hình node
- Vào node "Lưu MariaDB
  + Host: `mariadb`
  + Port: `3306`
  + User: `monitor`
  + Password: `monitor123`
  + Database: `monitor_db`
<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/46d1452f-1f92-4ce2-88c2-7a5a10850e3d" />

- Tiếp tục vào node `Lưu InfluxDB`
  + Version: `2.0`
  + URL: `http://influxdb:8086`
  + Token: `mytoken123456`
  + Organisation: `myorg`
  + Bucket: `monitor_bucket`
<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/43af04b6-0b61-45da-8286-901402cfc530" />

<img width="1919" height="986" alt="image" src="https://github.com/user-attachments/assets/f373dc05-b60d-4b44-8677-34408b23a11e" />

- Ấn vào node `Parse giá vàng` và sửa thành:
```
try {
    var data = msg.payload;
    if (typeof data === 'string') {
        data = JSON.parse(data);
    }
    var price = data.chart.result[0].meta.regularMarketPrice;
    msg.payload = { name: 'gold', value: price };
    msg.topic = "INSERT INTO sensor_data (name, value) VALUES ('gold', " + price + ");";
    msg.value = price;
    node.warn("Giá vàng: " + price);
    return msg;
} catch(e) {
    node.error("Parse error: " + e.message, msg);
    return null;
}
```
<img width="1919" height="979" alt="image" src="https://github.com/user-attachments/assets/2e1d1f2e-d71c-4c6d-b021-144df4aa187f" />

## 10. Cấu hình Telegram Bot
- Tìm kiếm @BotFather → nhấn vào → nhắn `/newbot`
- BotFather hỏi tên bot → gõ: `MonitorAlertBot`
- BotFather hỏi username → gõ: `monitor_vietanh_bot`
- BotFather trả về Token:
```
8909281104:AAG6kZyQwboK0nP8Xuj8ertY8RnwEHqm8_o
```
<img width="1919" height="986" alt="image" src="https://github.com/user-attachments/assets/88861ea9-bc86-451e-b01d-6b69ddf0de29" />

- Tạo 1 group mới trên Telegram
- Thêm bot @monitor_vietanh_bot vào group
- Nhấn Next → đặt tên group: `Monitor Alert` -> Nhấn Create
<img width="1919" height="982" alt="image" src="https://github.com/user-attachments/assets/10e842f0-04c5-4f5b-9268-16d3a8075ead" />

 - Nhắn `hello` rồi gửi
<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/a2cd8229-d3a0-4aa0-95f9-b794b2d50a68" />

- Truy cập vào: `https://api.telegram.org/bot8909281104:AAG6kZyQwboK0nP8Xuj8ertY8RnwEHqm8_o/getUpdates`
<img width="1919" height="749" alt="image" src="https://github.com/user-attachments/assets/1a65434c-4111-4f6b-9a4b-2ca4a38c59ec" />

- Quay lại chat BotFather trên Telegram Web
- Nhắn: `/setprivacy`
- BotFather hỏi chọn bot → nhắn: `@monitor_vietanh_bot`
- BotFather hỏi chế độ → chọn/nhắn: `Disable`
<img width="1919" height="984" alt="image" src="https://github.com/user-attachments/assets/0a360c2e-c9a4-4c4b-b009-aee737058823" />

- Quay lại Node-RED (localhost:1880), vào node "Gửi Telegram" và sửa URL thành:
```
https://api.telegram.org/bot8909281104:AAG6kZyQwboK0nP8Xuj8ertY8RnwEHqm8_o/sendMessage?chat_id=-5240697941&text={{{alert}}}
```
<img width="1919" height="986" alt="image" src="https://github.com/user-attachments/assets/e669d2e3-708d-40c7-85fe-859faf861741" />

- Truy cập vào node `Lấy giá vàng` → sửa URL thành: `https://query1.finance.yahoo.com/v8/finance/chart/GC%3DF`
<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/6e530a54-60dd-4ffa-a0c2-1bc5c4730d68" />

- Dữ liệu giá vàng đã chạy thành công! Giá vàng ~4210 USD/oz đang được cập nhật mỗi 30 giây
<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/e5c8b424-da11-4156-a7b9-2ee77d946af8" />

- Tiếp tục vào node "Kiểm tra ngưỡng" → thêm nội dung:
```
var val = msg.value;
var LOW = 3000;
var HIGH = 4000;

// Chỉ gửi alert mỗi 5 phút
var now = Date.now();
var lastAlert = context.get('lastAlert') || 0;
if (now - lastAlert < 300000) return null;

if(val < LOW){
    context.set('lastAlert', now);
    msg.alert = encodeURIComponent('⚠️ ALERT LOW: Giá vàng = ' + val.toFixed(2) + ' USD/oz - Dưới ngưỡng ' + LOW);
    return msg;
} else if(val > HIGH){
    context.set('lastAlert', now);
    msg.alert = encodeURIComponent('⚠️ ALERT HIGH: Giá vàng = ' + val.toFixed(2) + ' USD/oz - Vượt ngưỡng ' + HIGH);
    return msg;
}
return null;
```
<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/de788cf2-e670-4cf4-af25-2f47e01e58c8" />

- Trong Node-RED, kéo 1 node `debug` từ palette trái → nối vào output của node `Kiểm tra ngưỡng`
<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/38e8b54d-dd2e-4b80-bd49-577a93bce237" />

- Sau khi nhắn `hello` vào group Monitor Alert → sau đó truy cập vào URL: `https://api.telegram.org/bot8909281104:AAG6kZyQwboK0nP8Xuj8ertY8RnwEHqm8_o/getUpdates`
<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/0d40d390-e604-4a52-ba51-296911c7e093" />

<img width="1919" height="447" alt="image" src="https://github.com/user-attachments/assets/ba977acf-3bb2-44a7-adff-88f29c3cf058" />

- Bot đã gửi tin nhắn vào group thành công
<img width="1919" height="986" alt="image" src="https://github.com/user-attachments/assets/16a0e1f9-4edb-4004-b44e-a40a5b0ac7ff" />

## 11. Cấu hình Grafana
- Mở trình duyệt vào:
```
http://localhost:3000
```
<img width="1919" height="983" alt="image" src="https://github.com/user-attachments/assets/2d7dcf33-b159-4f1c-a3cf-7f28a12682fa" />

<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/52fb84b7-a5f3-464a-889d-c88dee063943" />

- Thêm Data Source InfluxDB
  + Nhấn Connections (menu trái) → Data sources → Add new data source
  + Tìm và chọn InfluxDB
<img width="1919" height="985" alt="image" src="https://github.com/user-attachments/assets/dc6f9661-3eab-4402-baba-344ec842a3f6" />

<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/5de9a27c-9db2-401d-84c8-4dc5102379bc" />

<img width="1919" height="988" alt="image" src="https://github.com/user-attachments/assets/f4979514-5d4a-4cac-9daa-c1d26bb23997" />

- Điền thông tin:
  + Query Language: `Flux`
  + URL: `http://influxdb:8086`
  + Organization: `myorg`
  + Token: `mytoken123456`
  + Default Bucket: `monitor_bucket`
<img width="1919" height="905" alt="image" src="https://github.com/user-attachments/assets/3ca395d0-b79e-499b-ae46-1a14a21af337" />

<img width="1568" height="733" alt="image" src="https://github.com/user-attachments/assets/8af14f74-b6ed-4cae-97a6-b2caa18a670d" />

-> Nhấn Save & Test → Sẽ hiển thị `datasource is working`

- Tạo Dashboard Grafana
  + Nhấn Dashboards → New → New dashboard
<img width="1923" height="990" alt="image" src="https://github.com/user-attachments/assets/dd7f4eda-2aa9-4aa8-8169-5c18d593b31b" />

<img width="1919" height="907" alt="image" src="https://github.com/user-attachments/assets/587c4ad0-c699-49c8-9cea-30db961875e8" />
  
<img width="1919" height="903" alt="image" src="https://github.com/user-attachments/assets/b4672fdd-35f1-4c54-a60e-a51d06fc9bf8" />

  + Chọn data source `influxdb`
<img width="1919" height="899" alt="image" src="https://github.com/user-attachments/assets/06272409-44f1-46d4-bb3c-faf412f1de72" />

- Nhấn vào ô query bên dưới → dán nội dung query này vào rồi ấn `Refresh` để chạy query và hiển thị biểu đồ
```
from(bucket: "monitor_bucket")
  |> range(start: -1h)
  |> filter(fn: (r) => r._measurement == "gold_price")
  |> filter(fn: (r) => r._field == "value")
```
<img width="1919" height="897" alt="image" src="https://github.com/user-attachments/assets/c8a4d232-a4ec-49c5-9e7d-0f928b50ab9f" />

<img width="1919" height="905" alt="image" src="https://github.com/user-attachments/assets/fbeffb30-9c28-4601-9193-20ecb5c935a1" />

- Đặt tên panel và lưu:
  + Bên phải tìm ô Title → xóa `New panel` → gõ: `Giá Vàng Lịch Sử (USD/oz)`
  + Nhấn Save
  + Hộp thoại hiện ra → đặt tên dashboard: `Monitor Dashboard` → nhấn Save
<img width="1919" height="902" alt="image" src="https://github.com/user-attachments/assets/d89886a5-faf7-4db9-9535-c8db2ce14fa2" />

<img width="1919" height="907" alt="image" src="https://github.com/user-attachments/assets/28eb64d9-4d8a-43cf-94e0-22eaa37fd230" />

- Nhấn Back to dashboard để về dashboard, rồi cần lấy URL embed của panel này để dùng trong iframe.
<img width="1919" height="898" alt="image" src="https://github.com/user-attachments/assets/55d02327-1bd1-48ec-ae43-33cda8739f35" />

- Nhấn vào tiêu đề panel `Giá Vàng Lịch Sử` → chọn `Share` → share embed → copy đoạn iframe URL.
<img width="1919" height="902" alt="image" src="https://github.com/user-attachments/assets/10631ebc-ff93-4c62-be1c-e037e794ea86" />

- iframe URL:
```
<iframe src="http://localhost:3000/d-solo/ad4lstv/monitor-dashboard?orgId=1&from=1781062466896&to=1781066041262&timezone=browser&tab=queries&panelId=panel-1" width="450" height="200" frameborder="0"></iframe>
```
<img width="1919" height="907" alt="image" src="https://github.com/user-attachments/assets/0d1757ea-a4dc-45ef-8896-5777cc26a9e9" />

- Cập nhật file `index.html` để dùng iframe này: `nano ~/bt5-monitor-app/nginx/html/index.html`
```
cat > ~/bt5-monitor-app/nginx/html/index.html << 'EOF'
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Monitor Dashboard</title>
  <style>
    body { font-family: Arial, sans-serif; background: #1a1a2e; color: #eee; margin: 0; padding: 20px; }
    h1 { text-align: center; color: #00d4ff; }
    .card { background: #16213e; border-radius: 12px; padding: 20px; margin: 20px auto; max-width: 800px; text-align: center; }
    .value { font-size: 48px; font-weight: bold; color: #00d4ff; }
    .label { font-size: 14px; color: #aaa; margin-top: 8px; }
    .status-ok { color: #00ff88; }
    .status-alert { color: #ff4444; }
    iframe { width: 100%; height: 400px; border: none; border-radius: 12px; margin-top: 10px; }
  </style>
</head>
<body>
  <h1>📊 Monitor & Alert Dashboard</h1>
  <div class="card">
    <div class="value" id="current-value">--</div>
    <div class="label">Giá Vàng (USD/oz) - Cập nhật tự động</div>
    <div class="label" id="update-time"></div>
    <div class="label" id="alert-status"></div>
  </div>
  <div class="card">
    <h3>📈 Biểu đồ lịch sử (Grafana)</h3>
    <iframe src="http://localhost:3000/d-solo/ad4lstv/monitor-dashboard?orgId=1&panelId=panel-1&refresh=30s&theme=dark" allowfullscreen></iframe>
  </div>
  <script>
    const ALERT_LOW = 3000;
    const ALERT_HIGH = 4000;
    function fetchLatest() {
      fetch('/api/latest')
        .then(res => res.json())
        .then(json => {
          if (json.status === 'ok' && json.data) {
            const val = json.data.value;
            const time = json.data.timestamp;
            document.getElementById('current-value').textContent = parseFloat(val).toFixed(2);
            document.getElementById('update-time').textContent = 'Lúc: ' + time;
            const statusEl = document.getElementById('alert-status');
            if (val < ALERT_LOW) {
              statusEl.textContent = '⚠️ ALERT LOW: Giá dưới ngưỡng!';
              statusEl.className = 'label status-alert';
            } else if (val > ALERT_HIGH) {
              statusEl.textContent = '⚠️ ALERT HIGH: Giá vượt ngưỡng!';
              statusEl.className = 'label status-alert';
            } else {
              statusEl.textContent = '✅ Bình thường';
              statusEl.className = 'label status-ok';
            }
          }
        })
        .catch(err => console.error('Lỗi fetch:', err));
    }
    fetchLatest();
    setInterval(fetchLatest, 5000);
  </script>
</body>
</html>
EOF
```
<img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/29b71ecc-66e6-493b-968d-17d633c02523" />

<img width="1922" height="763" alt="image" src="https://github.com/user-attachments/assets/de8625ff-35a0-41ed-8dd4-b8d60666260b" />

- Truy cập vào trình duyệt:
```
http://localhost
```
<img width="1919" height="981" alt="image" src="https://github.com/user-attachments/assets/7667bccf-69e1-49aa-a1f0-2ecca8d04a00" />

- Export container ra file nén, xóa rồi load lại.
- Chạy lệnh trên Ubuntu để export tất cả images:
```
cd ~/bt5-monitor-app
docker save \
  nodered/node-red:latest \
  grafana/grafana:latest \
  influxdb:2.7 \
  mariadb:10.11 \
  nginx:latest \
  bt5-monitor-app-flask-api:latest \
  -o bt5_monitor_images.tar
```

- Chờ vài phút rồi kiểm tra:
```
ls -lh bt5_monitor_images.tar
```
<img width="1923" height="447" alt="image" src="https://github.com/user-attachments/assets/70021467-37b8-4218-a1f8-25ef33c1f8ed" />









































