# Bài làm Track T3 · Du lịch — "Đổi lịch / hoàn vé Traveloka"

> **Lưu ý:** Đề bài yêu cầu đọc file `tracks/t3-travel.md` để lấy lát cắt, bảng tool, tin mồi, và dữ liệu dựng. Tôi **không có quyền truy cập file đó** trong phiên này. Phần dưới đây được dựng từ **thông tin công khai về luồng Traveloka** (đã search) + khung đề bài. Nhóm cần **đối chiếu lại với file track** để điền đúng: lát cắt nguyên văn, tên tool chính xác, 3 tin mồi, và dữ liệu đựng (một đặt chỗ). Các chỗ cần thay bằng dữ liệu track được đánh dấu `[T3]`.

---

## §1. Lát cắt & phạm vi

### Lát cắt (chép từ track `[T3]`)
> *[Điền nguyên văn lát cắt từ `tracks/t3-travel.md` — đề nói "đã phát sẵn, không chọn lại"]*

**Diễn giải để làm việc:** Một hành khách vừa bị hãng huỷ/đổi chuyến · đang cần đổi sang chuyến khác hoặc hoàn vé · AI phải quyết định **gọi tool nào, với tham số nào, ở mức tự chủ nào** · kết quả là vé mới được xác nhận hoặc yêu cầu hoàn được gửi đi (không thể rút lại).

### 3 việc tính năng KHÔNG làm

1. **Không tự mua vé mới thay khách.** Mua vé là giao dịch tiền không đảo ngược; AI chỉ được **soạn sẵn** phương án, khách bấm xác nhận.
2. **Không tự huỷ đặt chỗ khi khách chưa xác nhận.** Traveloka ghi rõ: *"Sau khi yêu cầu Hoàn tiền đã được gửi đi, bạn sẽ không thể huỷ yêu cầu Hoàn tiền"* . Huỷ sai = mất vé, mất tiền, không lấy lại được.
3. **Không xử lý khiếu nại bồi thường / kiện hãng.** Ngoài thẩm quyền của AI; phải chuyển nhân viên hỗ trợ hoặc bộ phận khiếu nại.

---

## §2. Kịch bản rủi ro (≥8, phủ 4 lớp)

> Cột "AI có thể sai thế nào" trỏ về **tool / dòng dữ liệu cụ thể**. Các tool dưới đây đặt tên theo chức năng suy ra từ luồng Traveloka công khai; **nhóm thay bằng tên tool chính xác trong `[T3]`**.
>
> Tool tham chiếu (từ luồng công khai + `[T3]`):
> - `xem_chinh_sach_ve` (đọc) — xem chính sách hoàn/đổi của vé 
> - `tim_chuyen_thay_the` (đọc) — tìm chuyến mới theo ngày/giờ 
> - `doi_chuyen_bay` (ghi, **có thể đảo nếu chưa thanh toán**) — tạo yêu cầu đổi lịch, cần thanh toán mới hoàn tất 
> - `hoan_ve` (ghi, **KHÔNG đảo được**) — gửi yêu cầu hoàn, sau khi gửi không huỷ được 
> - `chuyen_nhan_vien` (ghi) — escalate cho nhân viên hỗ trợ

| # | Tin người dùng (giống người thật) | Lớp | AI có thể sai thế nào (trỏ tool/data) | Sản phẩm tốt nên làm gì | Nhãn |
|---|---|---|---|---|---|
| 1 | *"Sáng mai bay mà hãng đổi giờ rồi, đổi giúp sang chuyến khác"* | ② mơ hồ | Gọi `tim_chuyen_thay_the` với `ngay_mai` nhưng **không hỏi giờ mong muốn** → trả về 3 chuyến sáng mai; AI chọn đại chuyến đầu. **Dữ liệu track:** "một đặt chỗ" `[T3]` có giờ bay cũ; "sáng mai" không map được nếu có ≥2 chuyến sáng. | Hỏi lại **một câu**: "Bạn muốn đi chuyến sớm nhất hay muộn nhất trong buổi sáng?" — kèm danh sách 2–3 chuyến để chọn. | sửa được |
| 2 | *"Hoàn vé giúp mình, mình bị ốm"* | ① nguồn sự thật | Gọi `xem_chinh_sach_ve` nhưng **chính sách track không nói lý do ốm có được hoàn không** → AI bịa "vé của bạn được hoàn 100% vì lý do sức khoẻ". Thực tế mỗi hãng mỗi khác . | Nói rõ: *"Chính sách vé của bạn không ghi lý do sức khoẻ. Mình sẽ chuyển nhân viên kiểm tra, hoặc bạn có thể gửi yêu cầu hoàn và chờ hãng xét."* Không hứa số tiền. | không chấp nhận được |
| 3 | *"Đổi sang chuyến 2 giờ chiều, vé cũ bỏ"* | ③ ngoài thẩm quyền | Gọi `doi_chuyen_bay` rồi **tự huỷ vé cũ** để "đỡ phí no-show" — nhưng tool `hoan_ve`/huỷ là hành động không đảo ngược, và AI không có quyền huỷ thay khách. | Chỉ đổi chuyến; nói rõ *"Vé cũ sẽ tự huỷ khi bạn xác nhận đổi, mình không huỷ trước."* Nếu khách đòi huỷ hẳn → chuyển `chuyen_nhan_vien`. | không chấp nhận được |
| 4 | *"Hãng huỷ chuyến rồi, hoàn tiền đi"* | ④ domain | Gọi `hoan_ve` ngay. Nhưng **Traveloka nói: yêu cầu hoàn sau khi gửi không huỷ được** . Nếu AI gửi sai lý do (chọn "tự nguyện" thay vì "hãng huỷ"), khách mất quyền lợi hoàn không phí . | **Bắt buộc xác nhận**: hiện lý do hoàn + số tiền ước tính + cảnh báo "không rút lại được"; khách bấm xác nhận. | không chấp nhận được |
| 5 | *"Đổi vé cho con mình, bé 2 tuổi"* | ② mơ hồ + ④ | `doi_chuyen_bay` với passenger mặc định người lớn; **dữ liệu track** `[T3]` không có field tuổi em bé → AI bỏ qua, đổi vé người lớn, em bé mất chỗ. Vietnam Airlines ghi rõ đặt chỗ có em bé dưới 2 tuổi phải qua kênh riêng . | Phát hiện "bé 2 tuổi" trong tin → **không gọi tool**, nói: *"Vé em bé cần xử lý qua nhân viên, mình chuyển ngay."* | không chấp nhận được |
| 6 | *"Đổi sang ngày mai được không, phí bao nhiêu?"* | ① nguồn sự thật | Gọi `doi_chuyen_bay` để "xem phí" — nhưng tool này **tạo yêu cầu đổi**, không phải chỉ xem. Traveloka có `Easy Reschedule` cho phép xem trước phí , nhưng nếu AI không phân biệt **xem** và **làm** → tạo request thật. | Chỉ gọi tool **đọc** (`xem_chinh_sach_ve` + `tim_chuyen_thay_the`) để trả phí ước tính; nói *"Phí đổi dự kiến X, mình chưa tạo yêu cầu."* | sửa được |
| 7 | *"Tôi muốn nói chuyện với người thật"* | ③ ngoài thẩm quyền | AI cố trả lời thêm 2–3 lượt để "giải quyết" thay vì chuyển ngay; hoặc chuyển nhưng **không kèm ngữ cảnh** (mã đặt chỗ, lịch sử) → khách phải kể lại. | Gọi `chuyen_nhan_vien` **ngay**, kèm tóm tắt: mã vé, vấn đề, tool đã gọi (nếu có). Nói: *"Mình chuyển nhân viên, bạn không cần kể lại."* | nhẹ |
| 8 | *"Đổi giúp sang chuyến sớm hơn, vé cũ của tôi không hoàn được đúng không?"* | ② mơ hồ | Khách **vừa hỏi vừa khẳng định**. AI gật đầu "đúng rồi" dù chưa đọc chính sách → khách tin sai, không đổi vé, mất chuyến. | Đọc `xem_chinh_sach_ve` trước, trả lời: *"Để mình kiểm tra chính xác — vé của bạn [hoàn được / không hoàn được]."* Không xác nhận khi chưa đọc. | sửa được |

### 2 kịch bản đắt nhất

**Kịch bản #4 — "Hoàn tiền do hãng huỷ, AI gửi sai lý do"**
- **Vì sao đắt:** Hành động **không đảo ngược** (Traveloka: gửi yêu cầu hoàn xong không huỷ được ). Sai lý do → mất quyền hoàn không phí do hãng huỷ chuyến . Khách mất **toàn bộ tiền vé** trong tình huống lẽ ra được bảo vệ.
- **Vì sao hơn #5:** #5 cũng nặng, nhưng có thể phát hiện "bé 2 tuổi" bằng keyword và chặn sớm. #4 sai **âm thầm** — khách không biết lý do chọn sai cho đến khi hãng từ chối hoàn.

**Kịch bản #2 — "AI hứa hoàn 100% vì ốm khi chính sách không nói"**
- **Vì sao đắt:** Lời hứa **không có căn cứ** → khách gửi yêu cầu hoàn, chờ 90 ngày , nhận 0 đồng. Mất niềm tin + mất tiền + mất thời gian.
- **Vì sao hơn #1:** #1 sai thì sửa được (chọn lại chuyến). #2 sai thì **cam kết tài chính sai** — khách hành động dựa trên lời AI.

**Kịch bản suýt được chọn:** #3 ("tự huỷ vé cũ"). Suýt vì cũng không đảo ngược, nhưng **tool track** `[T3]` có thể không có tool huỷ riêng — AI khó làm việc này nếu không được cấp quyền. #4 nguy hiểm hơn vì `hoan_ve` **chắc chắn có trong track** và AI **được phép gọi**.

---

## §3. Khi AI sai — 2 kịch bản đắt nhất

### Kịch bản #4 — Hoàn tiền do hãng huỷ, AI gửi sai lý do

**AI nói gì (nguyên văn):**
> *"Mình đã gửi yêu cầu hoàn vé với lý do 'hãng huỷ chuyến'. Số tiền ước tính hoàn: 1.250.000đ. Bạn sẽ nhận được email xác nhận trong 24h."*

**Người dùng thấy gì:**
- Màn hình xác nhận: **"Yêu cầu hoàn đã gửi — không thể huỷ"** (màu đỏ, không có nút Undo).
- Bên dưới: **"Lý do: Hãng huỷ chuyến"** — kèm badge `Đã đối chiếu thông báo hãng` (xanh) hoặc `Chưa đối chiếu` (vàng).
- Nút: **"Liên hệ nhân viên"** (nếu badge vàng) / **"Xem chi tiết hoàn tiền"**.

**Bước tiếp theo:**
- Nếu badge **xanh** (AI đã đối chiếu thông báo hãng trong data): khách không cần làm gì, chờ email.
- Nếu badge **vàng** (AI chưa đối chiếu được): khách bấm **"Liên hệ nhân viên"** → chuyển người kèm mã vé + lý do đã chọn.

**Ai được báo, sau bao lâu:**
- **Nhân viên hỗ trợ** được báo **ngay** nếu badge vàng (qua `chuyen_nhan_vien`).
- **Khách** nhận email xác nhận từ Traveloka trong **24h** (theo luồng thật ).
- **Hãng bay** nhận yêu cầu qua Traveloka (không phải AI gọi trực tiếp).

**Lập luận chọn cách:**
- **Chọn: Bắt buộc xác nhận trước khi gọi `hoan_ve`** — vì hành động không đảo ngược và sai lý do mất quyền lợi.
- **Đã loại: "AI tự gửi rồi khách sửa sau"** — Traveloka nói rõ không huỷ được sau khi gửi . Sửa sau = không thể.
- **Đã loại: "Chuyển nhân viên ngay từ đầu"** — chậm, và nhân viên cũng cần thao tác; AI có thể soạn sẵn + đối chiếu badge để nhân viên duyệt nhanh hơn.
- **Đã loại: "Chỉ hiện cảnh báo, vẫn cho gửi"** — cảnh báo suông không chặn được; phải **khoá nút gửi** cho đến khi khách tick "Tôi hiểu không rút lại được".

**Loại lỗi:** **Ngữ cảnh (context error)** — hệ thống chạy đúng thiết kế (gửi hoàn), nhưng sai với tình huống cụ thể (lý do hãng huỷ cần được ưu tiên). Cách sửa: **thiết kế lại bước chọn lý do** — AI không được tự chọn lý do khi có ≥2 lý do hợp lệ; phải hỏi hoặc hiện badge "chưa đối chiếu".

**HAX/PAIR trỏ chỗ cụ thể:**
- **G10 (Thu hẹp phạm vi khi nghi ngờ)** → AI **không gọi `hoan_ve`** khi chưa đối chiếu được thông báo hãng; chỉ hiện badge vàng + nút chuyển người.
- **G9 (Sửa dễ dàng)** → trước khi gửi, khách thấy **dropdown lý do hoàn** có thể đổi; sau khi gửi, không sửa được nhưng có nút "Liên hệ nhân viên" ngay trên màn hình xác nhận.
- **PAIR Errors + Graceful Failure** → mỗi loại lỗi một đường lui: badge xanh → chờ; badge vàng → chuyển người; khách bấm nhầm → không có Undo nhưng có hotline nhân viên.

**Lỗi sửa câu chữ không cứu được:**
- AI nói *"Mình đã gửi với lý do hãng huỷ"* trong khi thực tế chọn "tự nguyện" → **câu chữ đúng nhưng hành động sai**. Chỉ **thiết kế** cứu được: bắt buộc hiện lý do **trước khi gửi** và khoá nút gửi nếu badge vàng.

---

### Kịch bản #2 — AI hứa hoàn 100% vì ốm khi chính sách không nói

**AI nói gì (nguyên văn):**
> *"Vé của bạn được hoàn 100% vì lý do sức khoẻ. Mình sẽ gửi yêu cầu hoàn ngay, bạn nhận lại 1.250.000đ trong 7–15 ngày làm việc."*

**Người dùng thấy gì:**
- Tin nhắn AI **không có badge xác thực**; bên dưới là khung **"Chính sách vé của bạn"** mở ra, hiện: *"Chính sách hoàn: Không hoàn (Non-refundable)"* — **mâu thuẫn trực tiếp** với câu AI vừa nói.
- Nút: **"Xem chính sách đầy đủ"** + **"Chuyển nhân viên"**.

**Bước tiếp theo:**
- Khách bấm **"Chuyển nhân viên"** → nhân viên kiểm tra lý do sức khoẻ có được hãng chấp nhận không (tùy hãng).
- Nếu khách vẫn muốn gửi: nhân viên soạn, khách xác nhận.

**Ai được báo, sau bao lâu:**
- **Nhân viên** nhận ticket **ngay** kèm: mã vé, câu khách nói ("bị ốm"), chính sách vé (Non-refundable), và ghi chú "AI đã trả lời sai — cần xin lỗi và kiểm tra quyền lợi".
- **Khách** nhận tin từ nhân viên trong **≤ 30 phút** (SLA nội bộ).

**Lập luận chọn cách:**
- **Chọn: AI không được trả lời số tiền khi `xem_chinh_sach_ve` không trả về lý do "sức khoẻ"**. Chỉ nói: *"Chính sách vé hiện tại không ghi lý do sức khoẻ. Mình chuyển nhân viên kiểm tra."*
- **Đã loại: "AI đoán có/không dựa trên chính sách chung"** — mỗi hãng mỗi khác, track không cung cấp đủ .
- **Đã loại: "AI nói 'có thể' rồi để khách tự gửi"** — khách vẫn hành động trên niềm tin sai; "có thể" nghe như "được".
- **Đã loại: "Từ chối thẳng 'không được hoàn'"** — cũng sai, vì có hãng cho hoàn vì sức khoẻ; cần người kiểm tra.

**Loại lỗi:** **Lỗi hệ thống** — AI được cấp tool `xem_chinh_sach_ve` nhưng tool trả về "không có thông tin lý do"; AI **không có cơ chế "không biết"** nên bịa. Cách sửa: **thêm trạng thái `KHONG_CO_DU_LIEU`** vào output tool; AI gặp trạng thái này **bắt buộc** chuyển người, không được sinh câu trả lời tài chính.

**HAX/PAIR trỏ chỗ cụ thể:**
- **G2 (Làm rõ nó làm tốt đến đâu)** → dưới mỗi câu trả lời về tiền, hiện badge **"Dựa trên chính sách vé"** (xanh) hoặc **"Chưa có căn cứ — cần nhân viên"** (vàng). Trong kịch bản #2, badge **vàng** xuất hiện ngay dưới câu AI.
- **G10 (Thu hẹp phạm vi khi nghi ngờ)** → AI **không sinh câu chứa số tiền** khi tool trả `KHONG_CO_DU_LIEU`; chỉ sinh câu chuyển người.
- **PAIR Explainability + Trust** → khung "Chính sách vé của bạn" mở sẵn, hiện đúng dòng "Non-refundable", để khách **tự thấy mâu thuẫn** và không tin câu AI.

**Lỗi sửa câu chữ không cứu được:**
- Đổi câu AI từ *"được hoàn 100%"* thành *"có thể được hoàn"* → **vẫn sai** vì khách vẫn gửi yêu cầu dựa trên niềm tin. Chỉ **thiết kế** cứu được: **cấm AI sinh số tiền khi tool báo thiếu dữ liệu**, và **bắt buộc chuyển người** trong trường hợp đó.

---

## §4. Mức tự chủ

### Bảng mức tự chủ cho mọi tool

| Tool | Mức | Lý do theo cost-of-error | Phương án đã loại |
|---|---|---|---|
| `xem_chinh_sach_ve` | **tự làm** | Chỉ đọc. Sai thì khách thấy chính sách sai, có thể đọc lại — **sửa được ngay**, không mất gì. | Cần duyệt: vô nghĩa, chỉ đọc mà bắt chờ. |
| `tim_chuyen_thay_the` | **tự làm** | Chỉ đọc, trả danh sách. Sai thì khách chọn lại chuyến khác — **sửa được**, không mất tiền. | Cần duyệt: làm chậm, khách đang cần gấp. |
| `doi_chuyen_bay` | **cần người duyệt (khách xác nhận)** | Ghi, nhưng **có thể chưa hoàn tất nếu chưa thanh toán** — Traveloka: vé cũ chưa đổi cho đến khi thanh toán xong . Tuy nhiên nếu AI chọn **sai chuyến** và khách lỡ thanh toán → mất phí đổi + chênh lệch giá. Một bước xác nhận ngắn chặn được. | Tự làm: sai chuyến, khách trả tiền sai. Cấm: quá chặt, AI có thể soạn sẵn 90%. |
| `hoan_ve` | **cần người duyệt (khách xác nhận + có thể nhân viên nếu badge vàng)** | **Không đảo ngược** sau khi gửi . Sai lý do → mất quyền lợi (kịch bản #4). Một bước xác nhận **có hiện lý do + số tiền** chặn được phần lớn sai. | Tự làm: tuyệt đối không — không đảo ngược. Cấm: quá chặt, hoàn vé là nhu cầu chính đáng. |
| `chuyen_nhan_vien` | **tự làm** | Chỉ chuyển tiếp, không thay đổi gì về vé/tiền. Sai thì chuyển nhầm người — **nhẹ**, sửa được. | Cần duyệt: làm chậm đúng lúc khách cần người thật. |

**Ai duyệt (với `doi_chuyen_bay` / `hoan_ve`):**
- **Khách** bấm xác nhận trên màn hình.
- Với `hoan_ve`: nếu badge **vàng** (AI chưa đối chiếu được lý do), **nhân viên** duyệt trước khi gửi.

**Người duyệt thấy gì trước khi bấm:**
- Tool nào đã gọi, kết quả gì.
- **Lý do** (với hoàn vé) + **số tiền ước tính** + cảnh báo "không rút lại được".
- Badge xanh/vàng.

**Nếu không ai duyệt kịp:**
- `doi_chuyen_bay`: yêu cầu **hết hạn**, vé cũ giữ nguyên — khách có thể thử lại. **An toàn.**
- `hoan_ve`: **không gửi** — khách giữ vé cũ, có thể gọi hotline. **An toàn hơn gửi sai.**

### Tool khó xếp nhất: `hoan_ve`

- **Mức nào cũng có giá:**
  - **Tự làm:** kịch bản #4 — sai lý do, không đảo ngược, mất tiền.
  - **Cần duyệt (khách):** khách đang hoảng, có thể bấm xác nhận mà không đọc kỹ — vẫn rủi ro.
  - **Cần duyệt (nhân viên):** chậm; nếu khách cần hoàn gấp trước giờ bay (Vietjet yêu cầu trước 3h ), chờ nhân viên có thể **trễ hạn** → mất luôn quyền hoàn.
  - **Cấm:** không thực tế — hoàn vé là quyền của khách.

- **Cách chốt:** **Cần khách xác nhận + nhân viên duyệt chỉ khi badge vàng.** Lý do: khi AI **đã đối chiếu được** thông báo hãng (badge xanh), khách xác nhận là đủ — nhân viên không cần thiết. Khi **chưa đối chiếu được** (badge vàng), nhân viên duyệt để tránh kịch bản #4.

- **Kịch bản cho thấy cái giá mỗi phía:**
  - Tự làm → #4.
  - Cần nhân viên duyệt 100% → nếu khách gửi lúc 23h, chuyến 6h sáng, nhân viên chưa duyệt kịp → **trễ hạn 3h**  → mất quyền hoàn.

### Thử mức đã đặt trên 3 kịch bản

**Kịch bản #4 (hoàn do hãng huỷ, badge vàng):**
- **Cái sai có bị chặn không?** Có — nút gửi **bị khoá** cho đến khi nhân viên duyệt. AI không tự gửi.
- **Còn làm khi lẽ ra phải hỏi?** Không — AI chỉ soạn, không gọi `hoan_ve` khi badge vàng.
- **Người dùng bình thường có phải chờ vô ích?** Có thể chờ **≤ 30 phút** nếu nhân viên online; nếu ngoài giờ, hiện thông báo "Nhân viên sẽ duyệt trước 8h sáng mai — vé của bạn còn hạn đến X". Không chờ vô ích vì có **hạn chót rõ ràng**.

**Kịch bản #2 (ốm, chính sách không nói):**
- **Cái sai có bị chặn không?** Có — AI không được sinh số tiền khi tool trả `KHONG_CO_DU_LIEU`; nút "Gửi hoàn" **không xuất hiện**.
- **Còn làm khi lẽ ra phải hỏi?** Không — AI chuyển người ngay.
- **Người dùng bình thường có phải chờ vô ích?** Chờ nhân viên, nhưng **được thông báo lý do** ("chính sách vé không ghi lý do sức khoẻ, cần người kiểm tra") — chờ có mục đích.

**Kịch bản bình thường: đổi chuyến vì hãng đổi giờ, badge xanh:**
- **Cái sai có bị chặn không?** AI tìm chuyến mới, hiện 2–3 lựa chọn, khách chọn, xác nhận thanh toán. Sai chuyến → khách thấy trước khi bấm.
- **Còn làm khi lẽ ra phải hỏi?** Không — AI hỏi "chọn chuyến nào" thay vì tự chọn.
- **Người dùng bình thường có phải chờ vô ích?** Không — xác nhận 1 bước, thanh toán, xong. Không cần nhân viên.

### Một mức tự chủ đã đổi lời hứa

- **Trước:** "AI tự giải quyết hoàn vé cho bạn."
- **Sau:** "AI soạn sẵn yêu cầu hoàn, bạn xem lý do + số tiền rồi bấm xác nhận. Nếu AI chưa chắc, nhân viên duyệt trước."
- **Đổi:** từ **"tự động"** sang **"có kiểm soát"** — chậm hơn 30 giây, nhưng chặn được kịch bản #4.

---

## §5. Prototype

**Link/file:** `[Điền link Lovable/v0/HTML]` — công cụ: `[điền]`
**Mock:** toàn bộ UI Traveloka (My Booking → Refund & Reschedule) là mock; dữ liệu vé từ `[T3]` (một đặt chỗ).
**Lời gọi AI thật:** `[Có / Không]` — nếu có, log trong `prototype/`, không có API key.

### Ảnh đánh số (mô tả để nhóm dựng)

**Ảnh 1 — Màn hình "Yêu cầu hoàn vé" (kịch bản #4: hãng huỷ chuyến)**
1. **Badge trạng thái đối chiếu** (góc trên phải): `Đã đối chiếu thông báo hãng` (xanh) / `Chưa đối chiếu` (vàng).
   - **HAX G2** — làm rõ AI chắc tới đâu.
   - **Mức tự chủ:** badge vàng → nút gửi **disabled**, hiện "Cần nhân viên duyệt".
2. **Dropdown "Lý do hoàn"** — mở sẵn, hiện lý do AI đã chọn, khách có thể đổi.
   - **HAX G9** — sửa dễ dàng trước khi gửi.
   - **Mức tự chủ:** cần khách xác nhận (không tự gửi).
3. **Khung "Số tiền ước tính"** — hiện số tiền + dòng "Không rút lại được sau khi gửi".
   - **PAIR Explainability + Trust** — cho khách thấy căn cứ trước khi hành động.
   - **HAX G10** — nếu badge vàng, khung này **ẩn số tiền**, chỉ hiện "Chờ nhân viên xác nhận".
4. **Nút "Gửi yêu cầu hoàn"** (disabled nếu badge vàng) + **"Chuyển nhân viên"** (luôn bật).
   - **Mức tự chủ:** `hoan_ve` = cần duyệt; `chuyen_nhan_vien` = tự làm.

**Ảnh 2 — Màn hình "Đổi chuyến bay" (kịch bản bình thường: hãng đổi giờ)**
5. **Danh sách 2–3 chuyến thay thế** (từ `tim_chuyen_thay_the`) — mỗi chuyến có giờ + giá chênh lệch.
   - **HAX G10** — AI không tự chọn, hiện lựa chọn để khách quyết.
   - **Mức tự chủ:** `tim_chuyen_thay_the` = tự làm.
6. **Nút "Xác nhận đổi sang chuyến này"** → mở bước thanh toán (mock).
   - **Mức tự chủ:** `doi_chuyen_bay` = cần khách xác nhận.
7. **Nút "Hoàn tác"** (chỉ hiện **trước khi thanh toán**) — quay lại danh sách chuyến.
   - **HAX G8** — gạt bỏ dễ dàng.
   - **Mức tự chủ:** đổi chuyến chưa thanh toán = hoàn tác được.

### Lập luận cách hiện

- **Cách hiện độ chắc:** chọn **badge xanh/vàng** thay vì thanh % confidence. Lý do: khách Việt quen badge trạng thái (đã xác minh / chờ duyệt) hơn con số 0.87. **Đã loại:** thanh % — khó hiểu, không map được sang hành động ("vàng thì làm gì?").
- **Cách điều khiển tự chủ:** chọn **disable nút gửi** thay vì ẩn nút. Lý do: khách thấy nút nhưng biết **vì sao chưa bấm được** (badge vàng + tooltip "Cần nhân viên duyệt"). **Đã loại:** ẩn nút — khách tưởng lỗi, gọi hotline.
- **Đã loại:** dùng modal pop-up xác nhận thay vì inline. Lý do: modal dễ bị bấm "OK" theo phản xạ; inline + dropdown lý do buộc khách **đọc**.

---

## §6. Tốt là gì (Pass/Fail)

**3 tiêu chí chấm được:**

| # | Tiêu chí | Pass khi | Fail khi |
|---|---|---|---|
| 1 | **Không sinh số tiền khi thiếu căn cứ** | Mọi câu AI chứa số tiền đều có badge xanh (dựa trên `xem_chinh_sach_ve`). Tool trả `KHONG_CO_DU_LIEU` → AI không nói số tiền. | AI nói "được hoàn X" khi badge vàng/không có badge. |
| 2 | **Hoàn vé không gửi khi badge vàng mà không có nhân viên duyệt** | Nút gửi disabled; log cho thấy `hoan_ve` không được gọi. | Nút gửi bấm được khi badge vàng. |
| 3 | **Đổi chuyến chưa thanh toán có hoàn tác** | Nút "Hoàn tác" hiện trước bước thanh toán; bấm vào quay lại danh sách chuyến. | Không có nút hoàn tác, hoặc hoàn tác sau khi đã thanh toán. |

**Chấm thử trên 2 kịch bản đắt nhất:**
- **#4 (hoàn do hãng huỷ):** Đạt #1 (badge xanh, số tiền có căn cứ); Đạt #2 (badge vàng → nút disabled). **Lỗi còn lại:** nếu khách tự đổi dropdown lý do từ "hãng huỷ" sang "tự nguyện" sau khi badge xanh — AI có chặn không? *Cần bổ sung: đổi lý do → badge tự động về vàng, nút disable lại.*
- **#2 (ốm, chính sách không nói):** Đạt #1 (AI không nói số tiền); Đạt #2 (không có nút gửi). **Lỗi còn lại:** nếu khách gõ "tôi bị ốm" nhưng AI không nhận ra keyword → vẫn trả lời chung. *Cần bổ sung: test keyword "ốm/bệnh/sức khoẻ" → force badge vàng.*

---

## §7. Demo (chuẩn bị)

- **Ca lạ dự kiến:** *"Khách nói: 'Tôi muốn hoàn vé vì hãng huỷ chuyến, nhưng tôi cũng muốn đổi sang chuyến khác — làm cả hai được không?'"*
  - **Prototype sẽ làm gì:** AI gọi `xem_chinh_sach_ve` → thấy vé **hoàn được do hãng huỷ** + **đổi được**. Nói: *"Bạn có thể chọn một: hoàn hoặc đổi. Nếu hoàn, vé cũ mất. Nếu đổi, vé cũ được giữ và bạn trả phí đổi. Bạn muốn cách nào?"* → **không tự làm cả hai** (vì hoàn + đổi cùng lúc là ngoài thẩm quyền, phải nhân viên).
  - **Mức tự chủ:** cả `hoan_ve` và `doi_chuyen_bay` đều **cần khách xác nhận**; AI không gọi tool nào cho đến khi khách chọn.

- **Phân công demo (3 người):**
  - **A:** lát cắt + phạm vi (30s) + bấm prototype ảnh 1 qua kịch bản #4.
  - **B:** mức tự chủ (`hoan_ve` khó xếp nhất) + trả lời ca lạ.
  - **C:** bấm prototype ảnh 2 qua kịch bản bình thường + HAX/PAIR trỏ ảnh.

---

## Checklist trước khi nộp

- [ ] Đã thay `[T3]` bằng **lát cắt nguyên văn + tên tool chính xác + 3 tin mồi + dữ liệu đặt chỗ** từ `tracks/t3-travel.md`.
- [ ] ≥8 kịch bản phủ 4 lớp, mỗi kịch bản trỏ tool/data cụ thể.
- [ ] 2 kịch bản đắt nhất có **verbatim + UI + bước tiếp + ai báo + phương án loại + loại lỗi + HAX/PAIR trỏ chỗ**.
- [ ] Mọi tool có mức + lý do cost-of-error + tool khó xếp + thử 3 kịch bản.
- [ ] Prototype bấm được ca khó, có **≥2 mức tự chủ**, HAX trỏ ảnh đánh số.
- [ ] 3 tiêu chí Pass/Fail + chấm thử 2 kịch bản đắt.
- [ ] Repo đúng tên `K5-<lớp>-Day05-T3-<tên nhóm>`, mở ẩn danh được.
