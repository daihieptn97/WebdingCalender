# 💍 Lịch Cưới Hỏi — Ứng dụng xem ngày & giờ đẹp theo phong thủy Việt Nam

Một ứng dụng web tĩnh **một file HTML duy nhất** giúp các cặp đôi tra cứu ngày cưới hợp phong thủy theo phong tục truyền thống của người Việt. Người dùng nhập **ngày sinh dương lịch** của cô dâu và chú rể, cùng **ngày dự kiến cưới (dương lịch)**, ứng dụng sẽ tự động chuyển sang âm lịch và đánh giá ngày đó qua nhiều phương pháp phong thủy, đồng thời gợi ý các khung giờ hoàng đạo tốt nhất trong ngày.

---

## 🎯 Mục tiêu & phạm vi

- **Đầu vào:** ngày sinh dương lịch của cô dâu, ngày sinh dương lịch của chú rể, ngày cưới dự kiến dương lịch.
- **Đầu ra:** đánh giá tổng hợp ngày cưới (Rất tốt / Tốt / Trung bình / Nên tránh), kèm chi tiết từng tiêu chí và danh sách giờ hoàng đạo highlight giờ đẹp nhất.
- **Deliverable:** một file `index.html` duy nhất, không cần build, không phụ thuộc backend. Toàn bộ logic chạy bằng JavaScript trong trình duyệt.
- **Thư viện duy nhất:** `amlich-hnd.js` (Hồ Ngọc Đức) được nhúng inline để chuyển đổi dương ↔ âm lịch và tính can chi, giờ hoàng đạo.

---

## 🧱 Kiến trúc file

File `index.html` gồm bốn lớp xếp chồng từ dưới lên:

1. **Module âm lịch (Hồ Ngọc Đức)** — nhúng nguyên file `amlich-hnd.js`. Đây là tầng cơ sở, cung cấp:
   - `getLunarDate(dd, mm, yyyy)` → trả về object `LunarDate` chứa ngày/tháng/năm âm và số Julian (`jd`).
   - `getCanChi(lunarDate)` → trả về mảng `[tênNgày, tênTháng, tênNăm]` dưới dạng Can-Chi.
   - `getGioHoangDao(jd)` → trả về chuỗi liệt kê các giờ hoàng đạo theo Chi (Tý, Sửu, ...) kèm khung giờ.
   - `jdn(dd, mm, yyyy)` → số ngày Julian, dùng để tính khoảng cách ngày và truy ra Can-Chi của ngày.
2. **Module phong thủy** — các hàm thuần JavaScript bổ sung, xây dựng trên dữ liệu của module âm lịch (chi tiết ở phần "Thuật toán" bên dưới).
3. **Module đánh giá tổng hợp** — chấm điểm và xếp loại ngày cưới dựa trên kết quả của module phong thủy.
4. **Giao diện** — HTML + CSS thuần, phong cách **hiện đại tối giản, pastel sang trọng** (gam màu kem, hồng phấn, nâu trầm; typography serif cho tiêu đề, sans-serif cho nội dung).

---

## 🧮 Các thuật toán phong thủy được áp dụng

Ứng dụng kết hợp **bốn nhóm tiêu chí** truyền thống. Mỗi tiêu chí cho ra một kết luận riêng; module đánh giá tổng hợp tổng hợp lại thành điểm cuối.

### 1. Tuổi vợ chồng — Lục Hợp, Tam Hợp, Tứ Hành Xung, Lục Hại

Đây là tiêu chí xét **mối quan hệ giữa hai con giáp** của cô dâu và chú rể, dựa trên 12 Địa Chi.

**Cách lấy con giáp:** từ năm dương lịch sinh, chuyển sang năm âm lịch bằng `getLunarDate(...).year`, sau đó tra Chi bằng công thức của file âm lịch:

```
chiIndex = (year + 8) % 12     // 0=Tý, 1=Sửu, ..., 11=Hợi
```

**Các nhóm quan hệ:**

| Nhóm | Mô tả | Ghi chú |
|---|---|---|
| **Tam Hợp** | 4 nhóm 3 con giáp hợp nhau: (Thân-Tý-Thìn), (Dần-Ngọ-Tuất), (Tỵ-Dậu-Sửu), (Hợi-Mão-Mùi) | Rất tốt cho hôn nhân |
| **Lục Hợp** | 6 cặp đôi hợp nhau: Tý-Sửu, Dần-Hợi, Mão-Tuất, Thìn-Dậu, Tỵ-Thân, Ngọ-Mùi | Rất tốt |
| **Tứ Hành Xung** | 3 nhóm 4 con giáp xung khắc: (Dần-Thân-Tỵ-Hợi), (Tý-Ngọ-Mão-Dậu), (Thìn-Tuất-Sửu-Mùi). Trong nhóm, hai con đối nhau (cách 6) là **xung trực diện** (mạnh nhất). | Cần cân nhắc |
| **Lục Hại** | 6 cặp khắc nhau: Tý-Mùi, Sửu-Ngọ, Dần-Tỵ, Mão-Thìn, Thân-Hợi, Dậu-Tuất | Hại tinh thần |

**Hàm xử lý dự kiến:**
```js
function xetTuoiVoChong(chiCoDau, chiChuRe) {
  // Trả về: { tamHop, lucHop, tuHanhXung, lucHai, ketLuan, diem }
}
```

### 2. Cung mệnh — Ngũ hành nạp âm

Mỗi năm âm lịch ứng với một **nạp âm** trong **Lục Thập Hoa Giáp** (60 năm = 60 nạp âm, mỗi nạp âm là một biến thể cụ thể của ngũ hành Kim/Mộc/Thủy/Hỏa/Thổ). Ví dụ năm Giáp Tý nạp âm là Hải Trung Kim (Kim biển), năm Bính Tuất nạp âm Ốc Thượng Thổ (Đất nóc nhà), v.v.

**Quy luật tính nạp âm (theo bảng Lục Thập Hoa Giáp truyền thống):**

Mỗi cặp năm liền nhau (1 Dương + 1 Âm) có cùng một hành. Cứ 3 cặp (6 năm) thì đổi sang hành mới. Thứ tự lặp lại: **Kim → Hỏa → Mộc → Thủy → Thổ → Kim → ...** trong mỗi vòng 30 năm.

Trong ứng dụng, ta dùng một **bảng tra cứu 60 phần tử** (hardcoded) ánh xạ `(canIndex * 12 + chiIndex) % 60` → một trong 5 hành. Cách này đơn giản, chính xác, và là chuẩn của tất cả các trang tử vi online tại Việt Nam.

**Đánh giá quan hệ ngũ hành giữa hai mệnh:**

| Quan hệ | Ý nghĩa | Quy luật |
|---|---|---|
| **Tương sinh** | Hành này sinh hành kia (rất tốt) | Kim sinh Thủy, Thủy sinh Mộc, Mộc sinh Hỏa, Hỏa sinh Thổ, Thổ sinh Kim |
| **Tương hòa** | Hai hành giống nhau (tốt vừa) | Kim-Kim, Mộc-Mộc... |
| **Tương khắc** | Hành này khắc hành kia (xấu) | Kim khắc Mộc, Mộc khắc Thổ, Thổ khắc Thủy, Thủy khắc Hỏa, Hỏa khắc Kim |

**Hàm xử lý:**
```js
function xetCungMenh(namCoDau, namChuRe) {
  // Trả về: { menhCoDau, menhChuRe, quanHe ('sinh'|'hoa'|'khac'), diem }
}
```

### 3. Năm cưới — Kim Lâu, Hoang Ốc, Tam Tai (xét theo cô dâu)

Theo phong tục Việt, **ba đại kỵ năm cưới** được tính theo **cô dâu** (vì cô dâu là người về nhà chồng):

#### a) Kim Lâu

> "1, 3, 6, 8 Kim Lâu — dựng nhà lấy vợ tậu trâu thì đừng"

Lấy tuổi mụ cô dâu vào năm cưới (= tuổi tây + 1, hoặc chính xác hơn: `nam_cuoi - nam_sinh + 1`), chia 9 lấy số dư.

```
tuoiMu = namCuoi - namSinhCoDau + 1
duKimLau = tuoiMu % 9
```

- Dư **1 → Kim Lâu Thân** (hại bản thân cô dâu)
- Dư **3 → Kim Lâu Thê** (hại vợ chồng)
- Dư **6 → Kim Lâu Tử** (hại con cái)
- Dư **8 → Kim Lâu Lục Súc** (hại gia súc, vật nuôi, tài sản)
- Các giá trị còn lại (0, 2, 4, 5, 7) → **không phạm**

**Hóa giải dân gian:** "xin dâu hai lần" (làm lễ rước dâu hai lần riêng biệt).

#### b) Hoang Ốc

Cũng tính theo tuổi mụ, chia 6 lấy số dư để rơi vào 1 trong 6 cung:

```
duHoangOc = tuoiMu % 6   // (lưu ý: thực tế các trang phong thủy dùng vòng tra theo bảng tuổi 10–60)
```

| Cung | Ý nghĩa |
|---|---|
| 1 — Nhất Cát | Cát (tốt) |
| 2 — Nhì Nghi | Cát (tốt) |
| 3 — Tam Địa Sát | **Hung (xấu)** |
| 4 — Tứ Tấn Tài | Cát (tốt) |
| 5 — Ngũ Thọ Tử | **Hung (xấu)** |
| 6 — Lục Hoang Ốc | **Hung (xấu)** |

Ứng dụng dùng bảng tra cứu trực tiếp theo tuổi mụ (10 → Nhất Cát, 11 → Nhì Nghi, ...).

#### c) Tam Tai

Tam Tai là 3 năm liên tiếp xấu cho mỗi nhóm tam hợp:

| Nhóm tuổi (Tam hợp) | 3 năm Tam Tai |
|---|---|
| Thân – Tý – Thìn | Dần, Mão, Thìn |
| Dần – Ngọ – Tuất | Thân, Dậu, Tuất |
| Tỵ – Dậu – Sửu | Hợi, Tý, Sửu |
| Hợi – Mão – Mùi | Tỵ, Ngọ, Mùi |

→ Lấy Chi của năm sinh cô dâu, tra ra nhóm tam hợp, đối chiếu Chi của năm cưới với 3 năm Tam Tai của nhóm đó.

### 4. Ngày Hoàng Đạo / Hắc Đạo

Trong 12 con giáp ứng với Chi của ngày, có 6 chi là **Hoàng Đạo** (ngày được các vị thần thiện soi xét, thuận lợi), 6 chi là **Hắc Đạo** (ngày các thần ác cai quản, kiêng kỵ).

Cách xác định **phụ thuộc vào tháng âm lịch** (tính theo nguyên lý "tháng kiến"). Tóm tắt:

| Tháng âm | Ngày Hoàng Đạo (Chi) |
|---|---|
| 1, 7 | Tý, Sửu, Tỵ, Mùi |
| 2, 8 | Dần, Mão, Mùi, Dậu |
| 3, 9 | Thìn, Tỵ, Dậu, Hợi |
| 4, 10 | Ngọ, Mùi, Sửu, Dậu (...) |
| 5, 11 | Thân, Dậu, Sửu, Mão |
| 6, 12 | Tuất, Hợi, Mão, Tỵ |

> Bảng trên là rút gọn; ứng dụng sẽ hardcode đầy đủ bảng tra cứu 12 tháng theo nguồn tham khảo cổ.

### 5. Giờ Hoàng Đạo trong ngày cưới

Đây là phần **thuận tay nhất** vì file `amlich-hnd.js` đã có sẵn hàm `getGioHoangDao(jd)`.

- Mỗi ngày có **6 giờ hoàng đạo** (mỗi giờ = 2 tiếng đồng hồ thực tế).
- Cách tính: dựa trên Chi của ngày, tra vào mảng `GIO_HD` (đã có trong file âm lịch) — chuỗi bit 12 ký tự, ký tự '1' = giờ hoàng đạo.
- Ứng dụng sẽ **liệt kê đầy đủ 6 giờ** kèm khung giờ thực tế (ví dụ "Ngọ (11h–13h)"), và **highlight giờ đẹp nhất** — chọn theo quy tắc:

> **Giờ đẹp nhất = giờ hoàng đạo có Chi tam hợp hoặc lục hợp với Chi của ngày cưới và Chi tuổi cô dâu.**

Nếu không có giờ nào thỏa cả hai, ưu tiên: (1) hợp với ngày cưới → (2) hợp với cô dâu → (3) giờ ban ngày (6h–18h) thay vì ban đêm.

---

## 🧪 Module đánh giá tổng hợp

Mỗi tiêu chí cho một điểm. Tổng điểm xếp loại:

| Tiêu chí | Tốt nhất | Trung tính | Xấu nhất |
|---|---|---|---|
| Tuổi vợ chồng (Lục/Tam Hợp ↔ Xung/Hại) | +3 | 0 | −3 |
| Cung mệnh ngũ hành (Sinh ↔ Khắc) | +2 | +1 (hòa) | −2 |
| Kim Lâu | 0 | — | −3 (phạm) |
| Hoang Ốc | +1 | 0 | −2 (phạm) |
| Tam Tai | 0 | — | −3 (phạm) |
| Ngày Hoàng Đạo | +2 | — | −2 (Hắc Đạo) |

**Xếp loại:**
- ≥ 7 điểm → **Đại cát** (rất nên cưới)
- 4–6 → **Cát** (tốt)
- 1–3 → **Trung bình** (cân nhắc)
- ≤ 0 → **Hung** (nên chọn ngày khác, hoặc áp dụng hóa giải dân gian)

Hệ thống cũng hiển thị **gợi ý hóa giải** khi phạm Kim Lâu (xin dâu hai lần), Tam Tai (mượn tuổi), v.v.

---

## 🎨 Giao diện

Phong cách **hiện đại tối giản, pastel sang trọng**:

- **Bảng màu:** kem `#FAF6F2`, hồng phấn `#E8C5C5`, nâu trầm `#5C4033`, vàng kim nhẹ `#D4AF7A` cho điểm nhấn.
- **Typography:** `Playfair Display` (tiêu đề) + `Inter` (nội dung) — load từ Google Fonts.
- **Layout:** 1 cột, max-width 720px, căn giữa, nhiều khoảng trắng.
- **Component:**
  - Form nhập ở trên (3 trường date).
  - Card kết quả tổng (xếp loại + điểm tổng + 1 dòng nhận định).
  - Accordion / Section cho từng tiêu chí chi tiết.
  - Bảng giờ hoàng đạo cuối cùng, với hàng "giờ đẹp nhất" được highlight bằng nền vàng kim nhạt.
- **Không có hiệu ứng phức tạp**, chỉ fade-in nhẹ khi hiển thị kết quả.

---

## 📁 Cấu trúc file

```
index.html
├── <style>             — CSS pastel
├── <body>              — form + container kết quả
└── <script>
    ├── [Phần 1] amlich-hnd.js (nhúng nguyên xi)
    ├── [Phần 2] bảng tra cứu phong thủy
    │   ├── NAP_AM_60[]          // 60 hành nạp âm Lục Thập Hoa Giáp
    │   ├── HOANG_DAO_THEO_THANG // bảng Hoàng/Hắc Đạo
    │   ├── TAM_HOP[], LUC_HOP[], TU_HANH_XUNG[], LUC_HAI[]
    │   └── KIM_LAU_MAP, HOANG_OC_MAP, TAM_TAI_MAP
    ├── [Phần 3] hàm phong thủy
    │   ├── getChi(year), getCan(year), getMenh(year)
    │   ├── xetTuoiVoChong(), xetCungMenh()
    │   ├── xetKimLau(), xetHoangOc(), xetTamTai()
    │   ├── xetNgayHoangDao(lunar), xetGioHoangDao(jd)
    │   └── chamDiemTongHop(...)
    └── [Phần 4] UI logic
        ├── parse form
        ├── gọi các hàm phong thủy
        └── render kết quả vào DOM
```

---

## ⚠️ Lưu ý & disclaimers

- Ứng dụng dựa trên **kinh nghiệm dân gian Việt Nam**, không phải khoa học chính xác. Mang tính tham khảo, giúp tinh thần thoải mái khi chuẩn bị hôn lễ.
- Các tiêu chí phong thủy có **nhiều trường phái khác nhau** — ứng dụng dùng phổ biến nhất (xuất hiện trên các trang lịch vạn niên chính thống).
- Phạm vi năm hỗ trợ: **1900–2099** (giới hạn của bảng `TK20`/`TK21` trong `amlich-hnd.js`).
- Múi giờ: **UTC+7** (Việt Nam).

---

## 🚀 Cách chạy

Mở file `index.html` trực tiếp trong trình duyệt — xong. Không cần server, không cần internet (trừ phần load font Google nếu muốn typography đầy đủ; có fallback sans-serif/serif hệ thống nếu offline).

---

## 📚 Nguồn tham khảo

- `amlich-hnd.js` © Hồ Ngọc Đức (2004) — thuật toán âm lịch Việt Nam.
- Lục Thập Hoa Giáp & Ngũ hành nạp âm — sách *Thông Thư* và các trang tử vi truyền thống.
- Kim Lâu, Hoang Ốc, Tam Tai — tổng hợp từ *Thông Thư* và các nguồn phong thủy dân gian Việt Nam.
- 12 Trực và Ngày Hoàng Đạo / Hắc Đạo — *Lịch Vạn Sự*.