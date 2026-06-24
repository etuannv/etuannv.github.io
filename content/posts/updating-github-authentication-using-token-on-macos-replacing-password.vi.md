---
title: "Cập nhật xác thực GitHub bằng Token trên macOS (Thay thế mật khẩu)"
date: 2024-08-20
tags: ["github", "macos", "git", "authentication"]
categories: ["posts"]
description: "Cách thay thế xác thực bằng mật khẩu GitHub bằng personal access token trên macOS và lưu vào Keychain."
---

Với những thay đổi gần đây của GitHub, mật khẩu không còn được phép dùng để xác thực khi thực hiện các hành động như check-in hoặc check-out. Đây là cách bạn có thể cập nhật phương thức xác thực để dùng personal access token thay cho mật khẩu trên macOS.

Mục lục

- Bước 1: Tạo Personal Access Token (PAT)
- Bước 2: Cập nhật thông tin đăng nhập Git trên macOS
- Bước 3: Lưu Token vào macOS Keychain

### Bước 1: Tạo Personal Access Token (PAT)

1. Vào tài khoản GitHub của bạn và truy cập Settings.
2. Trong Developer settings, chọn Personal access tokens.
3. Nhấp vào Generate new token.
4. Chọn các phạm vi (scope) hoặc quyền bạn cần cho token, chẳng hạn `repo` để truy cập repository.
5. Nhấp Generate token và copy token vào nơi an toàn (bạn sẽ không thể xem lại nó).

### Bước 2: Cập nhật thông tin đăng nhập Git trên macOS

Mở Terminal và xóa thông tin đăng nhập GitHub cũ để macOS hỏi lại thông tin mới vào lần tương tác tiếp theo với GitHub:

```bash
git credential-osxkeychain erase
host=github.com
protocol=https
# Nhấn Enter hai lần
```

Hoặc mở ứng dụng Keychain Access và tìm `github.com`, rồi xóa các mục thông tin đăng nhập GitHub hiện có.

Bây giờ, khi bạn chạy một lệnh Git cần xác thực (ví dụ `git push`), hãy dùng tên đăng nhập GitHub của bạn và dán personal access token khi được hỏi mật khẩu:

```bash
git push origin main
# Username: <tên đăng nhập GitHub của bạn>
# Password: <dán personal access token vào đây>
```

macOS có thể đề nghị lưu token vào Keychain sau khi bạn nhập; chấp nhận để không phải nhập lại lần sau.

### Bước 3: Lưu Token vào macOS Keychain

Nếu bạn muốn tránh phải nhập token nhiều lần, cho phép macOS lưu nó vào Keychain khi được hỏi. Điều này giữ token an toàn và cho phép các hoạt động Git tự động sử dụng nó.

Vậy là xong — xác thực GitHub của bạn đã được cập nhật để dùng personal access token trên macOS.

---

**Xem thêm:**
- [Cập nhật xác thực GitHub bằng Token trên macOS](/vi/posts/github-token-authentication-macos/) — hướng dẫn gốc năm 2021
