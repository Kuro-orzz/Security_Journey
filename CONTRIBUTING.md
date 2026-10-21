# Contributing

## Bạn có thể đóng góp gì?

- Sửa lỗi chính tả hoặc nội dung sai
- Bổ sung kiến thức vào các mục đã có (Techniques, Case Study,...) hoặc thêm các mục khác thuộc chủ đề security
- Chia sẻ case study của riêng bạn
- Cải thiện giải thích, thêm ví dụ hoặc diagram

## Cấu trúc thư mục

```
src/
├── README.md                # Trang Introduction
├── SUMMARY.md               # Sidebar navigation
├── about.md                 # Trang About
├── Case_study/              # Các bài case study thực tế
│   ├── README.md
│   └── *.md
├── Techniques/              # Ghi chép kỹ thuật theo chủ đề
│   ├── Binary_exploitation/
│   ├── Web_application/
│   ├── Cryptography/
│   └── ...../
└── Random_notes/            # Ghi chép linh tinh
```

## Cách đóng góp

1. Fork repo [Security_Journey](https://github.com/Kuro-orzz/Security_Journey)
2. Tạo branch mới: `git checkout -b ten-branch`
3. Thêm hoặc sửa file `.md` trong thư mục `src/`
4. Thêm entry vào `src/SUMMARY.md` nếu tạo file mới
5. Build local để kiểm tra (xem [LOCAL_BUILD.md](LOCAL_BUILD.md))
6. Commit và tạo Pull Request

## Quy tắc viết bài

### Đặt tên file và thư mục

- Dùng `Snake_case` cho tên thư mục: `Binary_exploitation/`, `Web_application/`
- Dùng `Snake_case` hoặc `kebab-case` cho tên file: `first-rce.md`, `heap_concepts.md`
- Mỗi thư mục chủ đề nên có một `README.md` làm trang index

### Cấu trúc bài viết

- Bắt đầu bằng heading `# Tên bài`
- Nên có phần giới thiệu ngắn trước khi đi vào chi tiết
- Dùng heading `##`, `###` cho các mục con
- Hình ảnh đặt trong thư mục `assets/` cùng cấp hoặc trong thư mục bài viết

### Admonish callout

Blog hỗ trợ callout boxes qua plugin `mdbook-admonish`:

````markdown
```admonish warning title="Cảnh báo"
Nội dung cảnh báo.
```

```admonish tip title="Mẹo"
Nội dung mẹo.
```

```admonish note title="Ghi chú"
Nội dung ghi chú.
```
````

Các kiểu có sẵn: `note`, `tip`, `warning`, `danger`, `info`, `question`, `abstract`, `example`, `quote`, `bug`, `success`, `failure`.

### Case Study

Khi viết case study, lưu ý:

- **Không** tiết lộ tên, domain, hoặc thông tin nhận dạng mục tiêu
- Ghi rõ timeline (ngày phát hiện, ngày report, ngày patch)
- Mô tả impact và severity
- Tập trung vào quá trình tư duy và kỹ thuật, không chỉ kết quả

## Lưu ý

- **Không** commit thư mục `book/` (đã gitignore)
- **Không** chạy `mdbook init --theme` — sẽ ghi đè custom template
- Nội dung chỉ phục vụ mục đích học tập và nghiên cứu
- Mọi hoạt động pentest trong case study phải được thực hiện có sự đồng ý hoặc đã báo cáo đầy đủ

## License

Kiến thức nên được chia sẻ tự do. Mọi đóng góp vào Security Journey đều tuân theo tinh thần này.
