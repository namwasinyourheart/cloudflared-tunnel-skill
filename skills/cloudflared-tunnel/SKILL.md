---
name: cloudflared-tunnel
description: >
  Tạo quick tunnel bằng `cloudflared tunnel --url` để expose một local web
  server/port ra internet qua Cloudflare. Use when the user wants to share a
  locally running web app, VSCode, Jupyter, webhook, or demo over a temporary
  public URL without a Cloudflare account. Trigger with "tunnel", "public url",
  "expose port", "trycloudflare", "cloudflared", "share local server".
version: 1.0.0
license: MIT
author: elahw
compatibility:
  os: [macos, linux, windows]
  cloudflared: ">= 2022.0.0"
metadata:
  category: devops
  requires-auth: false
---

# Quick Tunnel với cloudflared

Tạo một URL `*.trycloudflare.com` công khai trỏ tới một local server, không cần
đăng nhập tài khoản Cloudflare, không cần DNS, không cấu hình trước.

## Khi nào dùng

- User muốn expose một local web server/port (VSCode, Jupyter, app dev, webhook) ra internet tạm thời.
- User nói "tunnel", "public url", "expose port", "trycloudflare", "cloudflared".
- URL tạm thời, không yêu cầu tên miền riêng hay lâu dài.

> Dùng **`cloudflared tunnel --url`** (quick tunnel) thay vì cấu hình named tunnel
> phức tạp. Khi cần domain ổn định, tên miền riêng, hay qua load balancer thì
> chuyển sang *named tunnel* (cần `cloudflared tunnel login`).

## Yêu cầu

- `cloudflared` đã cài. Kiểm tra: `cloudflared --version`.
- Nếu chưa cài, cài bằng:
  - **macOS (Homebrew):** `brew install cloudflared`
  - **Linux (deb):** `wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && sudo dpkg -i cloudflared-linux-amd64.deb`
  - **Windows (winget):** `winget install Cloudflare.cloudflared`

## Cách chạy

Trước tiên phải có local server đang chạy ở một port. Ví dụ:

```bash
python -m http.server 8080
# hoặc
uvicorn app:app --port 8000
modal_launch.py jupyter --account xxx   # app đang chạy ở 1 port local
```

Sau đó mở tunnel trỏ tới port đó:

```bash
cloudflared tunnel --url http://localhost:8080
```

Output sẽ chứa dòng:

```
Your quick Tunnel has been created! Visit it at (it may take some time to be reachable):
https://<random-string>.trycloudflare.com
```

Dùng URL đó để truy cập / chia sẻ. Rất hay dùng cho VSCode/Jupyter vì các app
này tự có auth (token/password) — tunnel chỉ là lớp vận chuyển public.

## Các option hay gặp

```bash
cloudflared tunnel --url http://localhost:8080            # HTTP
cloudflared tunnel --url http://localhost:8080 --no-autoupdate
cloudflared tunnel --url http://localhost:8080 --logfile tunnel.log   # ghi log ra file
cloudflared tunnel --url http://localhost:8080 --loglevel debug       # log chi tiết
```

## Lưu ý / xử lý

- Tunnel chạy ở **foreground** và dừng khi Ctrl+C. Để chạy nền có thể thêm `&`
  hoặc dùng `--logfile`, nhưng với hầu hết tác vụ nên để foreground trong terminal riêng.
- URL `trycloudflare.com` là **tạm thời** — mất khi process dừng. Không dùng cho production.
- Không cần `cloudflared tunnel login` cho quick tunnel kiểu này; login chỉ cần cho named tunnel.
- Nếu local server chưa chạy hoặc sai port, tunnel vẫn tạo URL nhưng trả về lỗi 502 khi truy cập — hãy xác nhận server local thực sự đang listen đúng port trước.
- Khi kết hợp với Jupyter/VSCode trên Modal: sau khi có URL của app (`*.modal.run`),
  nếu muốn public ổn định hơn hãy dùng chính URL Modal (đã có auth), còn `cloudflared`
  chỉ nên dùng cho server local thiếu auth sẵn (vd `python -m http.server`).
- Bảo mật: URL public = ai cũng truy cập được. Chỉ expose server có auth riêng,
  hoặc giới hạn truy cập khi có thể (Cloudflare Access) — không expose thứ nhạy cảm vô điều kiện.
