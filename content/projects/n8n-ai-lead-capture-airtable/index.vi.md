---
title: "AI Thu Thập Lead: Từ Form Đến CRM Trong 30 Giây"
date: 2026-07-08
lastmod: 2026-07-09
tags: ["n8n", "ai-automation", "automation", "airtable", "claude", "lead-management"]
categories: ["projects"]
description: "Workflow n8n + Claude AI tự động phân loại, chấm điểm, lưu và cảnh báo mọi lead từ form liên hệ — trong chưa đầy 30 giây."
---

![n8n workflow: Form Submit → Claude AI → Parse JSON → Airtable → Telegram Alert](ai-lead-capture-thumb.png)

Mục lục

- Vấn đề
- Giải pháp: workflow n8n 5 node
- Cách hoạt động
- Khác gì so với một form thông thường
- Tự trải nghiệm
- Công nghệ & chi phí
- Phù hợp với ai
- Các biến thể mình có thể xây
- Muốn áp dụng cho doanh nghiệp của bạn?

Mọi doanh nghiệp có form liên hệ đều gặp chung một vấn đề: lead đổ về, nằm im trong hộp mail, và đến lúc có người đọc rồi đánh giá thì những lead "nóng" nhất đã đi tìm chỗ khác.

Mình đã xây một workflow giải quyết đúng chuyện này. Mỗi lần có người điền form, Claude AI tự động phân tích, chấm điểm chất lượng, lưu vào Airtable và đẩy thông báo qua Telegram — trong chưa đầy 30 giây, hoàn toàn không cần thao tác tay. Muốn xem thử trực tiếp? Nhảy tới mục [Tự trải nghiệm](#tự-trải-nghiệm).

## Vấn đề

Phần lớn form liên hệ chỉ gửi một email thông báo. Email đó nằm lẫn trong hộp thư với đủ thứ khác, và vẫn phải có người đọc từng submission, quyết định có đáng theo đuổi không, copy dữ liệu vào CRM bằng tay, rồi tự nghĩ bước tiếp theo là gì.

Với một freelancer làm một mình hoặc một team nhỏ xử lý 10–50 yêu cầu mỗi tuần, đó là 30–60 phút làm việc lặp đi lặp lại mỗi tuần. Tệ hơn, lead giá trị cao lại bị xử lý chậm y hệt như spam.

## Giải pháp: workflow n8n 5 node

```
n8n Form (URL công khai)
       ↓
Claude AI — phân loại lead + gợi ý hành động tiếp theo
       ↓
Parse & cấu trúc JSON output
       ↓
Airtable — lưu toàn bộ lead kèm điểm AI
       ↓
Telegram — cảnh báo tức thì kèm emoji chất lượng lead
```

## Cách hoạt động

**Bước 1 — Gửi form.** Một form liên hệ gọn gàng thu thập tên, email, công ty, mô tả dự án và ngân sách. Không cần đăng nhập, chạy được trên mọi thiết bị.

**Bước 2 — Claude AI phân tích.** Mỗi submission được gửi tới Claude Haiku (nhanh và rẻ — dưới $0.001 mỗi lần phân tích). Claude trả về JSON có cấu trúc:

```json
{
  "lead_quality": "hot",
  "project_type": "workflow_automation",
  "budget_usd": 1500,
  "timeline": "urgent",
  "summary": "GrowthCo cần định tuyến lead từ HubSpot sang Slack. Phạm vi đơn giản, ngân sách hợp lý, timeline rõ ràng.",
  "suggested_action": "Đặt lịch gọi discovery 20 phút trong tuần này — rất phù hợp với gói build 3 ngày."
}
```

**Bước 3 — Lưu vào Airtable.** Mỗi lead vào Airtable với đủ 12 trường được điền tự động, cho team một góc nhìn CRM sạch sẽ, sắp xếp được, không phải nhập tay.

**Bước 4 — Cảnh báo Telegram.** Một thông báo tức thì bắn ra kèm emoji chất lượng lead:

- 🔥 HOT — theo dõi trong vòng một giờ
- ⚡ WARM — theo dõi trong ngày
- ❄️ COLD — gửi bảng câu hỏi trước

Thông báo kèm luôn phần tóm tắt của AI và hành động gợi ý, nên bạn phản hồi đúng cách ngay lập tức thay vì vài giờ sau.

## Khác gì so với một form thông thường

Một form liên hệ cơ bản chỉ gửi email cho bạn. Workflow này:

- **Phân loại** lead (hot / warm / cold) để bạn biết nên ưu tiên vào đâu trước.
- **Trích xuất** ngân sách và timeline ngay cả khi viết bằng ngôn ngữ tự nhiên ("khoảng $1k", "gấp", "không vội").
- **Gợi ý** một hành động cụ thể riêng cho từng yêu cầu.
- **Lưu tất cả** vào CRM tự động — không copy-paste.
- **Cảnh báo tức thì** trên điện thoại, để bạn phản hồi trong vài phút thay vì vài giờ.

Khoảng cách giữa phản hồi 2 phút và phản hồi 3 giờ cho một lead nóng thường chính là ranh giới giữa thắng và thua hợp đồng.

## Tự trải nghiệm

Mình để workflow này chạy trực tiếp, nên bạn có thể thử ngay bây giờ.

**Bước 1 — Gửi một yêu cầu thử.** [Mở Form Yêu Cầu Dự Án](https://automation.etuannv.com/form/e22e7bc8-9840-416e-bf8d-71f117647172) và điền thông tin bất kỳ, thật hay bịa đều được. Thử hai kịch bản:

*Thử A — lead nóng (nên ra 🔥):*
- Tên: tên bạn
- Email: email bạn
- Công ty: tên công ty bất kỳ
- Mô tả: *"Chúng tôi cần một automation n8n để đồng bộ lead từ HubSpot sang Slack và gửi báo cáo hằng ngày. 3 nhân viên sales đang làm tay việc này, mỗi sáng 1 tiếng. Sẵn sàng bắt đầu tuần sau."*
- Ngân sách: $1500

*Thử B — lead lạnh (nên ra ❄️):*
- Tên: tên bạn
- Email: email bạn
- Mô tả: *"Tôi cần hỗ trợ automation gì đó"*
- Ngân sách: (để trống)

**Bước 2 — Xem nó xuất hiện trong Airtable.** [Xem Airtable trực tiếp](https://airtable.com/appFVeuyLArweHyTi/shrXQgx46zudpUMCw). Submission của bạn sẽ hiện ra thành một dòng mới trong vòng 30 giây, với đủ 12 trường do AI điền — gồm cả điểm chất lượng, ngân sách được trích xuất và hành động gợi ý.

**Bước 3 — Xem video demo.** [Xem trên YouTube](https://youtu.be/hYoz-02DN4Q) để thấy toàn bộ luồng từ đầu đến cuối: gửi form → Claude phân tích → dòng Airtable → cảnh báo Telegram, với cả một lead nóng và một lead lạnh để thấy AI phân biệt thế nào.

**Bước 4 — Lấy mã nguồn.** Toàn bộ workflow là mã nguồn mở trên GitHub — **[n8n-ai-lead-capture-airtable](https://github.com/etuannv/n8n-ai-lead-capture-airtable)**. Import file JSON, thêm credential của bạn, và nó là của bạn.

## Công nghệ & chi phí

| Thành phần | Công cụ | Chi phí |
|---|---|---|
| Engine workflow | n8n (tự host) | ~$5/tháng VPS |
| Phân loại bằng AI | Claude Haiku (Anthropic) | <$1/tháng với 100 lead |
| CRM / cơ sở dữ liệu | Airtable | Gói miễn phí |
| Thông báo | Telegram Bot | Miễn phí |
| **Tổng** | | **~$6/tháng** |

Với $6/tháng, nó thay thế 30–60 phút phân loại lead thủ công mỗi tuần.

## Phù hợp với ai

Workflow này hợp với mọi doanh nghiệp hay freelancer nhận yêu cầu qua form liên hệ hoặc email, muốn phản hồi nhanh hơn với lead giá trị cao, đã ngán việc copy dữ liệu form vào spreadsheet hay CRM bằng tay, và cần một cách đơn giản để ưu tiên follow-up mà không cần cả một đội sales.

Đặc biệt hiệu quả với freelancer, agency, consultant, startup SaaS và các doanh nghiệp dịch vụ nhỏ.

## Các biến thể mình có thể xây

Cùng một mô hình có thể áp dụng cho nhiều tình huống:

- **Sàng lọc hồ sơ ứng tuyển** — submission được chấm theo độ phù hợp, lưu vào Airtable, chỉ báo cho hiring manager với ứng viên mạnh.
- **Phân loại ticket hỗ trợ** — ticket đến được phân loại theo mức độ khẩn và loại, rồi định tuyến tới đúng người.
- **Sàng lọc đăng ký sự kiện** — form người tham dự được chấm theo độ khớp ICP, prospect nóng được đánh dấu để sales tiếp cận.
- **Xử lý yêu cầu nhà cung cấp / đối tác** — yêu cầu hợp tác được phân loại và định tuyến trước khi có ai kịp đọc.

Tất cả chạy trên cùng một stack: n8n + Claude AI + CRM bạn chọn (Airtable, HubSpot, Google Sheets, Notion).

## Muốn áp dụng cho doanh nghiệp của bạn?

Nếu team bạn đang mất thời gian phân loại lead thủ công, copy dữ liệu form, hoặc phản hồi chậm vì không có gì được ưu tiên, đây là một dự án build trong 1–3 ngày.

[Nhắn mình trên Upwork](https://www.upwork.com/freelancers/etuannv), mô tả quy trình form hoặc hộp thư hiện tại của bạn, và mình sẽ nói chính xác mình sẽ tự động hóa nó thế nào — cùng chi phí bao nhiêu.

---

*Tuan Nguyen là automation developer Top Rated Plus trên Upwork với hơn 10 năm kinh nghiệm Python và data engineering, chuyên xây hệ thống AI automation, workflow n8n và chatbot RAG cho khách hàng toàn cầu.*

---
**Xem thêm:**
- [AI Email Triage — tự động phân loại và ưu tiên hộp thư của bạn](https://etuannv.com/vi/posts/ai-email-triage-n8n-claude/) — cùng mô hình phân loại-và-định-tuyến áp dụng cho email
- [Prompt Engineering qua 5 cấp độ](https://etuannv.com/vi/posts/prompt-engineering-5-levels/) — các kỹ thuật prompt Cấp 1–5 mà workflow này dựa vào
- [Dùng Claude Code hiệu quả](https://etuannv.com/vi/posts/claude-code-efficiently/) — cách mình xây và ship những automation như thế này nhanh hơn
