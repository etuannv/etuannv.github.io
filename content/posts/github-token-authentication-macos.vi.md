---
title: "Cập nhật xác thực GitHub bằng Token trên macOS"
date: 2021-08-01
tags: ["github", "macos", "git", "authentication"]
categories: ["posts"]
description: "Hướng dẫn từng bước thay thế xác thực bằng mật khẩu GitHub bằng personal access token trên macOS."
---

Với những thay đổi gần đây của GitHub, mật khẩu không còn được phép dùng để xác thực khi thực hiện các hành động như `git push` hoặc `git pull`.

## Bước 1: Tạo Personal Access Token

1. Vào GitHub → **Settings** → **Developer settings** → **Personal access tokens**
2. Nhấp **Generate new token**
3. Chọn quyền: tối thiểu phải tích `repo`
4. Nhấp **Generate token** và **copy ngay lập tức** (bạn sẽ không thấy lại nó nữa)

## Bước 2: Xóa thông tin đăng nhập cũ trên macOS

```bash
git credential-osxkeychain erase
host=github.com
protocol=https
# Nhấn Enter hai lần
```

Hoặc qua ứng dụng Keychain Access: tìm `github.com` và xóa mục đó.

## Bước 3: Sử dụng Token cho lần push tiếp theo

```bash
git push origin main
# Username: tên đăng nhập GitHub của bạn
# Password: dán personal access token vào đây
```

macOS sẽ đề nghị lưu vào Keychain — chọn yes để không phải nhập lại lần sau.

Vậy là xong! Xác thực GitHub của bạn đã được cập nhật để dùng personal access token.

---

**Xem thêm:**
- [Cập nhật xác thực GitHub bằng Token trên macOS (Thay thế mật khẩu)](/vi/posts/updating-github-authentication-using-token-on-macos-replacing-password/) — hướng dẫn cập nhật năm 2024
