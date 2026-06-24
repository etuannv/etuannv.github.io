---
title: "Hệ thống Theo dõi Giá Cạnh tranh"
date: 2020-01-01
tags: ["python", "web-scraping", "django", "automation"]
categories: ["projects"]
description: "Hệ thống theo dõi giá tự động, thu thập giá của đối thủ hàng ngày và hiển thị xu hướng trên dashboard."
---

## Tổng quan

Cạnh tranh trong thương mại điện tử nghĩa là phải biết đối thủ của bạn đang bán giá bao nhiêu — và biết điều đó mỗi ngày. Kiểm tra giá sản phẩm thủ công trên nhiều website rất tốn thời gian, không ổn định, và đơn giản là không thể mở rộng khi bạn theo dõi hàng chục hoặc hàng trăm sản phẩm.

**Hệ thống Theo dõi Giá Cạnh tranh** tự động hóa hoàn toàn quy trình này. Nó scrape trang sản phẩm của đối thủ theo lịch hàng ngày, lưu mọi điểm giá trong cơ sở dữ liệu có cấu trúc, và hiển thị dữ liệu qua dashboard web sạch với biểu đồ xu hướng lịch sử và cảnh báo thay đổi giá. Với hệ thống này, bạn luôn có cái nhìn chính xác và cập nhật về bối cảnh giá cạnh tranh mà không cần động tay.

## Cách hoạt động

Hệ thống sử dụng một web scraper dựa trên Python được cấu hình với danh sách URL sản phẩm mục tiêu và website đối thủ. Mỗi ngày, scraper truy cập từng trang, trích xuất giá hiện tại, và lưu kèm timestamp vào cơ sở dữ liệu. Dashboard Django sau đó hiển thị dữ liệu này dưới dạng biểu đồ xu hướng tương tác, bảng so sánh, và thông báo cảnh báo bất cứ khi nào phát hiện thay đổi giá đáng kể.

## Tính năng chính

- **Scraping tự động hoàn toàn hàng ngày** — chạy theo lịch và thu thập giá từ tất cả các trang mục tiêu đã cấu hình mà không cần can thiệp thủ công
- **Cơ sở dữ liệu lịch sử giá** — mỗi thay đổi giá được ghi lại kèm timestamp, xây dựng một tập dữ liệu lịch sử toàn diện ngày càng giá trị theo thời gian
- **Trực quan hóa xu hướng** — biểu đồ tương tác hiển thị biến động giá theo khoảng thời gian tùy chỉnh, giúp dễ dàng nhận ra mẫu hình và xu hướng theo mùa
- **Cảnh báo thay đổi giá** — hệ thống tự động đánh dấu các đợt giảm hoặc tăng giá đáng kể, để bạn phản ứng nhanh với biến động thị trường
- **Theo dõi nhiều sản phẩm, nhiều đối thủ** — giám sát bao nhiêu sản phẩm và website đối thủ tùy ý từ một dashboard duy nhất
- **Dashboard so sánh rõ ràng** — so sánh giá song song giữa các đối thủ cho bất kỳ sản phẩm hoặc danh mục được chọn
- **Xuất dữ liệu** — dữ liệu lịch sử giá có thể xuất sang CSV để phân tích thêm trong Excel, Google Sheets, hoặc công cụ business intelligence

## Công nghệ sử dụng

- **Python** cho web scraping và trích xuất dữ liệu tự động
- **Django** cho ứng dụng backend, quản lý dữ liệu và dashboard
- **PostgreSQL** để lưu trữ lịch sử giá có cấu trúc
- **Chart.js** để trực quan hóa xu hướng tương tác trên frontend
- **Celery + Cron** để lập lịch tác vụ scrape hàng ngày

## Trường hợp sử dụng

- **Người bán thương mại điện tử** giám sát giá đối thủ để điều chỉnh chiến lược giá của mình một cách linh hoạt
- **Nhóm mua hàng** theo dõi giá nhà cung cấp hoặc giá sỉ theo thời gian để xác định thời điểm mua tốt nhất
- **Nhà nghiên cứu thị trường** thu thập dữ liệu giá trên các ngành để làm báo cáo hoặc phân tích
- **Quản lý sản phẩm** giám sát giá thị trường của sản phẩm biến động ra sao sau khi ra mắt

## Demo

Có sẵn demo trực tiếp để khám phá dashboard, dữ liệu lịch sử giá mẫu, và biểu đồ xu hướng.

Demo trực tiếp: [pricetracking.etuannv.com](http://pricetracking.etuannv.com)  
Tên đăng nhập: `viewer` | Mật khẩu: `etuannv2019demo`

---

👉 Cần một bộ theo dõi giá tùy chỉnh xây dựng riêng cho thị trường hoặc ngành của bạn? [Liên hệ với tôi](/vi/contact) để trao đổi về dự án của bạn.

---

**Xem thêm:**
- [Hệ thống Theo dõi Giá Điện](/vi/projects/power-price-tracking/) — hệ thống tương tự cho giá thị trường năng lượng
- [Proxy tốt nhất cho Web Scraping](/vi/posts/best-proxies-for-web-scraping/) — giải pháp proxy dùng trong các dự án scraping
