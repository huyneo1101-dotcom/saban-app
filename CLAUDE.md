# SA BÀN — Tin tức & Phân tích Quân sự (app khoá mật khẩu)

App tĩnh 1 file được **mã hoá hoàn toàn**: nội dung thật nằm trong `index.html` (~2MB, 62 dòng nhưng blob là 1 dòng minified) dưới dạng ciphertext AES-256-GCM base64; chỉ màn khoá nhập mật khẩu là plaintext đọc được. Deploy tĩnh qua **GitHub Pages** (repo `huyneo1101-dotcom/saban-app`, có `.nojekyll`). Không React, không backend, không fetch, không service worker.

## ⚠️ Quy tắc SỐNG CÒN với repo này
- **KHÔNG đọc/parse `index.html` để hiểu app** — toàn bộ nội dung đã mã hoá, không đọc được. Chỉ ~vài trăm ký tự đầu (màn khoá + `<head>`) là plaintext.
- **KHÔNG sửa `index.html` bằng tay** — nó là output đã mã hoá. Sửa tay sẽ hỏng ciphertext (`D.ct`) → app không giải mã được.
- Muốn đổi nội dung app: sửa file NGUỒN plaintext (giữ ở máy local, **không commit**) rồi **CHẠY LẠI script mã hoá** để sinh `index.html` mới. Xem skill `lock-static-app` (AES-256-GCM + PBKDF2 200k vòng, giải mã bằng WebCrypto rồi `document.write` ra trang thật).
- Mật khẩu được nhớ ở `localStorage._sbk` để tự mở khoá lần sau; đổi mật khẩu nguồn → mọi client cũ phải nhập lại.

## Cấu trúc
- `index.html`: màn khoá (plaintext) + blob mã hoá `D.ct` / `D.salt` / `D.iv` + logic giải mã WebCrypto (`deriveKey` PBKDF2 → `AES-GCM` → `document.write`).
- `manifest.json`: PWA installable (`theme_color` `#0d1411`). **Không có service worker** → không chạy offline.
- `icon.png`: icon PWA. `.nojekyll`: cho GitHub Pages phục vụ file tĩnh nguyên trạng.

## Deploy
- GitHub Pages phục vụ trực tiếp từ repo (`.nojekyll` để bỏ qua xử lý Jekyll). **Chưa có CI/CD** (không có `.github/workflows/`); cập nhật = commit `index.html` mới đã mã hoá rồi push.

## Skills dùng chung
Repo có `.claude/skills/` (11 skill từ plugin vibe-pwa-kit). Liên quan nhất: **`lock-static-app`** (quy trình mã hoá/cập nhật app này) và **`deploy-static`** (nếu muốn thêm CI/CD GitHub Pages hoặc Netlify).
