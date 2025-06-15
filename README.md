# n8n-docker với Ngrok

Dự án này cung cấp một cách dễ dàng để triển khai n8n bằng Docker và Docker Compose, đồng thời sử dụng Ngrok để tạo tunnel public cho webhook của n8n.

## Yêu cầu

Trước khi bắt đầu, hãy đảm bảo bạn đã cài đặt các phần mềm sau:

*   [Docker](https://docs.docker.com/get-docker/)
*   [Docker Compose](https://docs.docker.com/compose/install/)
*   Một tài khoản [Ngrok](https://ngrok.com/) và `NGROK_AUTHTOKEN` của bạn.

## Cài đặt

1.  **Clone repository:**

    ```bash
    git clone https://github.com/harryitc/n8n-docker.git
    cd n8n-docker
    ```

2.  **Cấu hình Environment Variables:**

    Sao chép tệp `.env.example` thành `.env`:

    ```bash
    cp .env.example .env
    ```

    Mở tệp `.env` và chỉnh sửa các giá trị sau:

    *   `POSTGRES_PASSWORD`: Mật khẩu cho PostgreSQL (ví dụ: `your_strong_password`).
    *   `POSTGRES_NON_ROOT_PASSWORD`: Mật khẩu cho người dùng không phải root của PostgreSQL (có thể giống `POSTGRES_PASSWORD`).
    *   `NGROK_AUTHTOKEN`: Auth token của bạn từ Ngrok Dashboard.
    *   `WEBHOOK_DOMAIN`: Domain tùy chỉnh của tunnel Ngrok (ví dụ: `your-unique-subdomain.ngrok-free.app` nếu dùng gói miễn phí của Ngrok).
    *   `WEBHOOK_URL`: URL đầy đủ của webhook (ví dụ: `https://your-unique-subdomain.ngrok-free.app`). **Quan trọng:** Giá trị này phải là `https://` cộng với `WEBHOOK_DOMAIN` bạn đã đặt ở trên.

    Ví dụ `.env`:
    ```shellscript
    # ========= Other Configuration =========
    N8N_TIMEZONE=Asia/Ho_Chi_Minh

    # ========= PostgreSQL Configuration =========
    POSTGRES_HOST=postgres
    POSTGRES_PORT=5432
    POSTGRES_DB=n8n_docker
    POSTGRES_USER=postgres
    POSTGRES_PASSWORD=your_strong_password # THAY ĐỔI GIÁ TRỊ NÀY

    POSTGRES_NON_ROOT_USER=postgres
    POSTGRES_NON_ROOT_PASSWORD=your_strong_password # THAY ĐỔI GIÁ TRỊ NÀY

    # ========= Ngrok Tunnel Configuration =========
    NGROK_AUTHTOKEN=your_actual_ngrok_authtoken # THAY ĐỔI GIÁ TRỊ NÀY
    WEBHOOK_DOMAIN=your-unique-subdomain.ngrok-free.app # THAY ĐỔI GIÁ TRỊ NÀY
    WEBHOOK_URL=https://your-unique-subdomain.ngrok-free.app # THAY ĐỔI GIÁ TRỊ NÀY (phải khớp với WEBHOOK_DOMAIN)
    ```

3.  **Cấu hình Ngrok:**

    Sao chép tệp `ngrok.template.yml` thành `ngrok.yml`:

    ```bash
    cp ngrok.template.yml ngrok.yml
    ```

    Mở tệp `ngrok.yml` và thay thế `your_webhook_domain` bằng giá trị `WEBHOOK_DOMAIN` bạn đã đặt trong tệp `.env`.

    Ví dụ `ngrok.yml`:
    ```yaml
    version: 2
    log_level: debug
    tunnels:
        n8n:
            proto: http
            addr: n8n:5678
            domain: your-unique-subdomain.ngrok-free.app # PHẢI KHỚP VỚI WEBHOOK_DOMAIN TRONG .env
    ```

## Sử dụng

1.  **Khởi chạy các services:**

    ```bash
    docker-compose up -d
    ```

2.  **Truy cập n8n:**
    Mở trình duyệt và truy cập `http://localhost:5678`.

3.  **Webhook URL:**
    Webhook URL của bạn sẽ là giá trị bạn đã cấu hình cho `WEBHOOK_URL` trong tệp `.env` (ví dụ: `https://your-unique-subdomain.ngrok-free.app`). n8n sẽ sử dụng URL này cho các webhook của nó.

4.  **Kiểm tra Ngrok (Tùy chọn):**
    Bạn có thể kiểm tra trạng thái tunnel của Ngrok bằng cách truy cập giao diện web của Ngrok, thường là `http://localhost:4040` (nếu Ngrok client đang chạy trên máy host của bạn và không bị ghi đè bởi cấu hình trong container). Hoặc kiểm tra log của container ngrok: `docker-compose logs ngrok`.

## Dừng ứng dụng

Để dừng tất cả các services:

```bash
docker-compose down
```

## Xóa dữ liệu (Cẩn thận!)

Nếu bạn muốn xóa cả các volumes dữ liệu (bao gồm dữ liệu PostgreSQL và n8n):

```bash
docker-compose down -v
```

## Cấu hình

*   `.env`: Chứa các biến môi trường cho cấu hình PostgreSQL, n8n và Ngrok.
*   `docker-compose.yml`: Định nghĩa các services, networks, và volumes.
*   `ngrok.yml`: Cấu hình chi tiết cho tunnel Ngrok.
*   `init-data.sh`: Script khởi tạo người dùng và quyền cho PostgreSQL (chạy tự động khi PostgreSQL khởi động lần đầu).

Chúc bạn thành công!

