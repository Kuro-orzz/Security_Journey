# Local Build Guide

Hướng dẫn build mdBook trên máy local để kiểm tra trước khi push.

## 1. Cài đặt

Tải 2 file binary:

- [mdBook](https://github.com/rust-lang/mdBook/releases) — tải file `.zip` phiên bản `x86_64-pc-windows-msvc`
- [mdbook-admonish](https://github.com/tommilligan/mdbook-admonish/releases) — tải file `.zip` phiên bản `x86_64-pc-windows-msvc`

Giải nén cả 2 file `.exe` vào cùng 1 thư mục, ví dụ `~/.mdbook/`.

## 2. Thêm vào PATH

**PowerShell** (session hiện tại):

```powershell
$env:PATH += ";$env:USERPROFILE\.mdbook"
```

**Git Bash** (session hiện tại):

```bash
export PATH="$PATH:$HOME/.mdbook"
```

> Muốn giữ vĩnh viễn thì thêm thư mục chứa `.exe` vào System Environment Variables.

## 3. Build & Preview

```bash
cd Security_Journey/
mdbook-admonish install .   # chỉ cần chạy 1 lần (tạo file CSS)
mdbook serve --open          # mở localhost:3000, tự reload khi sửa file
```

`Ctrl+C` để tắt server.

## 4. Chỉ build (không serve)

```bash
mdbook build
```

Output nằm trong thư mục `book/` (đã được `.gitignore`).

## 5. Cấu trúc thư mục

```
Security_Journey/
├── book.toml              # Config chính của mdBook
├── src/
│   ├── README.md          # Trang chủ (Introduction)
│   ├── SUMMARY.md         # Sidebar navigation
│   ├── about.md           # Trang About
│   ├── assets/            # Ảnh, file đính kèm
│   ├── Case_study/        # Các bài case study
│   ├── Techniques/        # Ghi chép kỹ thuật
│   └── Random_notes/
├── theme/
│   └── index.hbs          # Custom template (sidebar, layout)
├── .github/workflows/
│   └── deploy.yml         # GitHub Actions auto-deploy
└── book/                  # Output build (gitignored)
```

## 6. Thêm bài viết mới

1. Tạo file `.md` trong thư mục tương ứng (`src/Case_study/`, `src/Techniques/`, ...)
2. Thêm entry vào `src/SUMMARY.md` để bài viết hiện trên sidebar
3. Build lại để kiểm tra: `mdbook serve --open`

**Ví dụ:** Thêm bài về SQL Injection vào mục Web Security:

```markdown
# Trong src/SUMMARY.md, thêm dòng:
  - [Web Security]()
    - [SQL Injection](Techniques/web/sqli.md)   # <-- thêm dòng này
```

Rồi tạo file `src/Techniques/web/sqli.md` với nội dung bài viết.

## 7. Custom theme

Template sidebar nằm ở `theme/index.hbs`. File này override template mặc định của mdBook — nếu muốn reset về mặc định:

```bash
mdbook init --theme   # tạo lại thư mục theme/ với file gốc
```

## 8. Admonish (callout boxes)

Plugin `mdbook-admonish` hỗ trợ các block callout trong bài viết:

````markdown
```admonish warning title="Cảnh báo"
Nội dung cảnh báo ở đây.
```

```admonish note title="Ghi chú"
Nội dung ghi chú.
```

```admonish tip title="Mẹo"
Nội dung mẹo.
```
````

Các kiểu có sẵn: `note`, `tip`, `warning`, `danger`, `info`, `question`, `abstract`, `example`, `quote`, `bug`, `success`, `failure`.

## 9. Deploy

Blog tự động deploy lên GitHub Pages mỗi khi push lên branch `master` (qua GitHub Actions). Không cần làm gì thêm — chỉ cần push là xong.

Xem kết quả tại: [https://kuro-orzz.github.io/Security_Journey](https://kuro-orzz.github.io/Security_Journey)
