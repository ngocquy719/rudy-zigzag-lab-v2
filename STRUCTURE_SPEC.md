# CẤU-TRÚC SMC — NGUYÊN-TẮC CHUẨN (research 23/10, nguồn: joshyattridge/smart-money-concepts + LuxAlgo)

## TẠI SAO BẢN CŨ (#43-52) SAI
- Em dùng **fractal 1-nến** (đỉnh/đáy chỉ cần cao/thấp hơn 1 nến 2 bên) → NHIỄU, sinh hàng trăm swing rác.
- Em dùng **máy trạng-thái emit** (bias flip, swHigh/swLow, pushSw) → whipsaw, 2-nét-mờ, CHoCH-giả, cascade khi lọc.
- Càng vá càng lòi lỗi vì **nền sai**.

## NGUYÊN-TẮC CHUẨN (2 bước, KHÔNG máy-trạng-thái)

### BƯỚC 1 — SWING POINTS = PIVOT CÓ LOOKBACK N
- Swing high = `high` CAO NHẤT trong **N nến TRƯỚC và N nến SAU** (mặc định N=50). Swing low tương tự với `low` thấp nhất.
- **N = độ mịn** (lookback lớn = SWING/vĩ-mô; nhỏ = INTERNAL/vi-mô). Đây là 2 TẦNG internal vs swing — em đang lẫn.
- Lọc 2 swing CÙNG LOẠI liên tiếp: giữ cái cực-hơn (= mergeAlt). Ép swing đầu/cuối so-le.

### BƯỚC 2 — BOS/CHoCH = KHỚP MẪU 4 SWING CUỐI (declarative, KHÔNG bias-machine)
Trượt cửa sổ 4 swing liên tiếp `[s4, s3, s2, s1]` (s1 mới nhất). Level = `s3` (swing bị phá). Đặt nhãn tại s2.

| Nhãn | Mẫu loại | Điều-kiện MỨC GIÁ |
|---|---|---|
| **BOS tăng** | L,H,L,H | s4 < s2 < s3 < s1 → 2 đáy TĂNG (HL) & 2 đỉnh TĂNG (HH) = uptrend sạch |
| **BOS giảm** | H,L,H,L | s4 > s2 > s3 > s1 → 2 đỉnh GIẢM (LH) & 2 đáy GIẢM (LL) = downtrend sạch |
| **CHoCH tăng** | L,H,L,H | s1 > s3 > s4 > s2 → đáy MỚI thấp hơn (LL) RỒI đỉnh mới phá lên (HH) = đảo lên |
| **CHoCH giảm** | H,L,H,L | s1 < s3 < s4 < s2 → đỉnh MỚI cao hơn (HH) RỒI đáy mới thủng (LL) = đảo xuống |

### BƯỚC 3 — XÁC NHẬN PHÁ VỠ bằng ĐÓNG NẾN (close_break=True)
- Từ nến `i+2` trở đi, tìm nến đầu tiên **close vượt level** (lên: close>level; xuống: close<level). Đó là cây phá vỡ.
- Nếu không có cây nào phá → **HUỶ** nhãn đó (chưa phá thì không vẽ — ĐÚNG luật Rudy).
- Nhãn chồng-lấn chưa-phá mà bị nhãn sau "trùm" → xoá.

## VÌ SAO CÁCH NÀY TỰ ĐÚNG MỌI LUẬT RUDY (không cần vá)
- **Luật close-vượt-râu** = `close_break=True` dùng close so với level(=râu swing). ✓
- **Luật theo xu-hướng** (giảm tìm thấp hơn / tăng cao hơn) = điều-kiện MỨC GIÁ đơn-điệu của BOS. ✓
- **4 trường-hợp hợp-lệ + không 2-mờ + không CHoCH-giả** = 4 mẫu khớp CHÍNH XÁC, declarative nên KHÔNG whipsaw/cascade. ✓
- **Không vẽ swing vô-hiệu (K/L)** = swing chỉ sinh từ pivot-N, không emit giữa chừng. ✓

## KẾ-HOẠCH DỰNG LẠI
1. Thay `baseZZ`(1-nến) → `swingHL(cs, N)` pivot-lookback + mergeAlt + ép biên.
2. Thay `structureBOS`(emit-machine) → `labelBOSCHoCH(swings, cs)` khớp-4-mẫu + xác-nhận-close.
3. Slider "Cấu trúc" = **N (lookback)** thay vì degree: N nhỏ=internal, lớn=swing.
4. Bỏ hết `alternateStruct/clean1pass/pushSw` (vá hậu-kỳ) — không cần nữa.
5. Giữ: màu đậm/mờ (HH/LL=đậm, HL/LH=mờ), sửa tay (Ctrl/Shift/chuột-phải), nhận-kèo.
