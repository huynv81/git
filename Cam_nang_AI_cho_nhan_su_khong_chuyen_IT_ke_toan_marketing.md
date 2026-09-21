# AI trong công việc: hiểu đúng, chọn đúng, dùng đúng

> **Tài liệu học chính dành cho nhân sự không chuyên IT**  
> Áp dụng cho toàn công ty, bao gồm Be Better Foundation  
> Thời điểm đánh giá dự kiến: đầu tháng 11/2026 — được kiểm tra lại tối đa 2 lần

---

## 1. Vì sao phải dùng AI khi tôi vẫn đang làm việc tốt?

Đây là câu hỏi đúng và cần được trả lời trước khi học bất kỳ công cụ nào.

Một người làm việc tốt thường đã có kiến thức chuyên môn, biết quy trình và chịu trách nhiệm về kết quả. AI không phủ nhận những năng lực đó. AI giúp người có chuyên môn **giảm thời gian cho phần việc lặp lại**, dành nhiều thời gian hơn cho kiểm tra, phán đoán và tạo giá trị.

Ví dụ, một kế toán vẫn phải hiểu chứng từ, kỳ hạch toán và quy định thuế. Nhưng họ không nhất thiết phải tự đọc thủ công 200 dòng mô tả giao dịch để phân nhóm ban đầu. Một nhân viên marketing vẫn phải hiểu khách hàng và thương hiệu, nhưng không nhất thiết phải bắt đầu mọi bản nháp từ trang trắng.

Vấn đề gốc không phải là “con người làm chưa tốt”, mà là:

- Khối lượng thông tin tăng nhanh hơn thời gian làm việc.
- Nhiều thao tác lặp lại đang tiêu tốn thời gian của người có chuyên môn.
- Tốc độ phản hồi ngày càng quan trọng.
- Doanh nghiệp cần giảm chi phí nhưng vẫn tăng sản lượng và chất lượng.

AI tạo ra lợi thế khi nó rút ngắn đường đi từ dữ liệu đến quyết định:

```mermaid
flowchart LR
    A[Thông tin đầu vào] --> B[AI đọc, tìm, so sánh hoặc tạo bản nháp]
    B --> C[Nhân viên kiểm tra bằng nghiệp vụ và nguồn gốc]
    C --> D[Người có thẩm quyền quyết định]
```

**Nguyên tắc quan trọng nhất:** AI hỗ trợ xử lý; con người chịu trách nhiệm. Không giao cho AI quyền tự xác nhận số liệu, hạch toán, kê khai thuế, phát hành nội dung hoặc phê duyệt thanh toán.

### Dùng AI có thực sự hiệu quả không?

Không đánh giá bằng cảm giác. Chọn một công việc lặp lại, thực hiện 3–5 lần và ghi bốn chỉ số:

| Chỉ số | Câu hỏi cần trả lời |
|---|---|
| Thời gian | Trước và sau khi dùng AI mất bao lâu? |
| Chất lượng | Có giảm lỗi, thiếu ý hoặc số lần sửa không? |
| Sản lượng | Trong cùng thời gian làm được thêm bao nhiêu? |
| Rủi ro | Có lộ dữ liệu, dùng sai nguồn hoặc tạo kết quả sai không? |

Chỉ giữ cách dùng giúp **tiết kiệm thời gian mà vẫn kiểm soát được chất lượng và rủi ro**.

---

## 2. Hiểu AI từ nguyên lý gốc

AI tạo sinh là hệ thống nhận dữ liệu và chỉ dẫn, sau đó dự đoán để tạo ra câu trả lời phù hợp. Nó không “biết” và không chịu trách nhiệm như con người. Vì vậy, câu trả lời có thể trôi chảy nhưng vẫn sai.

### AI mạnh ở đâu?

| Năng lực | Công việc phù hợp | Ví dụ |
|---|---|---|
| Đọc và viết ngôn ngữ | Tóm tắt, soạn nháp, chuẩn hóa cách diễn đạt | Chuyển biên bản họp thành danh sách việc cần làm |
| Tìm và tổng hợp | Thu thập nhiều nguồn, tìm điểm giống và khác | So sánh ba chính sách hoặc tổng hợp xu hướng thị trường |
| Nhìn hình ảnh | Đọc ảnh, bảng, hóa đơn hoặc ảnh chụp màn hình | Trích trường thông tin từ hóa đơn để người dùng kiểm tra |
| Phân tích dữ liệu | Làm sạch, phân nhóm, tính toán, tìm bất thường | So sánh ngân sách với thực tế và chỉ ra chênh lệch |
| Tạo nội dung đa phương tiện | Tạo hoặc chỉnh sửa hình ảnh, video, bố cục | Làm ba phương án hình minh họa cho chiến dịch |
| Dùng công cụ | Tìm file, đọc hệ thống, chạy phép tính, thực hiện nhiều bước | Lấy dữ liệu được cấp quyền rồi lập báo cáo nháp |

### Vì sao AI có thể bịa hoặc tính sai?

AI thường tạo câu trả lời có vẻ hợp lý nhất, không mặc định kiểm chứng sự thật. Nó có thể sai khi thiếu dữ liệu, hiểu nhầm kỳ báo cáo, đọc sai ảnh, dùng nguồn cũ, bỏ qua ngoại lệ hoặc tự suy ra con số chưa được cung cấp.

Do đó, không yêu cầu chung chung: “Hãy làm báo cáo này thật chính xác.” Hãy thiết kế nhiệm vụ để sai sót dễ bị phát hiện:

1. Chỉ rõ nguồn nào được phép dùng.
2. Yêu cầu ghi nguồn hoặc vị trí dữ liệu cho từng kết luận quan trọng.
3. Tách dữ liệu gốc, phép tính, nhận xét và giả định thành các phần riêng.
4. Yêu cầu AI ghi “không đủ dữ liệu” thay vì đoán.
5. Tính lại số quan trọng bằng Excel/Google Sheets hoặc công cụ tính toán độc lập.
6. Đối chiếu tổng, số dư đầu kỳ–cuối kỳ, số dòng và các ngoại lệ.
7. Người có chuyên môn duyệt trước khi sử dụng.

### Mô hình, ngữ cảnh và chỉ dẫn là gì?

- **Mô hình** là “bộ não” AI. Mô hình nhanh/nhẹ phù hợp việc đơn giản, khối lượng lớn; mô hình suy luận mạnh phù hợp việc khó nhưng thường tốn thời gian và chi phí hơn.
- **Ngữ cảnh (context)** là toàn bộ thông tin AI đang được phép nhìn thấy: câu hỏi, file, lịch sử trao đổi, hướng dẫn và dữ liệu từ công cụ. Thiếu ngữ cảnh thì AI phải đoán; ngữ cảnh lẫn lộn thì AI dễ hiểu sai.
- **Prompt** là lời giao việc cho AI. Thông thường chỉ cần nói rõ AI phải làm gì, dùng thông tin nào và trả kết quả ra sao; với việc quan trọng thì thêm yêu cầu kiểm tra.
- **Instruction** là quy tắc dùng lặp lại. Có thể đặt ở cấp tổ chức/hệ thống, cấp ứng dụng hoặc cấp dự án. Cấp cao hơn quy định nguyên tắc chung; cấp thấp hơn bổ sung yêu cầu cụ thể và không được trái với cấp trên.

Chọn mô hình bằng thử nghiệm trên cùng một bộ việc mẫu. Tổng chi phí không chỉ là phí tài khoản: còn gồm thời gian chờ, thời gian kiểm tra và chi phí sửa sai.

---

## 3. Bản đồ công cụ: gặp việc gì thì dùng cái gì?

Không cần nhớ hàng chục tên sản phẩm. Chỉ cần xác định công việc thuộc nhóm nào và bắt đầu bằng công cụ đơn giản nhất đủ dùng.

### Nhóm A — Làm việc với nội dung

| Công cụ | Dùng khi nào | Cách hoạt động và cách dùng hiệu quả |
|---|---|---|
| **Chat** | Một câu hỏi hoặc một đầu ra ngắn | Trao đổi trực tiếp với AI. Cung cấp mục tiêu, dữ liệu cần thiết và mẫu đầu ra. Phù hợp soạn email, tóm tắt, giải thích hoặc tạo bản nháp; không phù hợp để tự quản lý một quy trình dài. |
| **Project** | Nhiều cuộc trao đổi cùng thuộc một công việc | Gom file, hướng dẫn và hội thoại vào một không gian chung để AI duy trì bối cảnh. Mỗi dự án chỉ nên có một mục tiêu rõ; đặt tên file, phiên bản và kỳ báo cáo nhất quán. |
| **Work / CoWork** | Nhiệm vụ có nhiều bước và nhiều tài liệu | AI lập kế hoạch, xử lý lần lượt và tạo sản phẩm có thể kiểm tra. Nêu rõ điểm nào AI được tự làm, điểm nào phải dừng xin duyệt. Không giao quyền rộng hơn mức cần thiết. Tên và phạm vi tính năng có thể khác theo gói/phiên bản. |
| **Skill** | Một cách làm tốt cần được dùng lại | Đóng gói hướng dẫn, mẫu, tiêu chuẩn và bước kiểm tra thành “quy trình nghề nghiệp” cho AI. Ví dụ: skill lập báo cáo công nợ phải luôn kiểm tra tổng, tuổi nợ, ngoại lệ và dẫn nguồn. Skill không phải kiến thức thần kỳ; chất lượng phụ thuộc vào quy trình được viết vào đó. |

### Nhóm B — Tìm và đọc thông tin

| Công cụ | Dùng khi nào | Cách hoạt động và cách dùng hiệu quả |
|---|---|---|
| **Web Search** | Cần một thông tin hiện hành hoặc vài nguồn cụ thể | AI tìm trên web rồi tóm tắt. Yêu cầu ưu tiên nguồn chính thức, ghi ngày và gắn liên kết ngay cạnh kết luận. Mở nguồn gốc để kiểm tra trước khi dùng. |
| **Deep Research** | Câu hỏi rộng, cần nhiều nguồn và so sánh có hệ thống | AI tự chia câu hỏi, tìm nhiều nguồn và tổng hợp thành báo cáo. Trước khi chạy, chốt phạm vi, thời gian, thị trường và tiêu chí đánh giá. Không dùng báo cáo nghiên cứu làm bằng chứng duy nhất cho quyết định quan trọng. |
| **Vision / OCR** | Dữ liệu nằm trong ảnh, PDF scan, hóa đơn hoặc biểu mẫu | Vision hiểu bố cục và nội dung hình; OCR chuyển ký tự trong ảnh thành văn bản. Ảnh mờ, dấu thập phân, mã số thuế và chữ viết tay dễ bị đọc sai. Luôn đối chiếu trường quan trọng với chứng từ gốc. |

### Nhóm C — Phân tích và tạo sản phẩm

| Công cụ | Dùng khi nào | Cách hoạt động và cách dùng hiệu quả |
|---|---|---|
| **Excel/Sheets và công cụ phân tích dữ liệu** | Cần tính toán, lọc, đối chiếu hoặc biểu đồ | AI có thể đề xuất công thức, làm sạch bảng và giải thích chênh lệch. Giữ nguyên file gốc, làm trên bản sao; khóa định dạng số; kiểm tra tổng kiểm soát và ô mẫu. Phép tính quan trọng phải nằm trong bảng tính để có thể xem lại, không chỉ xuất hiện trong câu trả lời. |
| **Tạo ảnh/video và Claude Design** | Cần bản nháp hình ảnh, video, bố cục hoặc ý tưởng thiết kế | Cung cấp mục tiêu, đối tượng, kích thước, thông điệp, nhận diện thương hiệu và điều cấm. Kiểm tra bản quyền, logo, chính tả, khuôn mặt và thông tin sản phẩm trước khi xuất bản. Khả năng cụ thể tùy tài khoản và phiên bản. |
| **Codex / Claude Code** | Công việc liên quan mã nguồn, file có cấu trúc, tự động hóa hoặc xử lý dữ liệu bằng chương trình | Công cụ có thể đọc thư mục được cho phép, sửa file, chạy lệnh và kiểm thử. Người không chuyên IT dùng khi có bài toán rõ và có người kỹ thuật duyệt. Luôn yêu cầu giải thích thay đổi, tạo bản sao/kiểm soát phiên bản và chạy kiểm tra trước khi dùng kết quả. |

### Nhóm D — Kết nối và tự động hóa

Đây là nhóm dễ nhầm nhất. Có thể hiểu theo một chuỗi đơn giản:

```mermaid
flowchart LR
    A[AI nhận yêu cầu] --> B[Cầu nối được cấp quyền]
    B --> C[Ứng dụng hoặc dữ liệu công ty]
    C --> B
    B --> D[AI tạo kết quả để con người duyệt]
```

| Khái niệm | Bản chất | Ví dụ dễ hiểu | Lưu ý |
|---|---|---|---|
| **Connector** | Kết nối dựng sẵn giữa AI và một dịch vụ | Cho AI tìm tài liệu trong Google Drive hoặc đọc lịch được cấp quyền | Chỉ thấy dữ liệu trong phạm vi tài khoản và quyền đã cấp. Kiểm tra quyền trước khi kết nối. |
| **Plugin** | Gói mở rộng bổ sung một khả năng hoàn chỉnh | Một plugin có thể chứa hướng dẫn, công cụ kết nối và giao diện phục vụ một nghiệp vụ | Chỉ cài từ nguồn tin cậy; xem plugin được đọc và làm gì. Plugin là “gói tính năng”, không đồng nghĩa với MCP. |
| **MCP** | Một chuẩn chung để AI khám phá và gọi các công cụ/dữ liệu bên ngoài theo cách thống nhất | Thay vì xây một kiểu kết nối riêng cho từng AI, hệ thống công ty cung cấp một “quầy giao dịch chuẩn”; AI xem được dịch vụ nào có sẵn rồi gọi dịch vụ được phép | MCP không tự làm dữ liệu đúng và không tự bảo mật. Máy chủ MCP, quyền truy cập và từng thao tác vẫn phải được kiểm soát. |
| **API** | Cửa giao tiếp có quy tắc để hai phần mềm trao đổi với nhau | Hệ thống kế toán nhận yêu cầu “lấy danh sách hóa đơn tháng 9” và trả dữ liệu theo cấu trúc xác định | Thường cần đội kỹ thuật triển khai; phải quản lý khóa truy cập, nhật ký và giới hạn sử dụng. |
| **Agent / Automation / Scheduled workflow** | AI thực hiện một chuỗi bước theo mục tiêu, có thể chạy theo sự kiện hoặc lịch | Mỗi thứ Hai lấy dữ liệu được duyệt, lập báo cáo nháp, kiểm tra tổng và gửi người phụ trách xem | Chỉ tự động hóa quy trình đã ổn định. Bắt đầu ở chế độ tạo bản nháp; giữ bước phê duyệt trước hành động có hậu quả. |

#### MCP hoạt động như thế nào, nói bằng ngôn ngữ đời thường?

Hãy tưởng tượng AI là một nhân viên mới, còn các hệ thống công ty là những tủ hồ sơ khóa. **MCP giống quầy tiếp nhận có danh mục dịch vụ và kiểm soát quyền**:

1. Quầy cho AI biết có thể yêu cầu những việc gì, chẳng hạn “tìm hợp đồng” hoặc “đọc danh sách hóa đơn”.
2. AI gửi yêu cầu theo mẫu chuẩn.
3. Hệ thống kiểm tra danh tính và quyền.
4. Công cụ thực hiện yêu cầu rồi trả kết quả.
5. AI dùng kết quả để tạo bản nháp; con người kiểm tra và quyết định.

MCP hữu ích khi doanh nghiệp có nhiều nguồn dữ liệu hoặc muốn cùng một công cụ nội bộ dùng được với nhiều hệ sinh thái AI. Nhân viên nghiệp vụ không cần lập trình MCP, nhưng cần biết **AI đang truy cập nguồn nào, có quyền gì và thao tác nào cần phê duyệt**.

### Ba hệ sinh thái dùng trong công ty

| Hệ sinh thái | Nên học gì trước | Dùng nổi bật cho |
|---|---|---|
| **OpenAI** | ChatGPT, Project/Work, Codex, skill và kết nối | Hội thoại, công việc tri thức, phân tích dữ liệu, tạo nội dung và tác vụ kỹ thuật |
| **Claude** | Claude, Project, CoWork, Claude Code, skill và tính năng thiết kế | Đọc/viết tài liệu dài, công việc nhiều bước, thiết kế và tác vụ kỹ thuật |
| **Gemini** | Gemini, Gem, Deep Research, tạo/xử lý ảnh-video, Google AI Studio | Công việc gắn với hệ sinh thái Google, nghiên cứu và thử nghiệm mô hình |

**Gem** là một phiên bản Gemini được cấu hình cho nhiệm vụ lặp lại bằng hướng dẫn riêng. **Google AI Studio** là môi trường thử mô hình, prompt và cấu hình nâng cao; nhân viên nghiệp vụ dùng để thử nghiệm có kiểm soát, không đưa dữ liệu nhạy cảm vào khi chưa được cho phép.

Cách phân biệt nhanh:

- Việc ngắn, cần trao đổi: dùng **Chat**.
- Việc kéo dài, nhiều tài liệu: tạo **Project**.
- Việc nhiều bước: dùng **Work/CoWork** nếu tài khoản có.
- Việc kỹ thuật hoặc xử lý file bằng chương trình: dùng **Codex/Claude Code**, có kiểm tra.
- Việc lặp lại theo một chuẩn: tạo **Skill**.
- Cần dữ liệu bên ngoài: dùng **Connector/MCP/API** theo quyền được cấp.
- Quy trình ổn định và lặp định kỳ: mới xem xét **Agent/Automation**.

---

## 4. Cách giao việc để AI cho kết quả tốt và ít sai nhất

### Giao việc cho AI bằng ba câu hỏi

Không cần học công thức prompt và không cần viết dài. Trước khi gửi, chỉ cần tự trả lời ba câu:

1. **Muốn AI làm gì?** — Tóm tắt, so sánh, kiểm tra, phân tích hay soạn bản nháp?
2. **AI dùng thông tin nào?** — File, email, hình ảnh, kỳ báo cáo hoặc nguồn cụ thể nào?
3. **Muốn nhận kết quả ra sao?** — Một bảng, email, danh sách việc hay bản báo cáo?

Ghép ba câu trả lời thành một yêu cầu tự nhiên:

> Hãy **[làm việc gì]**, sử dụng **[thông tin nào]** và trả kết quả dưới dạng **[mong muốn]**.

Ví dụ đơn giản:

> Tóm tắt email dưới đây và trả ra danh sách gồm việc cần làm, người phụ trách và thời hạn.

Ví dụ với công nợ:

> Phân tích file công nợ tháng 9 đính kèm, chia theo tuổi nợ 0–30, 31–60, 61–90 và trên 90 ngày. Trả ra bảng khách hàng cần ưu tiên thu hồi.

Đối với số liệu hoặc công việc quan trọng, thêm **một câu an toàn** ở cuối:

> Nếu thiếu hoặc không đọc rõ dữ liệu, không được đoán; hãy đánh dấu `CẦN KIỂM TRA` và đối chiếu tổng trước khi trả kết quả.

Như vậy, prompt công nợ hoàn chỉnh chỉ cần:

> Phân tích file công nợ tháng 9 đính kèm, chia theo tuổi nợ 0–30, 31–60, 61–90 và trên 90 ngày. Trả ra bảng khách hàng cần ưu tiên thu hồi. Nếu thiếu hoặc không đọc rõ dữ liệu, không được đoán; hãy đánh dấu `CẦN KIỂM TRA` và đối chiếu tổng trước khi trả kết quả.

Nếu kết quả lần đầu chưa đúng, nói thẳng phần cần sửa, chẳng hạn: “Chỉ dùng ngày đến hạn, không dùng ngày hóa đơn” hoặc “Thêm cột số chứng từ”. Không cần viết lại toàn bộ prompt.

Những quy tắc dùng lặp lại nên được đặt một lần trong Project, Gem hoặc Skill. Khi đó, yêu cầu hằng ngày có thể rất ngắn:

> Phân tích file công nợ tháng 9 theo quy tắc của dự án và lập danh sách ưu tiên thu hồi.

### Quy trình bốn chặng

```mermaid
flowchart LR
    A[1. Chuẩn bị<br/>mục tiêu, nguồn, quyền] --> B[2. AI xử lý<br/>tạo bản nháp]
    B --> C[3. Kiểm tra<br/>nguồn, số, logic, ngoại lệ]
    C --> D[4. Phê duyệt<br/>lưu vết và sử dụng]
```

1. **Chuẩn bị:** xác định đầu ra, nguồn được dùng, dữ liệu nào được phép đưa vào và ai là người duyệt.
2. **AI xử lý:** giao từng chặng; yêu cầu AI nêu dữ liệu thiếu, giả định và phần chưa chắc chắn.
3. **Kiểm tra:** đối chiếu với nguồn gốc, công cụ tính toán và quy tắc nghiệp vụ.
4. **Phê duyệt:** người chịu trách nhiệm xác nhận, lưu phiên bản và chỉ sau đó mới phát hành hoặc thực hiện hành động.

### Năm kiểm tra bắt buộc

| Kiểm tra | Cách làm |
|---|---|
| Nguồn | Mở lại file/link gốc; xác nhận đúng phiên bản, kỳ và phạm vi |
| Số liệu | Tính lại bằng Excel/Sheets; kiểm tra tổng, số dòng, đơn vị và dấu thập phân |
| Logic | So với chính sách, quy trình và hiểu biết nghiệp vụ; tìm trường hợp ngoại lệ |
| Bảo mật | Không đưa mật khẩu, khóa truy cập, dữ liệu cá nhân hoặc bí mật kinh doanh vào công cụ chưa được duyệt |
| Đầu ra | Kiểm tra người nhận, tệp đính kèm, giọng điệu và quyền phê duyệt trước khi gửi |

Dừng và hỏi người phụ trách khi nguồn mâu thuẫn, dữ liệu thiếu, AI không dẫn được chứng cứ, kết quả ảnh hưởng tiền/thuế/pháp lý, hoặc công cụ yêu cầu quyền vượt quá nhiệm vụ.

---

## 5. Học bằng công việc thật và triển khai trong công ty

### Kế toán trưởng có thể ứng dụng AI vào việc gì?

Kế toán trưởng nên dùng AI như **trợ lý tổng hợp, trợ lý kiểm soát và trợ lý phân tích**, không dùng AI làm người phê duyệt. Các ứng dụng nên đi từ ít rủi ro đến nhiều kiểm soát:

| Công việc | AI chuẩn bị hoặc phát hiện | Kế toán trưởng chịu trách nhiệm |
|---|---|---|
| Đóng sổ cuối tháng | Checklist, việc còn thiếu, tài khoản biến động lớn và bút toán cần chú ý | Xác minh và phê duyệt bút toán, số liệu khóa sổ |
| Báo cáo quản trị | So sánh thực tế–ngân sách–kỳ trước, tạo bản nháp giải trình | Xác nhận nguyên nhân và thông điệp gửi lãnh đạo |
| Dự báo dòng tiền | Tổng hợp lịch thu–chi, lập kịch bản cơ sở/tốt/xấu, cảnh báo tuần thiếu tiền | Chọn giả định và quyết định phương án tài chính |
| Công nợ phải thu | Phân nhóm tuổi nợ, xếp thứ tự khoản cần thu, soạn danh sách theo dõi | Đánh giá khả năng thu và quyết định cách xử lý |
| Công nợ phải trả và chứng từ | Trích thông tin, tìm hóa đơn trùng, sai trường dữ liệu hoặc hồ sơ thiếu | Đối chiếu chứng từ và phê duyệt thanh toán |
| Kiểm soát nội bộ | Đánh dấu giao dịch bất thường, vượt hạn mức, thiếu phê duyệt hoặc có dấu hiệu chia nhỏ | Điều tra nguyên nhân; AI không được kết luận gian lận |
| Thuế và chính sách | Tìm nguồn chính thức, tóm tắt thay đổi, lập bảng so sánh quy định | Đọc văn bản gốc, đánh giá trường hợp thực tế và kết luận nghiệp vụ |
| Hồ sơ kiểm toán | Lập danh mục tài liệu, phát hiện hồ sơ thiếu, soạn bản giải trình ban đầu | Chịu trách nhiệm về bằng chứng và nội dung cung cấp |
| Quy trình và đào tạo | Viết SOP, checklist, câu hỏi tình huống và tài liệu cho nhân viên mới | Ban hành quy trình, phân quyền và kiểm tra việc thực hiện |
| Tư vấn ban lãnh đạo | Chuyển số liệu thành xu hướng, nguyên nhân, rủi ro và các kịch bản | Đưa ra khuyến nghị dựa trên chiến lược và thực tế doanh nghiệp |

Ba bài toán nên thí điểm trước là **báo cáo biến động tháng**, **dự báo dòng tiền 13 tuần** và **rà soát công nợ/chứng từ theo ngoại lệ**. Chúng lặp lại thường xuyên, dễ đo thời gian trước–sau và vẫn giữ được bước kiểm tra của kế toán trưởng.

### Cách biến một công việc thật thành quy trình AI

Mọi bài thực hành bên dưới đều đi theo cùng một đường:

```mermaid
flowchart LR
    A[Chọn một việc] --> B[Chuẩn bị dữ liệu]
    B --> C[Giao việc cho AI]
    C --> D[Kiểm tra kết quả]
    D -->|Có lỗi| E[Nói rõ lỗi và yêu cầu sửa]
    E --> D
    D -->|Đạt| F[Lưu cách làm và đo hiệu quả]
```

Không tự động hóa ngay từ đầu. Trước tiên phải làm thử bằng Chat, tìm được cách kiểm tra đáng tin cậy, sau đó mới đưa quy tắc vào Project/Skill và cuối cùng mới cân nhắc Connector, MCP hoặc lịch chạy tự động.

### Bài thực hành 1 — Báo cáo công nợ hàng tuần

**Kết quả cần đạt:** Trong thời gian ngắn, kế toán có bảng ưu tiên thu hồi nợ đúng tổng, đúng ngày và chỉ rõ hồ sơ cần kiểm tra.

**1. Chuẩn bị trước khi hỏi AI**

| Cần chuẩn bị | Nội dung |
|---|---|
| File dữ liệu | Bảng công nợ còn mở, số chứng từ, khách hàng, ngày hóa đơn, ngày đến hạn, số tiền, người phụ trách |
| Mốc thời gian | Ngày chốt báo cáo, ví dụ 30/09/2026 |
| Quy ước | Tuổi nợ tính từ ngày đến hạn; các nhóm 0–30, 31–60, 61–90 và trên 90 ngày |
| Điều kiện ưu tiên | Do kế toán trưởng quy định, ví dụ quá 60 ngày hoặc số dư trên 100 triệu đồng |
| Kiểm soát | Tổng số dòng và tổng công nợ trong file gốc |

Nếu chưa có ngày đến hạn hoặc tiêu chí ưu tiên, phải bổ sung trước. AI không thể tự biết chính sách tín dụng của công ty.

**2. Công cụ nên dùng**

- Lần đầu: Chat có khả năng đọc Excel/CSV.
- Khi làm hằng tuần: Project chứa quy ước và mẫu báo cáo.
- Khi cách làm đã ổn định: Skill kiểm tra công nợ; Connector/MCP chỉ dùng nếu được cấp quyền lấy dữ liệu từ hệ thống.

**3. Prompt dùng ngay**

> Phân tích file công nợ đính kèm tại ngày 30/09/2026. Tính tuổi nợ từ ngày đến hạn và chia thành 0–30, 31–60, 61–90 và trên 90 ngày. Xếp ưu tiên các khoản quá 60 ngày hoặc trên 100 triệu đồng. Trả ra ba phần: tổng hợp theo nhóm tuổi nợ; danh sách cần ưu tiên thu hồi; dữ liệu thiếu hoặc bất thường. Nếu thiếu ngày hoặc số tiền, không được đoán và phải ghi `CẦN KIỂM TRA`. Trước khi trả kết quả, đối chiếu số dòng và tổng công nợ với file gốc.

**4. Đầu ra đạt chuẩn phải có**

- Tổng số dòng và tổng tiền đầu vào.
- Bảng tổng hợp số tiền theo từng nhóm tuổi nợ.
- Danh sách ưu tiên gồm khách hàng, chứng từ, ngày đến hạn, số ngày quá hạn, số tiền và người phụ trách.
- Danh sách dòng thiếu dữ liệu, số dư âm, ngày bất hợp lý hoặc khả năng trùng chứng từ.
- Xác nhận tổng đầu ra bằng tổng đầu vào; nếu lệch phải nêu số chênh lệch.

**5. Cách kiểm tra và sửa lỗi**

Kế toán kiểm tra tổng tiền, số dòng; tính tay hoặc bằng Excel một số dòng ở mỗi nhóm; kiểm tra 100% khoản ưu tiên và ngoại lệ. AI không được tự suy đoán nguyên nhân khách hàng chậm trả.

Nếu phát hiện lỗi, không yêu cầu “làm lại cho đúng”. Hãy chỉ rõ bằng chứng:

> Dòng của khách hàng ABC đang tính 75 ngày nhưng từ ngày đến hạn 20/08/2026 đến ngày chốt 30/09/2026 không phải 75 ngày. Hãy kiểm tra lại công thức tuổi nợ, sửa toàn bộ cột này, giữ nguyên các cột khác và báo số dòng đã thay đổi. Sau đó đối chiếu lại tổng.

**6. Đo hiệu quả:** so sánh thời gian lập báo cáo, số lỗi phát hiện khi kiểm tra, số khoản bỏ sót và thời gian dành cho việc đôn đốc trước–sau khi dùng AI.

### Bài thực hành 2 — Chuẩn bị chiến dịch marketing

**Kết quả cần đạt:** Có bộ đề xuất chiến dịch bám đúng khách hàng, thông điệp và thương hiệu; mọi khẳng định về sản phẩm đều có nguồn hoặc được đánh dấu cần xác minh.

**1. Chuẩn bị trước khi hỏi AI**

| Cần chuẩn bị | Nội dung |
|---|---|
| Brief | Mục tiêu chiến dịch, sản phẩm, khách hàng, khu vực, thời gian và ngân sách |
| Nguồn sự thật | Thông tin sản phẩm đã duyệt, bảng giá, chính sách và đường dẫn chính thức |
| Thương hiệu | Giọng điệu, màu sắc, logo, từ được dùng và từ bị cấm |
| Kênh | Facebook, website, email, TikTok hoặc kênh cụ thể |
| Tiêu chí thành công | Lead, lượt đăng ký, doanh thu, tỷ lệ chuyển đổi hoặc chỉ số được giao |

**2. Công cụ nên dùng**

- Deep Research khi cần nghiên cứu thị trường hoặc đối thủ; phải yêu cầu nguồn và thời gian.
- Project lưu brief, khách hàng mục tiêu và quy chuẩn thương hiệu.
- Chat phát triển ý tưởng và nội dung; công cụ ảnh/video tạo bản nháp hình ảnh.

**3. Prompt dùng ngay**

> Dựa trên brief và bộ quy chuẩn thương hiệu đính kèm, đề xuất ba hướng chiến dịch cho [sản phẩm] nhắm tới [nhóm khách hàng]. Với mỗi hướng, trình bày thông điệp chính, lý do phù hợp, ý tưởng hình ảnh, nội dung cho Facebook và email, cùng chỉ số cần theo dõi. Chỉ sử dụng thông tin sản phẩm trong tài liệu đã cung cấp. Không tự tạo giá, ưu đãi, số liệu hoặc cam kết; nội dung nào chưa có nguồn phải ghi `CẦN XÁC MINH`.

**4. Đầu ra đạt chuẩn phải có**

- Ba phương án đủ khác nhau để lựa chọn, không chỉ thay vài từ.
- Bảng so sánh đối tượng, thông điệp, kênh, ưu điểm, rủi ro và chỉ số.
- Nội dung mẫu phù hợp giới hạn của từng kênh.
- Danh sách tuyên bố về giá, tính năng, kết quả hoặc thị trường cần kiểm chứng.
- Không sử dụng dữ liệu hoặc hình ảnh không rõ quyền sử dụng.

**5. Cách kiểm tra và sửa lỗi**

Người phụ trách kiểm tra thông tin sản phẩm, giá, ưu đãi, đối tượng, giọng điệu, chính tả, bản quyền và quy định quảng cáo. Không đánh giá chung chung rằng nội dung “chưa hay”; hãy nói chính xác điều cần thay đổi:

> Giữ nguyên phương án 2 và cấu trúc hiện tại. Bỏ câu “hiệu quả số 1” vì không có bằng chứng. Viết lại tiêu đề dưới 12 từ, dùng giọng điệu gần gũi trong tài liệu thương hiệu và không thay đổi giá hoặc ưu đãi.

**6. Đo hiệu quả:** thời gian từ brief đến bản nháp, số vòng sửa, tỷ lệ nội dung được duyệt, chi phí tạo bản nháp và kết quả chiến dịch. Không kết luận AI hiệu quả chỉ vì tạo được nhiều nội dung hơn.

### Bài thực hành 3 — Kiểm tra bộ chứng từ thanh toán

**Kết quả cần đạt:** AI tạo bảng đối chiếu và chỉ ra ngoại lệ để kế toán tập trung kiểm tra; AI không quyết định chứng từ hợp lệ và không phê duyệt thanh toán.

**1. Chuẩn bị trước khi hỏi AI**

| Cần chuẩn bị | Nội dung |
|---|---|
| Chứng từ | Hóa đơn, hợp đồng/đơn đặt hàng, biên bản nghiệm thu hoặc phiếu nhập |
| Danh mục chuẩn | Nhà cung cấp, mã số thuế, tài khoản ngân hàng và hạn mức đã được duyệt |
| Quy tắc đối chiếu | Những trường bắt buộc phải khớp; mức sai lệch được chấp nhận nếu có |
| Phạm vi quyền | Chỉ dùng công cụ/tài khoản đã được công ty cho phép xử lý loại dữ liệu này |
| Kiểm soát | Số bộ chứng từ, tổng số tiền và danh sách file đầu vào |

**2. Công cụ nên dùng**

- Vision/OCR để đọc ảnh và PDF scan.
- Excel/Sheets để kiểm tra trùng và tính tổng.
- Project/Skill để lưu danh mục trường bắt buộc và mẫu bảng ngoại lệ.
- Không kết nối trực tiếp hệ thống thanh toán nếu chưa có cơ chế phân quyền, nhật ký và phê duyệt.

**3. Prompt dùng ngay**

> Đọc các bộ chứng từ đính kèm và lập bảng gồm nhà cung cấp, mã số thuế, số hóa đơn, ngày hóa đơn, số hợp đồng/đơn hàng, số tiền trước thuế, thuế, tổng thanh toán và tài khoản ngân hàng. So sánh hóa đơn với hợp đồng/đơn hàng và biên bản nghiệm thu. Chỉ ghi `KHỚP`, `KHÔNG KHỚP` hoặc `THIẾU DỮ LIỆU` kèm vị trí chứng từ. Không tự sửa, không kết luận đủ điều kiện thanh toán. Đánh dấu ký tự không đọc rõ và kiểm tra hóa đơn có khả năng trùng số, trùng tiền hoặc trùng nhà cung cấp.

**4. Đầu ra đạt chuẩn phải có**

- Một dòng cho mỗi bộ chứng từ và đường dẫn/tên file làm bằng chứng.
- Từng trường được đối chiếu, không chỉ kết luận chung “hợp lệ”.
- Danh sách sai lệch, tài liệu thiếu, ký tự OCR không chắc chắn và hóa đơn có khả năng trùng.
- Tổng số bộ và tổng số tiền để đối chiếu với danh sách đầu vào.
- Không có cột “phê duyệt thanh toán” do AI quyết định.

**5. Cách kiểm tra và sửa lỗi**

Kế toán kiểm tra 100% mã số thuế, số hóa đơn, số tiền, tài khoản ngân hàng và mọi ngoại lệ; đồng thời chọn mẫu ngẫu nhiên trong nhóm AI ghi `KHỚP`. Khi ảnh mờ, xem chứng từ gốc thay vì yêu cầu AI đoán lại.

Ví dụ yêu cầu sửa:

> Hóa đơn `HD023.pdf` ghi tổng thanh toán 118.800.000 đồng, không phải 118.000.000 đồng. Hãy đọc lại riêng file này ở độ rõ cao hơn, cập nhật đúng một dòng tương ứng, giữ nguyên các dòng khác và tính lại tổng. Nếu vẫn không chắc chắn, ghi `CẦN KIỂM TRA BẢN GỐC`.

**6. Đo hiệu quả:** số phút mỗi bộ hồ sơ, tỷ lệ trường OCR sai, số ngoại lệ phát hiện đúng, số ngoại lệ bỏ sót và số bộ phải làm lại.

### Lộ trình bốn tuần gắn với ba bài thực hành

Mỗi nhân viên chọn bài gần công việc của mình nhất. Không cần thực hiện cả ba bài.

| Tuần | Công nợ | Marketing | Chứng từ thanh toán | Kết quả cuối tuần |
|---|---|---|---|---|
| **1 — Làm thử** | Chạy một file nhỏ và so với báo cáo cũ | Tạo một bộ nội dung từ brief cũ | Trích xuất 5–10 bộ chứng từ mẫu | Ghi thời gian làm cũ, thời gian dùng AI và lỗi tìm thấy |
| **2 — Làm đúng** | Chốt cách tính tuổi nợ và checklist đối chiếu | Chốt nguồn sự thật và checklist thương hiệu | Chốt trường bắt buộc và cách kiểm tra OCR | Có prompt ngắn, mẫu đầu ra và checklist được người phụ trách duyệt |
| **3 — Làm lặp lại** | Đưa quy tắc vào Project/Skill và chạy file mới | Đưa brief mẫu, thương hiệu vào Project | Đưa bảng kiểm vào Project/Skill và chạy bộ mới | Hai lần liên tiếp cho đầu ra cùng cấu trúc, không tăng lỗi |
| **4 — Đo và quyết định** | Chạy báo cáo tuần thật có người duyệt | Làm một nội dung thật trước khi phát hành | Xử lý một lô thật trong phạm vi được phép | Báo cáo trước–sau và quyết định: giữ, sửa hay dừng cách làm |

Một quy trình chỉ được coi là đạt khi **nhanh hơn, không làm tăng sai sót, không vi phạm bảo mật và người chịu trách nhiệm có thể kiểm tra được kết quả**. Nếu AI liên tục sai cùng một loại dữ liệu, dừng mở rộng; sửa dữ liệu đầu vào, quy tắc hoặc công cụ trước khi tiếp tục.

### Yêu cầu thực hiện

- Cài ứng dụng desktop Claude và ChatGPT/Codex theo hướng dẫn của công ty; Gemini sử dụng bằng tài khoản công ty theo gói được cấp.
- Người đã có tài khoản làm việc riêng tiếp tục dùng để học; người chưa có tài khoản đăng ký bản miễn phí bằng email công ty theo quy định.
- Chỉ sử dụng tài khoản trả phí và các kết nối do công ty cấp sau khi hoàn thành giai đoạn cơ bản.
- Dành 30 phút đến 2 giờ mỗi ngày trong giờ làm việc tùy lưu lượng công việc; nên cố định một khung giờ.
- Khi gặp khó khăn về cài đặt, tài khoản, quyền truy cập hoặc kỹ thuật, báo Thảo (Grace) hoặc Thanh IT.

### KPI nên đo

- Hoàn thành lộ trình và bài kiểm tra đầu tháng 11/2026.
- Có ít nhất một quy trình công việc thật được cải thiện và có số liệu trước–sau.
- Giảm thời gian nhưng không tăng lỗi hoặc vi phạm bảo mật.
- Biết giải thích vì sao chọn Chat, Project, Work/CoWork, Skill, Deep Research, Connector, MCP hoặc công cụ khác.
- Biết phát hiện giới hạn của AI và thực hiện đủ bước kiểm tra trước khi sử dụng kết quả.

---

### Tờ ghi nhớ một trang

1. **Tôi đang giải quyết việc gì?** Viết đầu ra mong muốn trong một câu.
2. **AI cần biết gì?** Chỉ cung cấp đúng nguồn, đúng kỳ, đúng phạm vi và đúng quyền.
3. **Dùng công cụ nào?** Chat cho việc ngắn; Project cho việc dài; Work/CoWork cho nhiều bước; Skill cho quy trình lặp; Connector/MCP/API cho dữ liệu ngoài; Automation chỉ khi quy trình đã ổn định.
4. **AI có thể sai ở đâu?** Nguồn, số liệu, logic, ngoại lệ, bảo mật và hành động.
5. **Tôi kiểm tra bằng gì?** Chứng từ gốc, nguồn chính thức, bảng tính, quy trình nghiệp vụ và người có thẩm quyền.
6. **Kết quả có tạo giá trị không?** Đo thời gian, chất lượng, sản lượng và rủi ro.

> **Câu cần nhớ:** Không học AI để trò chuyện nhiều hơn. Học AI để giao đúng việc, kiểm soát đúng rủi ro và tạo ra kết quả tốt hơn có thể đo được.

---

### Nguồn học chính thức

- OpenAI — [ChatGPT và Codex: tình huống sử dụng](https://learn.chatgpt.com/use-cases)
- Anthropic — [Bắt đầu với Claude Code](https://docs.anthropic.com/en/docs/claude-code/getting-started)
- Google — [Tạo và dùng Gems](https://support.google.com/gemini/answer/15236321?hl=en)
- Google — [Deep Research trong Gemini](https://support.google.com/gemini/answer/15719111?hl=en)
- Google — [Google AI Studio](https://ai.google.dev/aistudio)

> Tên tính năng, phạm vi truy cập và giới hạn sử dụng có thể thay đổi theo thời điểm, hệ điều hành và gói tài khoản. Khi học hoặc triển khai, ưu tiên tài liệu chính thức và quy định nội bộ hiện hành.
