# OPERATING DASHBOARD — Trợ lý AI CSKH Shop Online (Zalo OA)

**Loại mô hình:** B2B (SMB SaaS qua PLG) · **Cập nhật:** 09/10/2026 · **Học viên:** Bùi Thị Ngọc Trân – 2A202602529  
**NORTH STAR:** **Time-to-First-Value (TTFV)** — Hiện tại: *Chưa đo (mô phỏng 4h)* — Mục tiêu: **< 12 giờ**

### Đèn báo sớm (Leading — nhìn hằng ngày / hằng tuần)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn | Báo trước cho |
|---|---|---|---|---|
| **Time-to-First-Value (TTFV)** ⭐ | Chưa đo | 🟢 <12h · 🟡 12–48h · 🔴 >48h | [TB] baseline self-serve | POC→Paid & Churn sớm |
| **Onboarding Drop-off Rate** | Chưa đo | 🟢 <25% · 🟡 25–45% · 🔴 >45% | [BM] Benchmarkit 09/10/2026 | TTFV & Activation |
| **Token Cost / Resolved Ticket** | $0.0753 | 🟢 <$0.080 · 🟡 $0.080–$0.110 · 🔴 >$0.110 | [MH] suy từ P=$0.27, GM≥60% | Gross Margin gộp |

### Đèn vận hành (Operating — nhìn hằng tuần / hằng tháng)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn | Báo trước cho |
|---|---|---|---|---|
| **AI Containment Rate** | 65% (mô phỏng) | 🟢 ≥60% · 🟡 45–59% · 🔴 <41% | [MH] breakeven GM≥60% | Gross Margin & Quá tải CSKH |
| **POC-to-Paid Conversion** | Chưa đo | 🟢 ≥15% · 🟡 8–14% · 🔴 <8% | [BM] ICONIQ 08/10/2026 | Doanh thu ARR & Payback |
| **Tập trung doanh thu** | 100% (1 shop mẫu) | 🟢 <20% · 🟡 20–35% · 🔴 >35% | [BM] B2B standard 27/08/2026 | Rủi ro dòng tiền & Churn |

### Đèn kết quả (Lagging — nhìn hằng quý)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn |
|---|---|---|---|
| **Gross Margin (Blended)** | 72.6% (mô phỏng) | 🟢 ≥65% · 🟡 50–64% · 🔴 <50% | [BM] AI-native 53% (ICONIQ 07/2026) |
| **CAC Payback (PLG)** | Chưa đo | 🟢 <6 tháng · 🟡 6–12 tháng · 🔴 >12 tháng | [MH] suy từ ARPU $178.5 & CAC budget $1,555 |

### 5 luật quyết định (⏹ = luật dừng)

1. ⏹ **NẾU** TTFV > 48h **TRÊN** 3 shop onboarding liên tiếp **VÀ** shop đã tích hợp OA **THÌ** đóng băng chiến dịch quảng cáo kéo shop mới 1 tuần, làm mượt Knowledge Base mẫu **KHÔNG THÌ** không đổ thêm tiền ads để bù drop-off.
2. ⏹ **NẾU** Token Cost / Resolved Ticket > $0.110 **TRONG** 7 ngày **VÀ** volume ≥ 200 tickets **THÌ** dừng tính năng sinh câu tự do, kích hoạt Prompt Caching và cắt context **KHÔNG THÌ** không tăng giá thu phí ticket để che chi phí token.
3. **NẾU** AI Containment Rate < 41% **TRONG** 2 tuần **VÀ** volume ≥ 300 tickets **THÌ** thu hẹp phạm vi bot về 2 flow chuẩn (vận đơn + bảng giá), chuyển ca khó cho shop **KHÔNG THÌ** không mở rộng thử nghiệm ngành mới.
4. **NẾU** 1 shop chiếm > 35% doanh thu **TRONG** 2 tháng liên tiếp **THÌ** dành 80% nguồn lực marketing tháng tới chuyển đổi 5 shop vừa để phân tán **KHÔNG THÌ** không nhận code tính năng riêng lẻ (custom) cho shop lớn.
5. **NẾU** POC-to-Paid < 8% **TRÊN** 10 shop kết thúc 14 ngày trial **THÌ** phỏng vấn 5 shop rời bỏ để viết lại Value Metric và đóng gói $19/tháng kèm 100 ticket **KHÔNG THÌ** không kéo dài trial miễn phí vô thời hạn.

### Cổng gác 90 ngày

| Ngày | Metric (đúng 1) | Ngưỡng qua cổng | Bằng chứng vật lý | Nếu trượt |
|---|---|---|---|---|
| **30** (Học) | Policy Compliance Rate | ≥ 85% trên 100 test case tổng hợp | Log file JSON/CSV kiểm thử Eval có nhãn | **FIX** (tinh chỉnh prompt/KB trong 2 tuần; không scale) |
| **60** (Đòn bẩy) | Time-to-First-Value (TTFV) | 5/5 shop pilot đạt TTFV < 24 giờ | Webhook transaction log trên Zalo OA | **PIVOT** (đơn giản hóa onboarding chỉ còn 1 ngành hàng) |
| **90** (Vận hành) | Paid Customers & GM | ≥ 3 shop trả phí VÀ Gross Margin ≥ 60% | Hóa đơn thanh toán ngân hàng + đối soát API | **KILL** (dừng dự án nếu sau khi FIX vẫn không ai trả tiền) |

**KILL CRITERIA:** Dừng toàn bộ dự án vào ngày **08/01/2027** nếu sau 90 ngày không có ít nhất **3 shop trả phí duy trì Gross Margin ≥ 60%** và **Containment Rate thực tế vẫn dưới 41%**.

**CHƯA ĐO ĐƯỢC:** Tỷ lệ Containment và CSAT trên hội thoại khách hàng thật chưa đo được vì sản phẩm đang ở giai đoạn pre-MVP, đang chờ cấp quyền Zalo Official Account API và chưa ký thỏa thuận dữ liệu với shop pilot. (Cần gì để đo: Hoàn tất kiểm duyệt Zalo OA App và kết nối 5 shop thử nghiệm; Khi nào có số: Ngày 15/11/2026).

