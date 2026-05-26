# Thuật Toán Kiểm Tra Ngày Cưới Cát Tường (Wedding Date Selection Algorithm)

Tài liệu này cung cấp công thức tổng quát và cấu trúc thuật toán logic để tự động hóa việc kiểm tra, đánh giá một ngày Dương lịch bất kỳ có phải là ngày tốt (Cát) hay xấu (Hung) đối với một cặp đôi (dựa trên thông tin ngày sinh của Cô dâu và Chú rể).

---

## 1. Dữ liệu Đầu vào (Inputs)

Để thuật toán hoạt động chính xác, cần cung cấp các thông tin sau:
* **Cô dâu (Bride):** Ngày, tháng, năm sinh (Dương lịch).
* **Chú rể (Groom):** Ngày, tháng, năm sinh (Dương lịch).
* **Ngày cưới dự kiến (Wedding Date):** Ngày, tháng, năm dự kiến tổ chức (Dương lịch).

---

## 2. Các Bước Xử Lý Thuật Toán (Core Pipeline)

Thuật toán tuân theo quy trình 5 bước nghiêm ngặt dưới đây:

    [Nhập Input] -> [Bước 1: Chuyển đổi Âm lịch] -> [Bước 2: Kiểm tra Kim Lâu] 
                 -> [Bước 3: Lọc ngày Đại kỵ]   -> [Bước 4: Xét Hợp/Xung Chi] 
                 -> [Bước 5: Xét Ngũ Hành]      -> [Kết luận & Điểm số]

### Bước 1: Chuẩn hóa và Chuyển đổi Lịch (Calendar Conversion)
* Chuyển đổi ngày sinh Cô dâu, Chú rể và Ngày cưới dự kiến từ **Dương lịch sang Âm lịch**.
* Trích xuất các thành phần Can Chi và Ngũ Hành:
    * Năm/Tháng/Ngày/Giờ sinh theo Can Chi (ví dụ: Kỷ Mão, Đinh Sửu...).
    * Bản mệnh Ngũ Hành (ví dụ: Thành Đầu Thổ, Giản Hạ Thủy...).

### Bước 2: Kiểm tra Tuổi Kim Lâu của Cô dâu (Primary Filter)
Theo nguyên lý cổ dịch: *"Lấy vợ xem tuổi đàn bà"*. Đây là bộ lọc quyết định (Gateway) cho năm tổ chức.
* **Công thức tính Tuổi Mụ:** `Tuổi Mụ = Năm Cưới (Âm lịch) - Năm Sinh Cô dâu (Âm lịch) + 1`
* **Phép toán kiểm tra:**
    `Số dư = Tuổi Mụ % 9`
* **Quy tắc:**
    * Nếu `Số dư` thuộc `{1, 3, 6, 8}` -> **Phạm Kim Lâu** (Xấu -> Cảnh báo hoặc Loại trừ năm đó).
    * Nếu `Số dư` KHÔNG thuộc `{1, 3, 6, 8}` -> **Không phạm Kim Lâu** (Tốt -> Tiếp tục bước tiếp theo).
    * *Ví dụ thực tế:* Cô dâu 1999 cưới năm 2023 -> Tuổi mụ = 2023 - 1999 + 1 = 25. Khớp toán: 25 / 9 = 2 dư 7. Không phạm Kim Lâu.

### Bước 3: Lọc bỏ các ngày Đại kỵ hệ thống (Systemic Blacklist Days)
Kiểm tra xem Ngày cưới dự kiến (tính theo ngày Âm lịch) có trùng vào các ngày xấu bất biến hay không:
* **Ngày Tam Nương:** Mùng 3, 7, 13, 18, 22, 27 (Âm lịch hằng tháng).
* **Ngày Nguyệt Kỵ:** Mùng 5, 14, 23 (Âm lịch hằng tháng).
* **Ngày Sát Chủ / Thọ Tử:** Xác định dựa trên mối quan hệ giữa Tháng Âm lịch và Địa chi của Ngày cưới.
* *Quy tắc:* Nếu phạm vào các ngày này -> Trừ điểm nặng hoặc đánh dấu loại bỏ.

### Bước 4: Xét tương hợp Địa Chi (Earthly Branches Relationship)
So sánh **Địa chi của Ngày cưới** với **Địa chi năm sinh của Cô dâu** (và bổ trợ Chú rể):
* **Nhóm Cát (Cộng điểm):**
    * **Lục Hợp:** (Tý - Sửu), (Dần - Hợi), (Mão - Tuất), (Thìn - Dậu), (Tỵ - Thân), (Ngọ - Mùi).
    * **Tam Hợp:** (Thân - Tý - Thìn), (Tỵ - Dậu - Sửu), (Dần - Ngọ - Tuất), (Hợi - Mão - Mùi).
* **Nhóm Hung (Trừ điểm/Cấm kỵ):**
    * **Hình/Hại/Phá:** Tự hình, Tương hại (ví dụ: Mão - Thìn hại nhau).
    * **Chính Xung (Tứ hành xung trực diện):** Tý - Ngọ, Mão - Dậu, Dần - Thân, Tỵ - Hợi, Thìn - Tuất, Sửu - Mùi.
    * *Ví dụ thực tế:* Ngày cưới là ngày Tuất, cô dâu tuổi Mão -> Gặp cặp **Lục Hợp (Mão - Tuất)** -> Đại Cát.

### Bước 5: Xét tương sinh Ngũ Hành (Five Elements Balance)
So sánh **Mệnh Ngũ Hành của Ngày cưới** với **Mệnh Ngũ Hành của Cô dâu** (ưu tiên) và Chú rể:
* **Tương Sinh (Tốt nhất - Thang điểm tối đa):** Ngày sinh cho Người (Sinh nhập) hoặc Người sinh cho Ngày (Sinh xuất - chấp nhận được).
* **Tương Hòa (Rất tốt - Điểm cao):** Ngày và Người cùng mệnh (Thổ - Thổ, Kim - Kim...).
* **Tương Khắc (Xấu - Trừ điểm):** Ngày khắc Người (Khắc nhập - Rất xấu, tổn hại bản mệnh) hoặc Người khắc Ngày (Khắc xuất - Hao tổn năng lượng).

---

## 3. Hàm Logic Thuật Toán (Pseudo-code)

Dưới đây là cấu trúc mã giả logic mô tả cách lập trình thuật toán này:

```python
def danh_gia_ngay_cuoi(ngay_sinh_co_dau, ngay_sinh_chu_re, ngay_cuoi_du_kien):
    # Bước 1: Chuyển đổi sang âm lịch
    am_lich_co_dau = chuyen_duong_sang_am(ngay_sinh_co_dau)
    am_lich_chu_re = chuyen_duong_sang_am(ngay_sinh_chu_re)
    am_lich_cuoi = chuyen_duong_sang_am(ngay_cuoi_du_kien)
    
    diem_so = 100 # Điểm gốc ban đầu
    
    # Bước 2: Kiểm tra Kim Lâu
    tuoi_mu_co_dau = am_lich_cuoi.nam - am_lich_co_dau.nam + 1
    if tuoi_mu_co_dau % 9 in [1, 3, 6, 8]:
        return "XẤU (Cô dâu phạm Kim Lâu)"
        
    # Bước 3: Lọc ngày đại kỵ
    if am_lich_cuoi.ngay in [3, 7, 13, 18, 22, 27]:
        diem_so -= 40  # Phạm Tam Nương
    if am_lich_cuoi.ngay in [5, 14, 23]:
        diem_so -= 40  # Phạm Nguyệt Kỵ
        
    # Bước 4: Xét tương hợp Địa Chi
    if la_luc_hop(am_lich_cuoi.chi_ngay, am_lich_co_dau.chi_nam):
        diem_so += 20  # Lục hợp với cô dâu
    elif la_chinh_xung(am_lich_cuoi.chi_ngay, am_lich_co_dau.chi_nam):
        diem_so -= 30  # Chính xung với cô dâu
        
    # Bước 5: Xét tương sinh Ngũ Hành
    moi_quan_he_muyen = xet_ngu_hanh(am_lich_cuoi.ngu_hanh, am_lich_co_dau.ngu_hanh)
    if moi_quan_he_muyen == "Tuong_Hoa" or moi_quan_he_muyen == "Tuong_Sinh":
        diem_so += 15
    elif moi_quan_he_muyen == "Tuong_Khac_Nhap":
        diem_so -= 25

    # Kết luận dựa trên tổng điểm
    if diem_so >= 80:
        return f"NGÀY ĐẸP (CÁT TƯỜNG) - Điểm: {diem_so}/100"
    elif diem_so >= 50:
        return f"NGÀY BÌNH THƯỜNG - Điểm: {diem_so}/100"
    else:
        return f"NGÀY XẤU (HUNG) - Điểm: {diem_so}/100"