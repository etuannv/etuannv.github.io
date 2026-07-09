---
title: "Dùng Claude Code Hiệu Quả: Context, Token, và Chọn Đúng Giao Diện"
date: 2026-07-09
tags: ["claude", "claude-code", "ai", "tools", "vscode"]
categories: ["posts"]
description: "Cách dùng Claude Code cho tốt, không chỉ là cho chạy được — quản context như một ngân sách, bàn giao giữa các phiên bằng CLAUDE.md, dùng hook như một sự đảm bảo, và chọn đúng giao diện (CLI vs VS Code) cho từng việc."
---

![Dùng Claude Code hiệu quả](claude-code-efficiently-feature-image.png)

Phần lớn mọi người học Claude Code theo cùng một kiểu. Bạn cài nó, chạy trong một thư mục, nhờ nó sửa một bug, và nó chạy. Tuyệt. Rồi một tháng sau, vào tối thứ Ba, bạn đã dùng hết 80% cửa sổ context, Claude quên mất quyết định kiến trúc bạn đưa ra một giờ trước, và bạn đang đốt token để giải thích lại chính dự án của mình.

Khoảng cách giữa "Claude Code chạy được" và "Claude Code chạy *tốt*" không nằm ở prompt hay hơn. Nó nằm ở ba thứ: cách bạn quản context, cách bạn bàn giao giữa các phiên, và bạn có chọn đúng giao diện cho công việc hay không.

Đây là những gì mình đúc rút được khi dùng nó hằng ngày trên các dự án Python và Django, viết ra để lần sau còn tìm lại được.

## Phần 1 — Claude Code thực chất là gì (và chạy ở đâu)

Claude Code là một công cụ lập trình dạng agent. Không phải autocomplete — mà là một agent đọc codebase của bạn, lập kế hoạch, sửa file, chạy lệnh và lặp lại.

Nó chạy ở bốn nơi, và điều này khiến nhiều người vấp:

| Giao diện | Là gì |
|---|---|
| **CLI** | `claude` trong bất kỳ terminal nào. Bản gốc. Đầy đủ tính năng. |
| **VS Code extension** | Panel GUI ngay trong IDE của bạn. Diff, checkpoint, tài liệu kế hoạch. |
| **Desktop app** | Claude Desktop có một tab Code. |
| **Web** | Phiên trên cloud có thể tiếp tục ở máy local. |

Hai sự thật khiến gần như ai cũng bất ngờ:

**Cài VS Code extension KHÔNG cho bạn lệnh `claude` trong terminal.** Extension đóng gói bản CLI riêng của nó cho panel chat. Muốn chạy `claude` trong terminal tích hợp của VS Code, bạn cần cài thêm bản CLI độc lập.

**Extension và CLI dùng chung lịch sử hội thoại.** Bắt đầu một cuộc trò chuyện trong extension, rồi chạy `claude --resume` trong terminal để tiếp tục nó ở đó. Bạn không bao giờ chọn cái này *thay cho* cái kia. Bạn chỉ đang chọn dùng cái nào *ngay lúc này*.

## Phần 2 — Việc đầu tiên bạn nên làm: `/init`

Chạy `/init` mỗi khi bạn làm việc trong một thư mục mới. Nó tạo ra một file `CLAUDE.md` — bộ não của dự án, được nạp vào context ở đầu mỗi phiên.

File này là thứ có đòn bẩy cao nhất mà bạn kiểm soát được.

### Một CLAUDE.md tốt trông như thế nào

**Nên:**
- Dùng gạch đầu dòng và tiêu đề ngắn — viết sao cho mật độ thông tin cao
- Đặt những guardrail quan trọng nhất ở **đầu** file
- Commit `CLAUDE.md` vào git — nó là config, và config xứng đáng được quản lý phiên bản
- Rà soát và cắt tỉa định kỳ — đây là tài liệu sống, không phải viết một lần rồi quên

**Không nên:**
- Đổ nguyên một style guide hay API docs vào — quá dài, và Claude đọc nó kém
- `@-include` các file lớn trừ khi thật sự cần — mỗi include tốn token ở mọi lượt
- Viết luật mơ hồ kiểu "viết code cho tốt" — luật phải cụ thể và kiểm tra được
- Để nó vượt quá ~500 dòng mà không tách — đó là lúc cần một thư mục `rules/`

### Bố cục đầy đủ của `.claude/`

Khi dự án lớn lên, phần cấu hình sẽ trải ra:

```
.claude/
├── CLAUDE.md              ← bộ não dự án, commit vào git
├── CLAUDE.local.md        ← ghi chú riêng tư, không bao giờ push
├── settings.json          ← permission + hook, commit
├── settings.local.json    ← settings riêng tư
├── memory.md              ← bộ nhớ làm việc của chính Claude
├── rules/                 ← luật chi tiết, tách ra khỏi CLAUDE.md
│   ├── workflow.md
│   ├── design.md
│   └── tech-defaults.md
├── agents/                ← subagent chuyên biệt
│   ├── researcher.md
│   └── reviewer.md
└── skills/                ← các tác vụ tái sử dụng, lặp lại được
    └── deploy-check.md
```

Các biến thể `.local` tồn tại để bạn commit config của cả nhóm mà vẫn giữ tùy chọn cá nhân ngoài repo.

## Phần 3 — Những tính năng thực sự thay đổi cách bạn làm việc

### Plan Mode

Claude mô tả nó định làm gì và chờ bạn phê duyệt trước khi động vào bất cứ thứ gì. Trong VS Code, kế hoạch mở ra thành một tài liệu markdown đầy đủ mà bạn có thể chú thích trực tiếp trước khi Claude bắt đầu.

Vòng lặp hiệu quả:

1. **Vòng 1** — Claude tạo kế hoạch
2. **Vòng 2** — Bạn rà soát và phản biện ("đừng động vào module auth")
3. **Vòng 3** — Claude chỉnh lại
4. **Vòng 4** — Bạn duyệt, Claude thực thi

**Dùng Plan Mode khi:**
- Tác vụ phức tạp hơn một thay đổi đơn lẻ
- Xây một tính năng mới từ đầu
- Tích hợp với một dịch vụ bên ngoài (database, thanh toán, API)
- Refactor lớn
- Bạn chưa chắc cách tiếp cận nào là tốt nhất
- Tác vụ động vào nhiều file cùng lúc

**Bỏ qua khi:**
- Sửa bug nhỏ, đơn giản
- Chỉnh text hay style lặt vặt
- Những việc bạn đã làm nhiều lần và nắm chắc

### Checkpoints — Tính năng không ai nhắc tới

Claude Code tự động lưu trạng thái code của bạn trước mỗi thay đổi. Bấm `Esc` hai lần, hoặc chạy `/rewind`, và bạn có thể nhảy ngược về. Khi rewind, bạn chọn khôi phục code, hội thoại, hay cả hai.

Đây là khác biệt giữa việc dè dặt trông chừng từng chỉnh sửa và tự tin giao phó một cuộc refactor lớn. Đi sai hướng ba lượt rồi? Rewind và rẽ nhánh.

Hai điều cần lưu ý: checkpoint bao **các chỉnh sửa của Claude**, không phải chỉnh sửa của bạn hay các lệnh bash. Và chúng bổ trợ cho git, không thay thế git.

Trong VS Code extension, rê chuột lên bất kỳ tin nhắn nào và bạn có nút rewind với ba lựa chọn: rẽ nhánh hội thoại từ đây (code giữ nguyên), rewind code về đây (hội thoại giữ nguyên), hoặc cả hai.

### Permission Modes

Bốn chế độ, từ thận trọng đến liều lĩnh:

| Chế độ | Hành vi |
|---|---|
| **Manual** | Hỏi trước mỗi hành động. Bắt đầu từ đây với bất kỳ codebase mới nào. |
| **Plan** | Mô tả ý định, chờ duyệt, không động vào gì. |
| **Edit automatically** | Sửa file không cần hỏi. |
| **Bypass permissions** | Bỏ qua toàn bộ lời hỏi. Cẩn thận khi dùng. |

Đặt mặc định trong settings của VS Code qua `claudeCode.initialPermissionMode`. Của mình là `plan`. Nếu tối trong tuần mình chỉ có chín mươi phút, một chỉnh sửa tự động sai là mất cả phiên.

### Subagents — Giữ context chính sạch sẽ

Một subagent chạy một tác vụ trong cửa sổ context riêng của nó và chỉ trả về phần tóm tắt. Cuộc trò chuyện chính của bạn không bao giờ thấy 40 trang tài liệu mà nó đã đọc.

Một agent `researcher` nằm ở `.claude/agents/researcher.md`:

```markdown
---
name: researcher
description: Research and summarize information on request
model: claude-sonnet-4-6
---

You are a research agent. Your job is to:
1. Gather information as requested
2. Analyze and compare the options
3. Return a concise summary — 500 words maximum

Always end with: a clear recommendation and the reason for it.
```

Rồi: *"Dùng agent researcher để tìm hiểu ba công cụ deploy website miễn phí phổ biến nhất."*

Claude sinh ra agent, agent đọc tài liệu, và context chính của bạn nhận về một câu trả lời 500 từ thay vì lượng HTML của ba website.

**Đây là thói quen tiết kiệm context hiệu quả nhất.** Bất cứ khi nào một tác vụ đòi đọc nhiều để cho ra ít, đó là lúc dùng subagent.

Nhưng nó không miễn phí. Subagent cũng ngốn quota — xem mục token bên dưới.

### Skills — Cho việc bạn làm lặp lại

Nếu subagent cô lập context, thì skill đóng gói sự lặp lại. Một skill là một định nghĩa tác vụ tái sử dụng đặt ở `.claude/skills/`:

```markdown
---
name: create-lesson
description: Generate a complete lesson outline and content from a topic
---

When the user provides a topic, automatically produce:

1. LESSON TITLE — short and compelling
2. LEARNING OBJECTIVES
   - After this lesson, learners will understand...
   - After this lesson, learners will be able to do...
   - After this lesson, learners will be able to apply...
3. MAIN CONTENT (5 sections)
   - Each with: heading + explanation + real-world example
4. REVIEW QUESTIONS (5)
   - Mix: 3 conceptual, 2 applied

Tone: friendly, accessible, suitable for non-technical readers.
```

Viết một lần. Gọi mãi mãi.

### Hooks — Phản xạ, không phải trí nhớ

`CLAUDE.md` là trí nhớ của Claude. Hook là phản xạ của nó: những lệnh shell tự động chạy tại các điểm cố định trong vòng đời.

Có khoảng ba mươi sự kiện hook, nhưng **90% việc dùng thực tế là ba cái**: `PreToolUse`, `PostToolUse`, và `Stop`.

| Sự kiện | Chạy khi | Ví dụ |
|---|---|---|
| `PreToolUse` | Trước khi Claude dùng một tool | Ghi log mọi lệnh bash để kiểm toán |
| `PostToolUse` | Sau khi Claude dùng một tool | Tự format file Claude vừa sửa |
| `Stop` | Khi Claude kết thúc một lượt | Phát tiếng chuông; backup file đã đổi |
| `UserPromptSubmit` | Mỗi prompt bạn gửi | Chèn luật dự án vào context |
| `SessionStart` | Phiên mở ra | In `git status` và các TODO đang mở |
| `PreCompact` | Trước khi context bị nén | Chụp lại transcript ra đĩa |

### Ba sự thật về hook giúp bạn tiết kiệm cả một buổi tối

**1. `exit 1` không chặn. Chỉ `exit 2` mới chặn.**

Đây là lỗi kinh điển của người viết hook lần đầu. Mọi mã thoát khác 0 đều bị coi là lỗi không chặn — cái guard của bạn âm thầm chẳng làm gì cả.

**2. Hook vẫn chạy ngay cả dưới `--dangerously-skip-permissions`.**

Một hook `PreToolUse` trả về `permissionDecision: "deny"` sẽ chặn tool *ngay cả trong chế độ bypass*. Bypass tắt các lời hỏi tương tác; nó không tắt hook.

Đọc lại lần nữa. Nghĩa là bạn có thể chạy Claude hoàn toàn tự động trong một worktree cô lập, mà guard "không bao giờ xóa thư mục này" của bạn vẫn có tiếng nói cuối cùng. Cộng đồng gọi mô hình này là **Safe YOLO**.

Nó cũng là một nguyên tắc đáng khái quát hóa: **chỉ dẫn là gợi ý; hook là đảm bảo.** Nếu một luật thực sự không được phép vi phạm, đừng viết nó vào `CLAUDE.md` rồi cầu may. Hãy viết một hook.

**3. Bảo vệ secret của bạn một cách tường minh.**

Nếu repo của bạn có file `.env` chứa API key hay khóa mã hóa, hãy thêm một luật `Read` deny cho đường dẫn đó trong `settings.json`, hoặc viết một hook `PreToolUse` khớp `Read|Edit|Write` và thoát 2 khi đường dẫn khớp `.env`.

Một luật deny chặn file không cho đến tay Claude — kể cả qua text được chọn hay thông báo mở-file.

## Phần 4 — Quản lý context (Nơi phần lớn token chết)

Context là một ngân sách. Hãy tiêu nó cho vấn đề, không phải cho việc đọc lại mọi thứ.

### Compact ở mức 70–80%, không phải ở 100%

Chạy `/compact` khi context của bạn ở quanh mức 70–80% — và lý tưởng là ngay sau khi làm xong một tính năng, không phải giữa chừng.

Đừng compact mù. Hãy nói cho nó biết cái gì quan trọng:

```
/compact Prioritize preserving the authentication system's
architecture, the database schema design, and the list of
files created — these are the most important decisions from
this session.
```

Không có chỉ dẫn đó, bộ tóm tắt tự quyết cái gì quan trọng. Và nó thường quyết sai.

### Bảy thói quen cắt giảm token

1. **Giữ một `CLAUDE.md` tốt.** Mỗi câu Claude phải hỏi về dự án của bạn là context lẽ ra không cần tiêu.

2. **Dùng subagent để nghiên cứu.** Cái tiết kiệm lớn nhất. Đọc thì đắt; tóm tắt thì rẻ.

3. **Chia tác vụ lớn thành các phiên nhỏ.** Xong một cái, ghi kết quả ra markdown rồi bắt đầu phiên mới.

4. **Đừng dán nội dung lớn vào chat.** Tham chiếu một file bằng `@`, hoặc đưa vào `CLAUDE.md`. Text đã dán nằm trong context mãi mãi.

5. **Cắt tỉa workspace.** Hỏi: *"Kiểm tra workspace xem có file và thư mục thừa không, và hỏi mình trước khi xóa bất cứ thứ gì."* Ít file hơn, tìm kiếm rẻ hơn.

6. **`@-include` tiết chế.** Mỗi include tốn token ở **mọi lượt**, không chỉ lượt đầu.

7. **Chạy `/usage`.** Nó cho thấy hành vi nào đang ngốn từ 10% usage trở lên — cache miss, context dài, các phiên có nhiều subagent hay làm song song — kèm mẹo cụ thể để giảm từng cái. Nó cũng bóc tách usage theo skill, subagent, plugin và MCP server.

Cái cuối cùng đáng làm trước khi tối ưu bất cứ thứ gì. Đoán mò token đi đâu là một cách phí token.

## Phần 5 — Vòng lặp bàn giao giữa các phiên

Context reset. Dự án thì không. Vòng lặp này là thứ giữ chúng kết nối với nhau.

### Bước 1 — Kết thúc phiên cho đúng cách

Trước khi đóng, nhờ Claude cập nhật `CLAUDE.md`:

```
Before finishing, update CLAUDE.md with what was completed
in this session: the updated status of each part, the next
steps for the following session, and the important decisions
made along with their reasons.
```

### Bước 2 — Bắt đầu phiên tiếp theo cho đúng cách

Đừng lao thẳng vào một tác vụ. Bắt Claude đọc trước đã:

```
Read CLAUDE.md and tell me where the project currently stands.
What are the next steps, and is there anything I should be
aware of?
```

### Bước 3 — Với một phiên lớn, lập kế hoạch trước khi xây

```
Based on CLAUDE.md and the project's current status, plan out
today's session: what to prioritize and in what order to
tackle things.
```

Lặp lại mỗi phiên, và `CLAUDE.md` trở thành trí nhớ dài hạn của dự án. Cửa sổ context thì ngắn. File thì không.

**Tự động hóa nửa nhàm chán.** Một hook `SessionStart` in `git status` và grep các TODO sẽ cho Claude biết trạng thái hiện tại trước khi bạn gõ một chữ. Những gì `SessionStart` và `UserPromptSubmit` ghi ra stdout được thêm vào context như thứ Claude có thể thấy và hành động theo — đó chính là điều khiến hai sự kiện này đặc biệt.

## Phần 6 — VS Code Extension: Bảy thứ bạn dùng hằng ngày

**1. Không thấy icon Spark?** Nó chỉ hiện trên thanh công cụ editor **khi có một file đang mở**. Mở một thư mục thôi là chưa đủ. Đây là điểm gây bối rối số một. Cách khác: bấm **✱ Claude Code** ở thanh trạng thái góc dưới phải — cái đó chạy kể cả khi không mở file nào.

**2. `Alt+K` / `Option+K` chèn một mention theo dải dòng.** Chọn code, bấm phím đó, và `@app.py#5-10` rơi vào prompt của bạn. Dù sao Claude vẫn tự thấy phần bạn chọn; footer hiển thị số dòng. Icon con mắt bị gạch chéo nghĩa là Claude *không* thấy nó.

**3. `@terminal:name` tham chiếu output của terminal.** Thôi copy-paste stack trace đi.

**4. `Cmd+Esc` / `Ctrl+Esc` chuyển focus** giữa editor và ô prompt. Tay giữ nguyên trên bàn phím.

**5. Rewind có ba lựa chọn.** Rê chuột lên bất kỳ tin nhắn nào: rẽ nhánh hội thoại (code giữ nguyên), rewind code (hội thoại giữ nguyên), hoặc cả hai.

**6. Claude tự đọc panel Problems của bạn.** Extension chạy một MCP server tên `ide` phơi ra `mcp__ide__getDiagnostics`, trả về mọi lỗi và cảnh báo mà VS Code biết. Mở file bị lỗi, nói "sửa mấy cái này", và Claude đã biết cái gì sai. Trên một codebase lớn, riêng điều này đã đủ biện minh cho việc dùng extension.

**7. Ghim panel vào secondary sidebar.** Claude luôn hiện ra trong lúc bạn code. Dùng tab cho các hội thoại song song — một chấm màu trên icon spark: xanh dương nghĩa là có một yêu cầu quyền đang chờ; cam nghĩa là Claude làm xong khi tab đang bị ẩn.

**Bonus:** lịch sử phiên có một tab **Remote**. Bắt đầu một tác vụ trên Claude Code phiên bản web ban ngày, tiếp tục nó trong VS Code buổi tối.

## Phần 7 — CLI vs Extension: Cái nào, khi nào

| | CLI (terminal) | VS Code extension |
|---|---|---|
| Lệnh & skill | Tất cả | Một tập con (gõ `/` để xem) |
| Cấu hình MCP server | Có | Một phần — thêm qua CLI, quản bằng `/mcp` |
| Checkpoints | Có | Có |
| Phím tắt bash `!` | Có | Không |
| Tab completion | Có | Không |
| Xem diff | Qua IDE | Cạnh nhau, gốc luôn trong IDE |
| Plan mode | Dạng text | Tài liệu markdown, chú thích trực tiếp |
| Truy cập panel Problems | Không | Có |

### Dùng extension khi

- Bạn rà kỹ từng chỉnh sửa và muốn diff thật
- Bạn sửa lỗi kiểu (type error), lỗi lint, hay bất cứ thứ gì trong panel Problems
- Bạn làm việc mang tính khám phá và có thể cần rewind
- Bạn muốn chú thích một kế hoạch trước khi Claude thực thi
- Bạn mới với một codebase và muốn có cổng kiểm duyệt quyền ở mỗi thay đổi

### Dùng CLI khi

- Bạn cần `!` để chạy nhanh một lệnh bash giữa cuộc trò chuyện
- Bạn cần một lệnh hoặc skill mà panel không phơi ra
- Bạn đang thiết lập MCP server
- Bạn chạy công việc song song cô lập: `claude --worktree feature-auth` khởi động Claude trong worktree riêng với file và nhánh riêng, để các phiên song song không giẫm lên nhau
- Bạn đang trên một server, trong CI, hoặc qua SSH

### Nhưng đa số: dùng cả hai

Chúng dùng chung lịch sử hội thoại. Làm việc trong extension, gõ `claude --resume` trong terminal tích hợp khi cần sức mạnh của terminal, và tiếp tục trong cùng một phiên.

Đó mới là câu trả lời thật. Câu hỏi không phải là chọn công cụ nào. Mà là cái nào hợp cho năm phút tiếp theo.

## Bản rút gọn

Nếu bạn chỉ nhớ sáu điều:

1. **`/init` trước tiên, luôn luôn.** Một `CLAUDE.md` tốt là file có đòn bẩy cao nhất trong repo của bạn.
2. **Plan Mode cho bất cứ thứ gì lớn hơn một dòng sửa.** Rà soát, phản biện, rồi duyệt.
3. **Subagent cho việc nghiên cứu.** Đọc thì đắt; tóm tắt thì rẻ.
4. **Compact ở 70–80%, và nói rõ cần giữ gì.** Đừng để bộ tóm tắt đoán mò.
5. **Chỉ dẫn là gợi ý; hook là đảm bảo.** Và `exit 2` mới chặn — không phải `exit 1`.
6. **Extension và CLI dùng chung lịch sử.** Thôi phân vân đi. Dùng cả hai.

Thói quen nằm dưới cả sáu điều: coi context như một ngân sách bạn đang tiêu, và `CLAUDE.md` như cuốn sổ cái còn lại khi ngân sách cạn.

## Tham khảo

- [Tài liệu Claude Code](https://code.claude.com/docs/en/overview)
- [Dùng Claude Code trong VS Code](https://code.claude.com/docs/en/vs-code)
- [Tài liệu về Hooks](https://code.claude.com/docs/en/hooks)

---

*Tuan Nguyen là automation developer Top Rated Plus trên Upwork với hơn 10 năm kinh nghiệm Python và data engineering, chuyên xây hệ thống AI automation, workflow n8n và chatbot RAG cho khách hàng toàn cầu.*

---
**Xem thêm:**
- [Vì sao mình chuyển từ GitHub Copilot sang Claude](https://etuannv.com/vi/posts/why-i-switched-from-github-copilot-to-claude/) — vì sao Claude thành công cụ code hằng ngày của mình
- [Prompt Engineering qua 5 cấp độ](https://etuannv.com/vi/posts/prompt-engineering-5-levels/) — nguyên tắc "chỉ dẫn là gợi ý, code là đảm bảo", áp dụng cho prompt
- [AI Email Triage — tự động phân loại và ưu tiên hộp thư của bạn](https://etuannv.com/vi/posts/ai-email-triage-n8n-claude/) — một workflow n8n + Claude chạy thật, mã nguồn trên GitHub
- [AI Thu Thập Lead — từ form đến CRM trong 30 giây](https://etuannv.com/vi/posts/n8n-ai-lead-capture-airtable/) — một workflow n8n + Claude chạy thật khác, mã nguồn trên GitHub
