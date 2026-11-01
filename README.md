# APK Reverse Toolkit — GitHub Actions

Tự động phân tích/giải mã APK bằng **JADX + APKTool**. Không cần máy tính cá nhân; có thể quản lý repo và tải kết quả bằng điện thoại/Termux.

## Cấu trúc

```text
.
├── .github/workflows/reverse-apk.yml
├── apks/                 # Đặt một hoặc nhiều file .apk tại đây
└── output/               # Kết quả tải từ GitHub Actions (không commit)
```

## Cách dùng

1. Tạo một repository GitHub mới, ví dụ `apk-reverse-toolkit`.
2. Upload toàn bộ nội dung dự án này lên repository.
3. Đưa APK cần xử lý vào thư mục `apks/` (ví dụ `apks/my-app.apk`).
4. Commit và push. Workflow tự chạy khi có thay đổi trong `apks/`.
5. Hoặc vào **Actions → Reverse APK (JADX + APKTool) → Run workflow** để chạy thủ công.
6. Mở lần chạy mới nhất → **Artifacts** → tải `apk-reverse-results` (ZIP).

Workflow tạo cho mỗi APK:
- `jadx/` — mã Java được dịch ngược từ DEX (có thể không khôi phục chính xác source gốc).
- `apktool/` — Smali, manifest đã giải mã và resources.
- `logs/` — nhật ký lệnh.
- `summary.txt` — thông tin APK và trạng thái từng công cụ.

## Termux: push dự án lên GitHub

Cài Git (nếu chưa có):

```bash
pkg update -y
pkg install git openssh -y
```

Giải nén ZIP dự án, sau đó:

```bash
cd apk-reverse-github-actions
git init
git branch -M main
git add .
git commit -m "Add APK reverse GitHub Actions"
git remote add origin https://github.com/USERNAME/REPOSITORY.git
git push -u origin main
```

Thay `USERNAME/REPOSITORY` bằng repo của bạn. Nếu GitHub yêu cầu xác thực, dùng đăng nhập GitHub/credential manager hoặc Personal Access Token làm mật khẩu HTTPS; không đưa token vào file hay commit.

### Thêm APK bằng Termux

Ví dụ APK nằm trong Download:

```bash
termux-setup-storage
cp ~/storage/shared/Download/app.apk apks/
git add apks/app.apk
git commit -m "Add APK for reverse engineering"
git push
```

Sau khi Actions chạy xong, tải artifact trong trang Actions. Trên điện thoại có thể mở GitHub bằng trình duyệt, vào lần chạy workflow và chọn artifact.

## Lưu ý

- Chỉ phân tích APK mà bạn sở hữu hoặc được phép kiểm tra.
- APK có thể lớn; GitHub giới hạn kích thước file thông thường. Không commit APK lớn hơn 100 MB; cân nhắc Git LFS hoặc cách lưu trữ phù hợp.
- Dịch ngược không thể đảm bảo khôi phục source code gốc, tên biến, comment hay cấu trúc project ban đầu.
- JADX có thể báo lỗi với một số DEX/obfuscation; APKTool cũng có thể không giải mã được một số tài nguyên. Xem `logs/` và `summary.txt`.
- Workflow không cài APK, không ký APK và không tự tạo APK đã build lại.
