---
title: "Hệ thống Cơ sở dữ liệu Giấy phép"
date: 2019-03-27
tags: ["python", "web-scraping", "django", "database"]
categories: ["projects"]
description: "Hệ thống tự động thu thập dữ liệu giấy phép ô tô và nhà thầu hàng ngày để theo dõi giấy phép mới và thay đổi trạng thái."
---

## Tổng quan

Hệ thống Cơ sở dữ liệu Giấy phép là một nền tảng hoàn toàn tự động gồm hai module, được thiết kế để thu thập, lưu trữ và giám sát dữ liệu giấy phép chuyên môn — cụ thể là cho ngành ô tô và nhà thầu. Hệ thống lấy dữ liệu hàng ngày từ các cơ sở dữ liệu chính phủ công khai, xác định giấy phép mới được cấp, và phát hiện thay đổi trạng thái của các giấy phép hiện có. Điều này giúp doanh nghiệp, nhóm tuân thủ và nhà nghiên cứu luôn có cái nhìn cập nhật về tình hình cấp phép mà không cần thao tác thủ công.

Cho dù bạn cần xác minh chứng chỉ của một nhà thầu, theo dõi hoạt động của đối thủ, hay luôn cập nhật về tuân thủ quy định, hệ thống này cung cấp một nguồn thông tin đáng tin cậy và có thể tìm kiếm được.

## Cách hoạt động

Hệ thống được chia thành hai module tích hợp chặt chẽ, hoạt động cùng nhau liền mạch:

1. **Module Scraper** — Một crawler tự động dựa trên Python, chạy theo lịch hàng ngày. Nó truy cập các cổng cấp phép của chính phủ liên quan, trích xuất các bản ghi giấy phép mới nhất, so sánh với cơ sở dữ liệu hiện có, và đánh dấu mọi mục mới hoặc thay đổi trạng thái (ví dụ: đang hoạt động → bị treo, hoặc giấy phép mới được cấp). Toàn bộ dữ liệu được lưu trong cơ sở dữ liệu quan hệ có cấu trúc để truy vấn nhanh.

2. **Module Dashboard** — Giao diện web dựa trên Django, hiển thị dữ liệu thu thập được dưới dạng dễ tìm kiếm, rõ ràng. Người dùng có thể lọc theo loại giấy phép, trạng thái, khu vực, hoặc khoảng thời gian. Dashboard cũng nổi bật các thay đổi gần đây và có thể gửi cảnh báo khi một giấy phép được theo dõi có thay đổi.

## Tính năng chính

- **Thu thập dữ liệu tự động hàng ngày** từ các nguồn cấp phép công khai của chính phủ
- **Phát hiện thay đổi** — tự động xác định giấy phép mới và cập nhật trạng thái
- **Dashboard web có thể tìm kiếm** với bộ lọc theo loại giấy phép, trạng thái, ngày
- **Theo dõi lịch sử giấy phép** — xem toàn bộ lịch sử của bất kỳ giấy phép cụ thể nào
- **Hệ thống cảnh báo** cho các giấy phép được theo dõi khi thay đổi trạng thái
- **Kiến trúc có thể mở rộng** — dễ dàng mở rộng để bao gồm thêm loại giấy phép hoặc tiểu bang khác

## Công nghệ sử dụng

- **Python** cho web scraping và pipeline dữ liệu
- **Django** cho dashboard backend và REST API
- **PostgreSQL** để lưu trữ dữ liệu có cấu trúc và truy vấn nhanh
- **Celery + Cron** để lập lịch và tự động hóa tác vụ scrape hàng ngày

## Trường hợp sử dụng

- Nhóm tuân thủ xác minh trạng thái giấy phép nhà thầu hoặc ô tô trước khi trao hợp đồng
- Doanh nghiệp giám sát hoạt động cấp phép của đối thủ trong khu vực của họ
- Nhà nghiên cứu và phân tích viên theo dõi xu hướng cấp phép trên các ngành
- Nhóm pháp lý và nhân sự thực hiện xác minh lý lịch

## Demo

Có sẵn demo trực tiếp để xem trước. Bạn có thể đăng nhập và khám phá dashboard với dữ liệu thực.

Demo trực tiếp: [licensedb.etuannv.com](http://licensedb.etuannv.com)  
Tên đăng nhập: `viewer` | Mật khẩu: `etuannv2019demo`

---

👉 Cần một hệ thống giám sát giấy phép tùy chỉnh cho ngành của bạn? [Liên hệ với tôi](/vi/contact) để trao đổi về yêu cầu của bạn.

---

**Xem thêm:**
- [Proxy tốt nhất cho Web Scraping](/vi/posts/best-proxies-for-web-scraping/) — giải pháp proxy dùng trong các dự án scraping
- [Price Tracking System](/vi/projects/price-tracking-system/) — một hệ thống thu thập dữ liệu tự động khác
