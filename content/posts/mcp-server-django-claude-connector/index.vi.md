---
title: "Cách tôi xây dựng một MCP server chỉ đọc để kết nối ứng dụng Django với Claude"
date: 2026-09-05
tags: ["python", "django", "mcp", "claude", "automation", "api"]
categories: ["posts"]
description: "Những bài học từ việc xây dựng một MCP server chỉ đọc (read-only), biến một ứng dụng Django đang chạy production thành một connector cho Claude — từ bẫy async ORM, yêu cầu OAuth, đến những chi tiết deployment ít ai cảnh báo trước."
---

Mục lục

- MCP server là gì?
- Dự án: từ ứng dụng Django đến connector cho Claude
- Ba nguyên tắc trước khi viết bất kỳ dòng code nào
- Bài học 1: ORM của bạn sẽ "nổ" trong môi trường async
- Bài học 2: Chạy server như một Django management command
- Bài học 3: Tài khoản database read-only phức tạp hơn bạn tưởng
- Bài học 4: Deployment bắt buộc phải là HTTPS, và OAuth không phải tùy chọn
- Bài học 5: Proxy headers, nếu không server sẽ tự quảng cáo là 127.0.0.1
- Bài học 6: Tái sử dụng tầng truy vấn, khớp đúng với UI hiện có
- Bài học 7: Kiểm chứng giả định trên dữ liệu thật, không phải dữ liệu dev
- Bài học 8: Đưa cho model dữ liệu có cấu trúc và để nó tự diễn giải
- Checklist nhanh cho connector MCP của riêng bạn
- Kết luận

## MCP server là gì?

MCP (Model Context Protocol) là giao thức cho phép một trợ lý như Claude gọi các tool nằm ngoài cửa sổ chat. Một "connector" trong Claude thực chất chỉ là một MCP server mà client được trỏ tới — cùng cơ chế cho phép Claude đọc Gmail, kiểm tra lịch, hay truy vấn hệ thống payroll của bạn. Sau khi kết nối, Claude có thể gọi các tool của server ngay giữa cuộc hội thoại và suy luận trên bất cứ dữ liệu nào chúng trả về.

Phần thú vị nhất ở đây, với bất kỳ ai đang vận hành một web app sẵn có, là: bạn không cần xây một chatbot từ đầu, cũng không cần dạy Claude bất cứ điều gì về domain của mình trước. Bạn chỉ cần expose một số tool được mô tả rõ ràng trên nền ứng dụng đã có, và Claude sẽ tự quyết định khi nào và cách nào để gọi chúng.

## Dự án: từ ứng dụng Django đến connector cho Claude

Một khách hàng của tôi vận hành một mô hình e-commerce nhiều cửa hàng. Anh ấy đã dùng Claude cho phần lớn công việc hàng ngày — đã có connector cho payroll, cho Upwork, và (qua một bên thứ ba) cho ngân hàng của mình. Một ngày nọ, anh ấy đặt ra một câu hỏi nghe có vẻ đơn giản: liệu có thể làm điều tương tự cho hệ thống nội bộ, để hỏi nó bằng tiếng Anh thông thường giống như cách anh ấy hỏi về payroll không?

"Hệ thống nội bộ" ở đây là một Labor Tracking System (LTS) mà tôi đã duy trì vài năm nay: một ứng dụng Django/MySQL theo dõi công việc trong kho, đơn hàng, các station, và gần đây hơn là dữ liệu quảng cáo trên nhiều storefront. Yêu cầu đó đã trở thành một MCP server expose mười tool chỉ đọc từ một ứng dụng production đang chạy thật, cho Claude Desktop.

![Connector LTS đã kết nối trong Claude, hiển thị mười tool chỉ đọc cùng quyền truy cập từng tool](lts-connector-thumb.png)

Bài viết này là tập hợp những điều tôi ước mình đã biết trước khi bắt đầu — những cái bẫy khiến tôi mất hàng giờ, và những quyết định thiết kế mà tôi sẽ chọn lại không chút do dự.

## Ba nguyên tắc trước khi viết bất kỳ dòng code nào

Trước khi đụng vào bất kỳ tool nào, tôi đặt ra ba nguyên tắc, và tôi sẽ bắt đầu mọi dự án tương tự với đúng ba nguyên tắc này:

1. **Chỉ đọc, tuyệt đối.** Đây là một database production có người dùng thật đang hoạt động, và một scraper riêng vẫn đang ghi dữ liệu vào cùng lúc. MCP server chỉ bao giờ thực thi `SELECT`. Không ghi, không ngoại lệ.
2. **Tái sử dụng code có sẵn, không viết lại business logic.** Ứng dụng đã biết cách tính các con số của nó. Bất kỳ tool nào tự tính lại độc lập cuối cùng sẽ lệch pha và không khớp với những gì client thấy trên web UI.
3. **Không đụng đến PII của khách hàng.** Server này phục vụ dữ liệu lao động và hiệu suất quảng cáo. Nó không bao giờ chạm vào tên người mua, địa chỉ, hay nội dung đơn hàng.

Tôi triển khai theo từng lát mỏng — nền tảng với hai tool đầu tiên, rồi đến deployment, rồi mới thêm các tool khác — kiểm chứng từng lát end-to-end trước khi mở rộng. Nhịp độ đó quan trọng hơn bất kỳ mẹo kỹ thuật đơn lẻ nào.

## Bài học 1: ORM của bạn sẽ "nổ" trong môi trường async

Tool đầu tiên chạy hoàn hảo trong Django shell, rồi thất bại ngay khi tôi gọi nó qua giao thức MCP thật:

```
SynchronousOnlyOperation: You cannot call this from an async context —
use a thread or sync_to_async.
```

Framework MCP server chạy mỗi lần gọi tool trong một async request context, và ORM của Django từ chối các lệnh gọi database đồng bộ (synchronous) từ bên trong đó. Cách sửa là bọc mọi lệnh gọi có chạm đến ORM:

```python
from asgiref.sync import sync_to_async
result = await sync_to_async(run_the_query, thread_sensitive=True)(args)
```

Bài học phía sau cách sửa này quan trọng hơn chính cách sửa: **việc unit-test các hàm truy vấn một cách độc lập sẽ không phát hiện ra lỗi này.** Nó chỉ xuất hiện khi bạn thực sự chạy tool qua giao thức thật. Từ đó tôi coi việc "chạy round-trip qua giao thức thật" là tiêu chuẩn tối thiểu để coi một việc là "xong" — test trong shell sẽ đánh lừa bạn về môi trường runtime thật.

## Bài học 2: Chạy server như một Django management command

Thay vì dựng một service riêng phải tự cài đặt lại settings, cấu hình database, và load environment, tôi chạy MCP server như một Django management command:

```
python manage.py run_mcp_server --host 127.0.0.1 --port 8765
```

Vì đây là một management command, `django.setup()` đã chạy trước đó — nên các tool import đúng các model thật, dùng lại đúng settings và `.env`, và gọi cùng những hàm helper mà web app đang dùng. Một môi trường duy nhất, một nguồn sự thật duy nhất. Điều này cũng khiến deployment trở nên đơn giản: nó chỉ là thêm một systemd service nữa, cạnh những service đã chạy sẵn.

## Bài học 3: Tài khoản database read-only phức tạp hơn bạn tưởng

Tôi muốn có defense-in-depth: kể cả nếu một bug nào đó cố ghi dữ liệu, bản thân database cũng phải từ chối. Vì vậy tôi tạo một tài khoản MySQL riêng chỉ có quyền `SELECT`, và định tuyến mọi truy vấn của MCP qua một alias `DATABASES` riêng biệt.

Có hai điều khiến tôi mất thời gian, cả hai đều đáng ghi vào giấy nhớ:

- **Host quan trọng.** Ứng dụng kết nối tới một MySQL instance được quản lý qua network, không phải qua local socket. Một user được tạo dưới dạng `'ro_user'@'localhost'` sẽ không bao giờ khớp với các kết nối đó — bạn cần `@'%'` (hoặc host cụ thể). Tôi đã mất thời gian với một user tồn tại nhưng không bao giờ xác thực được.
- **Auth plugin quan trọng.** Plugin mặc định `caching_sha2_password` của MySQL 8 từ chối gửi credentials qua kết nối không mã hóa. Nếu ứng dụng của bạn kết nối không dùng TLS (ví dụ qua một mạng VPC riêng), user read-only cần dùng `mysql_native_password`, nếu không bạn sẽ gặp:
  ```
  Authentication plugin 'caching_sha2_password' reported error:
  Authentication requires secure connection.
  ```
  User chính của app đã dùng `mysql_native_password`; user read-only mới lại mặc định dùng plugin SHA-2, và sự lệch pha đó chính là toàn bộ vấn đề.

Điểm sâu xa hơn: hãy để alias read-only **kế thừa chính xác cấu hình kết nối của cái đang hoạt động** (cùng host, port, tùy chọn SSL) và chỉ đổi username với password. Đừng viết tay một khối kết nối mới — đó là cách các thiết lập SSL và auth âm thầm lệch nhau.

## Bài học 4: Deployment bắt buộc phải là HTTPS, và OAuth không phải tùy chọn

Các custom connector của Claude là những MCP server từ xa được truy cập qua HTTPS. Hai hệ quả này định hình toàn bộ việc deployment.

**Bạn cần TLS thật.** Tôi đặt server sau nginx sẵn có, dưới dạng một subdomain riêng, và kết thúc TLS ngay tại đó. Nếu bạn dùng Cloudflare, hãy để ý chế độ SSL: "Flexible" để lại chặng giữa Cloudflare và origin của bạn không mã hóa, và — tinh vi hơn — khiến app của bạn tưởng rằng request đến qua HTTP thuần dù trình duyệt đã dùng HTTPS. Điều đó làm hỏng OAuth redirect. Cuối cùng tôi chuyển sang TLS thật end-to-end (Let's Encrypt tại origin), sau đó tùy chọn thêm Cloudflare Full (strict) với một page rule giới hạn phạm vi.

**Xác thực phụ thuộc vào gói của client.** Điều này khiến tôi khá bất ngờ. Với gói Claude cá nhân, bạn không thể dán một static bearer token vào custom connector — Claude bắt buộc một luồng OAuth đầy đủ với PKCE. Một header dùng chung tĩnh chỉ là lựa chọn khả dụng ở gói Team/Enterprise. Vậy nên thiết kế kiểu "cứ nhét token vào header" vốn chạy tốt khi test local lại là ngõ cụt cho connector thật.

Tôi ủy quyền định danh cho Google qua một OAuth-proxy provider, giới hạn bằng một allowlist email tường minh, để việc kết nối nghĩa là "đăng nhập bằng Google" và chỉ những tài khoản được duyệt mới vào được. Một vài điều tôi học được theo cách khó khăn ở đây:

- **Ghim (pin) một phiên bản thư viện đã vá lỗi.** Các thư viện MCP OAuth từng có lỗ hổng dạng confused-deputy; hãy kiểm tra advisory và ghim ở bản đã fix trở lên.
- **Áp dụng allowlist ở hai nơi độc lập.** Tôi bọc lại token verifier của provider để từ chối các email không nằm trong allowlist, làm cho quá trình khởi động fail-closed nếu hook đó biến mất ở phiên bản tương lai, và kiểm tra lại lần nữa ở tầng tool. Một dependency phụ thuộc vào private-attribute không bao giờ nên là thứ duy nhất đứng giữa internet và dữ liệu của bạn.
- **Test cả trường hợp bị từ chối, không chỉ trường hợp được chấp nhận.** "Nó hoạt động khi tôi đăng nhập" không chứng minh được gì về allowlist. Tôi đã đăng nhập bằng một email *không* nằm trong danh sách và xác nhận server từ chối — đồng thời xác nhận việc từ chối đó trong log của server, để chắc chắn nó bị chặn đúng lý do.

## Bài học 5: Proxy headers, nếu không server sẽ tự quảng cáo là 127.0.0.1

Sau khi TLS đã hoạt động, metadata discovery của OAuth trả về mọi URL đều trỏ tới `http://127.0.0.1:8765` thay vì địa chỉ HTTPS công khai. Claude ngoan ngoãn cố kết nối tới `127.0.0.1` — trên chính máy của người dùng — và thất bại.

Server nằm sau nginx, nên nó chỉ bao giờ thấy `http://127.0.0.1`. Nó cần được cho biết để tin tưởng các forwarded header. Với một server dùng uvicorn, điều đó nghĩa là `proxy_headers=True` và `forwarded_allow_ips="*"` (an toàn ở đây vì nó chỉ bind vào localhost sau proxy), cộng với việc đảm bảo base URL công khai được cấu hình để metadata OAuth quảng cáo đúng host. Dấu hiệu đặc trưng — các endpoint metadata trả về `127.0.0.1` — là thứ đáng nhận ra ngay từ cái nhìn đầu tiên.

## Bài học 6: Tái sử dụng tầng truy vấn, khớp đúng với UI hiện có

Quyết định kiến trúc giá trị nhất là từ chối viết lại business logic. Các báo cáo quảng cáo trên web UI được vận hành bởi một hàm làm một việc thực sự phức tạp: nó phân bổ chi phí quảng cáo của mỗi listing across các product class mà listing đó có thể thuộc về, chia đều theo số lượng class để các phần cộng lại đúng bằng tổng. Việc tái tạo lại logic đó bên trong các tool MCP sẽ là một thảm họa chậm chạp và dần dần lệch pha. Thay vào đó, các tool **gọi đúng hàm mà trang web gọi**, rồi tổng hợp thêm ở trên. Kết quả: số liệu từ tool khớp với những gì client thấy trên trang, đến từng xu.

Khi "con số của AI có khớp với dashboard của tôi không?" là điều đầu tiên client sẽ kiểm tra, đây chính là ranh giới giữa niềm tin và sự bỏ cuộc.

## Bài học 7: Kiểm chứng giả định trên dữ liệu thật, không phải dữ liệu dev

Đây là bài học khiến tôi "hạ mình" nhất. Khi thiết kế các tool báo cáo quảng cáo, tôi đã xây một cơ chế khá cầu kỳ để xử lý một trường hợp tinh vi: khi cùng một listing chạy nhiều chiến dịch quảng cáo trong cùng một ngày, một số chỉ số ở cấp listing (như lượt xem trang) có vẻ được *sao chép* vào từng dòng chiến dịch thay vì độc lập — nên cộng dồn chúng sẽ nhân đôi con số thật. Tôi suy ra điều này từ một ví dụ duy nhất trong database dev.

Rồi tôi nhìn vào dữ liệu production thật, và bức tranh thay đổi hoàn toàn:

- Storefront chính (Etsy) **không hề có chiều dữ liệu campaign** — mỗi listing mỗi ngày chỉ có một dòng. Toàn bộ mối lo về multi-campaign trở nên vô nghĩa đối với chính dữ liệu mà client quan tâm nhất.
- Hành vi multi-campaign chỉ xuất hiện ở dữ liệu *nhập từ* các marketplace khác, thứ mà tôi đã được dặn là đừng quá quan trọng hóa.
- Trong khi đó, trường hợp *multi-class* — thứ tôi từng coi là edge case — lại xuất hiện ở khắp mọi nơi.

Thiết kế đúng cần hai quy tắc khác nhau trên hai trục khác nhau: cộng dồn các chỉ số thật sự theo từng campaign (chi phí, lượt click) qua các campaign, nhưng lấy một giá trị đại diện duy nhất cho các chỉ số cấp listing bị sao chép — và luôn cộng dồn theo trục class, vì đó là một phép chia rồi cộng lại thật sự. Tôi chỉ đi đến kết luận đó bằng cách đọc các dòng dữ liệu thật. Dữ liệu dev sẽ tự tin dẫn bạn đến kiến trúc sai; vài trăm dòng dữ liệu thật sẽ sửa sai cho bạn trong năm phút.

Tôi cũng xây một cơ chế đối soát (reconciliation) ngay trong một tool: nó tính tổng bằng hai cách hoàn toàn độc lập và báo cáo xem chúng có khớp nhau không. Chính kiểm tra đó sẽ phát hiện ra vấn đề — tại runtime, trên dữ liệu thật — nếu logic phân bổ có sai ở một trường hợp mà tôi không test được.

## Bài học 8: Đưa cho model dữ liệu có cấu trúc và để nó tự diễn giải

Một cám dỗ thường gặp là để tool trả về một câu văn đã viết sẵn. Đừng làm vậy. Các tool trả về **dữ liệu có cấu trúc kèm các lưu ý (caveat) tường minh**, và để Claude tự diễn giải. Ví dụ, tool worker-utilization trả về số giây thô *và* một trường `caveats` giải thích rằng giờ chấm công bao gồm cả nghỉ phép và nghỉ ốm, nên thời gian "không rõ nguyên nhân" không nhất thiết là lười biếng. Vì caveat đi kèm *cùng với dữ liệu*, Claude diễn giải con số một cách có trách nhiệm khi trả lời — nó sẽ không nói với chủ doanh nghiệp rằng một nhân viên rảnh rỗi 40% thời gian trong khi thực ra đó là một ngày lễ. Với một hệ thống đo lường con người, việc gói sự thận trọng ngay trong payload rất quan trọng.

Một điều nhỏ khác nhưng tác động lớn: **mô tả tool cũng là một phần của sản phẩm.** Model dùng chúng để quyết định gọi tool nào và điền tham số ra sao. Mô tả mơ hồ khiến model chọn nhầm tool hoặc điền sai định dạng ngày tháng. Mô tả rõ ràng — "cái này cần một username mà bạn có thể lấy từ tool find-worker; ngày tháng theo định dạng YYYY-MM-DD" — khiến toàn bộ hệ thống trông thông minh hơn hẳn.

## Checklist nhanh cho connector MCP của riêng bạn

Nếu bạn đang biến một ứng dụng sẵn có thành connector cho Claude, đây là thứ tự tôi sẽ làm:

- Quyết định phạm vi read hay write ngay từ đầu, và mặc định chọn read-only trừ khi có lý do cụ thể để không làm vậy.
- Chạy server bên trong tiến trình/management-command sẵn có của app, thay vì dựng một service song song.
- Nếu thêm một database user bị giới hạn quyền, hãy clone lại cấu hình kết nối đang hoạt động và chỉ đổi credentials.
- Lên kế hoạch cho TLS thật và OAuth với PKCE ngay từ đầu — đừng thiết kế quanh một static bearer token nếu có khả năng có user dùng gói cá nhân.
- Cấu hình proxy headers trước khi bạn debug "connection failed" đến lần thứ mười.
- Định tuyến mọi tool qua các hàm business-logic sẵn có của app, không bao giờ viết lại song song.
- Kiểm chứng mọi giả định không hiển nhiên trên dữ liệu production, không phải database dev.
- Trả về dữ liệu có cấu trúc và caveat, không phải văn xuôi, và viết mô tả tool như thể đó là tài liệu hướng dẫn cho người dùng — vì thực chất nó đúng là như vậy.

## Kết luận

Giờ đây client hỏi hệ thống nội bộ của anh ấy ngay trong cùng cửa sổ mà anh ấy kiểm tra payroll và dòng tiền — đó chính là mục đích ban đầu. Và khi anh ấy hỏi "listing nào lỗ tiền quảng cáo tuần trước," câu trả lời khớp với dashboard của anh ấy đến từng xu. Sự đối soát đó, hơn bất kỳ đoạn code khéo léo nào, mới là thứ khiến toàn bộ dự án trở nên thật, chứ không phải một bản demo.

Nếu bạn đang cân nhắc liệu một MCP server có đáng xây cho ứng dụng của mình hay không: vấn đề không nằm nhiều ở phần "ống nước" của giao thức, mà nằm ở kỷ luật deployment — TLS, OAuth, một database user được giới hạn quyền đúng cách, và cấu hình proxy sẽ ngốn nhiều thời gian của bạn hơn chính các tool. Hãy dự trù thời gian cho phần đó ngay từ đầu, phần còn lại sẽ đi rất nhanh.

---
**Xem thêm:**
- [Vì sao tôi chuyển từ GitHub Copilot sang Claude](/vi/posts/why-i-switched-from-github-copilot-to-claude/) — lý do Claude trở thành công cụ code hàng ngày của tôi
- [AI Email Triage với n8n và Claude](/vi/posts/ai-email-triage-n8n-claude/) — một dự án khác đưa Claude vào vận hành một quy trình kinh doanh thực tế
