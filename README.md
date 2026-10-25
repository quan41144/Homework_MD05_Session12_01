##  nginx: [emerg] unexpected "proxy_set_header" in /etc/nginx/sites-enabled/app.conf:7
- **Nguyên nhân:**
  Trong file cấu hình Nginx, mỗi chỉ thị (directive) bắt buộc phải kết thúc bằng dấu chấm phẩy (`;`).
  Tại dòng số 6:
  ```nginx
  proxy_pass http://localhost:8080
  ```
  Directive `proxy_pass` bị thiếu dấu `;` ở cuối dòng. Do đó, trình phân tích cú pháp của Nginx tiếp tục đọc sang dòng tiếp theo (dòng 7) và hiểu `proxy_set_header` là tham số tiếp theo của `proxy_pass`, dẫn đến lỗi cú pháp: `unexpected "proxy_set_header" in /etc/nginx/sites-enabled/app.conf:7`.

## sua

Thêm dấu `;` vào cuối chỉ thị `proxy_pass http://localhost:8080;`:

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

## Lệnh kiểm tra cấu hình và khởi động lại dịch vụ

### Bước 1: Kiểm tra cú pháp cấu hình
Luôn kiểm tra cấu hình trước khi reload hoặc restart để tránh làm gián đoạn dịch vụ:
```bash
sudo nginx -t
```

### Bước 2: Tải lại hoặc khởi động lại dịch vụ Nginx
- **Tải lại cấu hình (Khuyên dùng - Zero Downtime):**
  ```bash
  sudo systemctl reload nginx
  ```

- **Khởi động lại toàn bộ dịch vụ (nếu Nginx đang bị dừng hoàn toàn):**
  ```bash
  sudo systemctl restart nginx
  ```
