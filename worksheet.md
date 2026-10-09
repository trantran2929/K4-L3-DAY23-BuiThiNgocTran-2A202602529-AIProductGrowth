# Worksheet — Trợ lý AI CSKH Shop Online (Zalo OA)

Họ tên: Bùi Thị Ngọc Trân · MSSV: 2A202602529 · Ngày làm: 09/10/2026

---

## Trạm 1 — Loại mô hình

**Câu chốt loại:**  
Chúng tôi là **B2B (SMB SaaS qua kênh PLG)** vì người trả tiền là các chủ shop kinh doanh trực tuyến (SMB), người dùng trực tiếp bảng điều khiển là chủ shop/nhân viên vận hành, sản phẩm phục vụ công việc kinh doanh của chính họ bằng cách nhúng vào Zalo OA của shop, và chúng tôi phân phối trực tiếp qua phễu tự phục vụ (PLG) không qua cơ chế chia sẻ doanh thu (rev-share) với bên thứ ba.

**Bảng đèn §3.2 (B2B) của mô hình** (đánh giá theo thực tế hôm nay):

| Đèn trong §3.2 | ✅ / 🔧 / ❌ | Số nằm ở đâu / Cần gì để đo |
|---|---|---|
| **Time-to-first-value (TTFV)** ⭐ | 🔧 | Cần log sự kiện: timestamp khi shop cấp quyền Webhook Zalo OA thành công và timestamp ticket đầu tiên AI giải quyết thành công không escalate. |
| **Pipeline coverage** | ❌ | Chưa áp dụng do mô hình 90 ngày đầu đi 100% kênh PLG (Product-Led), không dùng đội ngũ sales trực tiếp (sales-led). |
| **% deal chết ở security/procurement** | ❌ | Chưa bán cho khối Enterprise; chủ shop SMB tự quyết định qua thẻ thanh toán hoặc chuyển khoản ngân hàng. |
| **POC → paid (dùng thử → trả tiền)** | 🔧 | Cần nối database thanh toán (chuyển khoản/cổng thanh toán) với tài khoản shop sau 14 ngày dùng thử. |
| **Sales cycle (tuần)** | ❌ | Không đo chu kỳ sales rep; đo thời gian từ khi đăng ký tài khoản (Sign-up) đến khi trả phí gói $29/tháng (PLG conversion cycle). |
| **Usage depth trong tài khoản** | 🔧 | Cần event tracking số lượng ticket AI xử lý trên tổng lưu lượng chat phát sinh hàng tuần của shop. |
| **Chi phí triển khai ÷ ACV** | ✅ | Hiện tại bằng $0 do áp dụng 100% self-serve onboarding (chủ shop tự dán FAQ/chính sách và quét QR cấp quyền OA). |
| **Tập trung doanh thu** | 🔧 | Đo từ billing system: doanh thu (phí nền + usage) của shop lớn nhất chia cho tổng doanh thu tháng. |
| **NRR (Net Revenue Retention)** | ❌ | Chưa có dữ liệu vận hành > 6 tháng để đo mở rộng/co lại doanh thu theo cohort; ghi nhận đo sau quý 2. |
| **Gross Margin** | 🔧 | Cần tổng hợp hóa đơn API hàng tháng (Anthropic Claude Haiku 4.5 + Server/Vector DB) đối chiếu với tổng phí thu từ các shop. |

---

## Trạm 2 — Thẻ đèn

**North Star Metric:** **Time-to-First-Value (TTFV)** — Hiện tại: *Chưa đo (ước tính sandbox 4 giờ)* — Mục tiêu 90 ngày: **< 24 giờ**.

| # | Tầng (L/O/G) | Đèn | Định nghĩa (đếm gì · **không** đếm gì) | Công thức | Nhịp · ai lấy số | Báo trước cho |
|---|---|---|---|---|---|---|
| 1 | **L** | **Time-to-First-Value (TTFV)** ⭐ *(North Star)* | Số giờ từ lúc shop tích hợp thành công Webhook Zalo OA đến khi AI xử lý **ticket thật đầu tiên thành công** (không chuyển người, không mở lại trong 24h). **Không đếm** tin nhắn test của chủ shop. | $T_{\text{ticket 1 resolved}} - T_{\text{webhook connected}}$ (tính bằng giờ) | Từng shop · Kỹ thuật backend log | POC → Paid (tầng O) và Churn M3 (tầng G) |
| 2 | **L** | **Onboarding Drop-off Rate** | % shop đăng ký tài khoản nhưng **không hoàn thành** bước nạp tài liệu chính sách (Knowledge Base) và kết nối Zalo OA trong vòng 24 giờ. **Không đếm** các tài khoản nội bộ thử nghiệm. | $\frac{\text{Số shop drop-off bước setup}}{\text{Tổng số shop sign-up mới}} \times 100\%$ | Hằng tuần · Growth Lead (PostHog) | TTFV (tầng L) và POC → Paid (tầng O) |
| 3 | **L** | **Token Cost per Resolved Ticket** *(Chi phí AI)* | Chi phí API inference trung bình (input, output, cache write/read) cho **1 ticket được giải quyết thành công**. **Không tính** chi phí server cố định. | $\frac{\text{Tổng chi phí API Claude Haiku trong kỳ}}{\text{Số ticket giải quyết thành công (Containment)}}$ | Hằng ngày / Hằng tuần · Kỹ thuật (API Dashboard) | Gross Margin (tầng G) |
| 4 | **O** | **AI Containment Rate** | % ticket hội thoại khách hàng được AI tự xử lý hoàn chỉnh đúng chính sách, không cần escalate sang chủ shop. **Không tính** các tin nhắn rác hoặc spam bị chặn ở tầng ngoài. | $\frac{\text{Số ticket hoàn tất tự động}}{\text{Tổng số ticket hợp lệ tiếp nhận}} \times 100\%$ | Hằng tuần · Vận hành & QA sản phẩm | Gross Margin (tầng G) và CSAT của shop |
| 5 | **O** | **POC-to-Paid Conversion Rate** | % shop hoàn thành 14 ngày dùng thử miễn phí và thực hiện thanh toán gói $29/tháng (+ phí ticket vượt mức). **Không đếm** tài khoản nội bộ hoặc shop được tài trợ miễn phí. | $\frac{\text{Số shop thanh toán lần đầu}}{\text{Tổng số shop kết thúc 14 ngày trial}} \times 100\%$ | Hằng tháng · Chủ dự án (Stripe / Ngân hàng) | Doanh thu ARR & CAC Payback (tầng G) |
| 6 | **O** | **Tập trung doanh thu khách hàng** | % tổng doanh thu của shop tạo ra giá trị thanh toán cao nhất trên tổng doanh thu toàn hệ thống trong tháng. **Không gộp** các shop thuộc cùng một chủ sở hữu nếu đăng ký riêng lẻ. | $\frac{\text{Doanh thu shop cao nhất trong tháng}}{\text{Tổng doanh thu toàn bộ shop trong tháng}} \times 100\%$ | Hằng tháng · Kế toán / Chủ dự án | Rủi ro dòng tiền & Runway (tầng G) |
| 7 | **G** | **Gross Margin (Blended)** | Tỷ lệ lợi nhuận gộp sau khi trừ chi phí trực tiếp COGS (API model, database vector, hạ tầng webhook) trên tổng doanh thu. **Không trừ** chi phí quản lý và lương nhà sáng lập. | $\frac{\text{Doanh thu} - \text{COGS (API + Infra)}}{\text{Doanh thu}} \times 100\%$ | Hằng tháng / Quý · Báo cáo tài chính nội bộ | Runway và Khả năng sống sót của dự án |
| 8 | **G** | **CAC Payback (PLG)** | Số tháng cần thiết để lãi gộp từ một shop bù đắp toàn bộ chi phí thu hút shop đó (ad spend, content, khuyến mãi onboarding). | $\frac{\text{Chi phí CAC thực tế}}{\text{ARPU} \times \text{Gross Margin}}$ (tháng) | Hằng quý · Chủ dự án | Tốc độ tái đầu tư tăng trưởng |

**Đèn chi phí AI là đèn số:** 3 (Token Cost per Resolved Ticket) và đèn số 4 (AI Containment Rate — quyết định mẫu số của Cost/Job).

---

## Trạm 3 — Ngưỡng

| # | Đèn | 🟢 Xanh | 🟡 Vàng | 🔴 Đỏ | Nguồn | Lý do (1 câu) · Ngày kiểm tra nếu [BM] |
|---|---|---|---|---|---|---|
| 1 | **Time-to-First-Value (TTFV)** | < 12 giờ | 12 – 48 giờ | > 48 giờ | **[TB]** | Baseline tự đặt cho self-serve PLG: shop kết nối Zalo OA buổi sáng thì buổi tối cao điểm (20:00) AI phải giải quyết được ca đầu tiên. |
| 2 | **Onboarding Drop-off Rate** | < 25% | 25% – 45% | > 45% | **[BM]** | Benchmark SaaS SMB self-serve: tỷ lệ drop-off kích hoạt chuẩn ngành ở mức ~35%–40% (kiểm tra ngày 09/10/2026 tại Benchmarkit & OpenView). |
| 3 | **Token Cost per Resolved Ticket** | < $0.080 | $0.080 – $0.110 | > $0.110 | **[MH]** | Suy từ mô hình tài chính Day 22: giá hiệu dụng $0.2746/job, để giữ GM ≥ 60% thì chi phí biến đổi cho 1 job hoàn thành không được vượt quá $0.1098. |
| 4 | **AI Containment Rate** | ≥ 60% | 45% – 59% | < 41% | **[MH]** | Suy từ tab 2_Pricing dòng R33: điểm gãy kỹ thuật để duy trì Gross Margin mục tiêu 60% là Containment Rate tối thiểu phải đạt 40.59%. |
| 5 | **POC-to-Paid Conversion** | ≥ 15% | 8% – 14% | < 8% | **[BM]** | Benchmark B2B PLG SaaS chuyển đổi trial sang trả tiền trung vị ngành đạt 8%–10% (kiểm tra ngày 08/10/2026 tại ICONIQ GTM 2026 & OpenView). |
| 6 | **Tập trung doanh thu** | < 20% | 20% – 35% | > 35% | **[BM]** | Benchmark rủi ro tập trung doanh thu B2B: mất 1 khách làm sụt >30% doanh thu là báo động đỏ mất khả năng thanh toán (HANDBOOK §3.2, kiểm tra 27/08/2026). |
| 7 | **Gross Margin (Blended)** | ≥ 65% | 50% – 64% | < 50% | **[BM]** | Benchmark ngành AI-native năm 2026E đạt trung vị 53% (ICONIQ State of AI 07/2026), sản phẩm nhắm mục tiêu 72.6% nhưng cảnh báo nếu dưới 50%. |
| 8 | **CAC Payback (PLG)** | < 6 tháng | 6 – 12 tháng | > 12 tháng | **[MH]** | Suy từ ngân sách CAC $1,554.7: với SMB e-commerce có nguy cơ đóng shop cao, thời gian hoàn vốn bắt buộc phải dưới 12 tháng để tránh đọng vốn. |

---

### Phụ lục [MH] — Phép tính chi tiết (≥2 phép tính)

#### [MH] 1 — Ngưỡng Token Cost per Resolved Ticket (Chi phí AI cho 1 job)

```
Đầu vào (từ mô hình Day 22 - Tab 1_Cost_Job & Tab 2_Pricing):
- Giá hiệu dụng bình quân thu về trên mỗi resolved ticket: P = $0.2746/job
  (cấu thành từ: $29 phí nền / 650 job = $0.0446 + $0.23 phí resolution)
- Gross Margin mục tiêu tối thiểu của mô hình: GM_target = 60.0%
- Chi phí hạ tầng cố định ước tính mỗi job: Infra = $0.008/job

Phép tính:
Chi phí trực tiếp tối đa cho phép cho mỗi resolved job:
  COGS_max = P × (1 − GM_target) = $0.2746 × (1 − 0.60) = $0.1098/job
Trừ đi chi phí infra cố định $0.008/job:
  Inference_Cost_max = $0.1098 − $0.0080 = $0.1018/job (làm tròn an toàn ngưỡng đỏ: $0.110/job)
Chi phí mô phỏng hiện tại với Claude Haiku 4.5 có Prompt Caching:
  Inference_Cost_base = $0.02025 (LLM) + $0.01867 (QA) + $0.008 (Infra) = $0.0469/job

Kết quả phân tầng ngưỡng:
🟢 Xanh: < $0.080/job (Đảm bảo GM đạt > 70%, đúng thiết kế mô hình cơ sở $0.0753/job)
🟡 Vàng: $0.080 – $0.110/job (Biên lợi nhuận gộp bị bào mòn về mức 60% – 70%)
🔴 Đỏ:   > $0.110/job (Gross Margin gãy dưới ngưỡng sống còn 60%)
```

---

#### [MH] 2 — Ngưỡng AI Containment Rate (Tỷ lệ AI tự giải quyết thành công)

```
Đầu vào (từ mô hình Day 22 - Tab 2_Pricing dòng R27–R35):
- Chi phí biến đổi trên 1 job được thử nghiệm: v = $0.030275
  (gồm: LLM input/output fresh & cached trên 6 turns trao đổi)
- Chi phí QA nội bộ trên 1 job thử nghiệm: q = $0.018667
- Chi phí phía chúng tôi cho 1 ca escalate (HITL Biến thể A: shop tự xử lý): e = $0.000
- Giá bán hiệu dụng mục tiêu: P = $0.2746/job hoàn thành
- Gross Margin mục tiêu: GM = 60.0%

Phép tính suy ngược điểm hòa vốn biên lợi nhuận (Breakeven Containment C):
Công thức xác định Containment tối thiểu C để Gross Margin không thấp hơn 60%:
  Cost_per_completed_job = (v + q + e) / C
  GM = 1 − (Cost_per_completed_job / P) ≥ 60%
  ↔ Cost_per_completed_job ≤ P × (1 − 0.60) = $0.2746 × 0.40 = $0.10984
  ↔ (0.030275 + 0.018667) / C ≤ 0.10984
  ↔ 0.048942 / C ≤ 0.10984
  ↔ C ≥ 0.048942 / 0.10984 = 0.405887 ≈ 40.59%

Kết quả phân tầng ngưỡng:
🟢 Xanh: ≥ 60.0% (Mô hình đạt trạng thái vận hành tối ưu, GM đạt 70% – 73%)
🟡 Vàng: 45.0% – 59.9% (Hệ thống có dấu hiệu trả lời trượt nhưng GM vẫn giữ được trên 60%)
🔴 Đỏ:   < 41.0% (Chạm điểm gãy toán học 40.59%, Gross Margin rơi xuống dưới 60%)
```

---

#### [MH] 3 — Ngưỡng CAC Payback qua kênh PLG

```
Đầu vào (từ mô hình Day 22 - Tab 4_Channel_Fit dòng R05–R09):
- ARPU trung bình mỗi shop: $178.50/tháng
- Gross Margin trung bình: 72.58% (≈ 72.6%)
- Lãi gộp bình quân mỗi shop: Gross_Profit = $178.50 × 72.58% = $129.56/tháng
- Payback mục tiêu tối đa cho phân khúc SMB (theo Bessemer Venture Partners): 12 tháng

Phép tính:
Ngân sách CAC tối đa cho phép thu hút 1 shop trả phí:
  CAC_max = Gross_Profit × 12 = $129.56 × 12 = $1,554.72 ≈ $1,555.00
Nếu CAC vượt quá $1,555, chu kỳ hoàn vốn kéo dài quá 12 tháng.
Với đặc thù shop online có tỷ lệ ngừng kinh doanh cao sau 1 năm, vốn sẽ bị chôn vùi.

Kết quả phân tầng ngưỡng Payback:
🟢 Xanh: < 6 tháng (Thu hồi vốn cực nhanh, CAC thực tế < $777/shop)
🟡 Vàng: 6 – 12 tháng (Chấp nhận được trong giai đoạn tăng trưởng ban đầu)
🔴 Đỏ:   > 12 tháng (CAC thực tế vượt $1,555/shop, nguy cơ lỗ vốn vì shop churn trước khi hoàn vốn)
```

---

## Trạm 4 — 5 luật quyết định

Mỗi luật tuân thủ nghiêm ngặt cú pháp: **NẾU — TRONG/TRÊN — (VÀ) — THÌ — KHÔNG THÌ**.  
Có đúng **2 luật dừng (⏹)** theo yêu cầu Rubric.

1. **⏹ Luật 1 (Leading - TTFV · Luật dừng tiếp nhận):**  
   **NẾU** Time-to-First-Value (TTFV) > 48 giờ  
   **TRÊN** 3 shop onboarding liên tiếp  
   **VÀ** shop đã tích hợp thành công Webhook Zalo OA  
   **THÌ** đóng băng toàn bộ chiến dịch quảng cáo tiếp nhận shop mới trong 1 tuần, cả đội tập trung làm lại bộ tài liệu Knowledge Base mẫu và chuẩn hóa giao diện cấu hình FAQ 1-click  
   **KHÔNG THÌ** không được tăng chi phí ads để bù lượng user rớt ở phễu kích hoạt ban đầu.

2. **⏹ Luật 2 (Chi phí AI - Cost/Job · Luật dừng mở rộng):**  
   **NẾU** Token Cost per Resolved Ticket > $0.110  
   **TRONG** 7 ngày liên tiếp  
   **VÀ** tổng số ticket xử lý trong giai đoạn này ≥ 200 lượt  
   **THÌ** dừng việc kích hoạt thêm tính năng sinh câu trả lời tự do, kích hoạt ngay cơ chế Prompt Caching bắt buộc và cắt giảm tối đa số token context trong prompt hệ thống  
   **KHÔNG THÌ** không được tự ý tăng giá thu phí mỗi ticket lên shop để che giấu việc token bị phình to.

3. **Luật 3 (Operating - Containment Rate · Luật xử lý chất lượng bot):**  
   **NẾU** AI Containment Rate < 41%  
   **TRONG** 2 tuần liên tiếp  
   **VÀ** tổng số lượt hội thoại hợp lệ tiếp nhận ≥ 300 ca  
   **THÌ** thu hẹp phạm vi bot chỉ xử lý 2 luồng công việc chuẩn (tra cứu tình trạng vận đơn và gửi bảng size/giá), toàn bộ câu hỏi tư vấn phức tạp chuyển thẳng cho nhân viên shop  
   **KHÔNG THÌ** không được mở rộng thử nghiệm sang ngành hàng mới khi ngành hàng hiện tại chưa đạt điểm hòa vốn vận hành.

4. **Luật 4 (Operating - Tập trung khách hàng · Luật phòng vệ rủi ro):**  
   **NẾU** doanh thu từ 1 shop chiếm > 35% tổng doanh thu hệ thống  
   **TRONG** 2 tháng liên tiếp  
   **THÌ** ưu tiên dành 80% nguồn lực marketing tháng tiếp theo để chuyển đổi thêm ít nhất 5 shop quy mô vừa nhằm phân tán cơ cấu doanh thu  
   **KHÔNG THÌ** không chấp nhận code thêm bất kỳ tính năng riêng lẻ (custom request) nào theo đòi hỏi độc quyền của shop lớn đó.

5. **Luật 5 (Operating - POC-to-Paid · Luật chốt đơn vị giá):**  
   **NẾU** tỷ lệ POC-to-Paid < 8%  
   **TRÊN** 10 shop kết thúc chu kỳ dùng thử 14 ngày  
   **THÌ** trực tiếp phỏng vấn sâu 5 chủ shop từ chối thanh toán để kiểm tra lại Value Metric và đóng gói lại gói cơ bản thành $19/tháng kèm 100 ticket miễn phí  
   **KHÔNG THÌ** không được tự ý kéo dài thêm ngày dùng thử miễn phí (free trial) cho các shop không có phản hồi kích hoạt.

