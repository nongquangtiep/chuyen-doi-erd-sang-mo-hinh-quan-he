# [Bài tập] Chuyển đổi ERD sang mô hình quan hệ

## Báo cáo chi tiết các bước chuyển đổi

---

### Bước 1: Xác định các thực thể có trong mô hình ERD
Từ sơ đồ ERD đã cho, hệ thống bao gồm 5 thực thể chính kèm theo các thuộc tính (thuộc tính gạch chân là khóa chính):

1. **PHIEUXUAT** (Phiếu xuất):
   - Thuộc tính khóa: `SoPX`
   - Thuộc tính mô tả: `NgayXuat`
2. **PHIEUNHAP** (Phiếu nhập):
   - Thuộc tính khóa: `SoPN`
   - Thuộc tính mô tả: `NgayNhap`
3. **VATTU** (Vật tư):
   - Thuộc tính khóa: `MaVTU`
   - Thuộc tính mô tả: `TenVTU`
4. **DONDH** (Đơn đặt hàng):
   - Thuộc tính khóa: `SoDH`
   - Thuộc tính mô tả: `NgayDH`
5. **NHACC** (Nhà cung cấp):
   - Thuộc tính khóa: `MaNCC`
   - Thuộc tính mô tả: `TenNCC`, `DiaChi`
   - Thuộc tính đa trị: `SĐT` (được thể hiện bằng hình elip nét đôi)

---

### Bước 2: Xác định các mối quan hệ 1 - 1, 1 - n, n - m giữa các thực thể để sinh ra các trường, các bảng tương ứng

* **Mối quan hệ 1 - N (Mối quan hệ 4: Cung cấp giữa NHACC và DONDH):**
  - Bản số: 1 Nhà cung cấp cung cấp Nhiều (N) Đơn đặt hàng.
  - Quy tắc chuyển đổi: Đưa khóa chính của thực thể bên 1 (`MaNCC` của `NHACC`) sang làm khóa ngoại (FK) trong bảng bên N (`DONDH`).
  - Kết quả: Bảng `DONDH` được bổ sung thêm trường `MaNCC`.

* **Mối quan hệ N - M (Nhiều - Nhiều):**
  Mỗi mối quan hệ N - M chuyển đổi thành một bảng liên kết độc lập. Khóa chính của bảng mới là tổ hợp khóa chính của hai thực thể tham gia kèm theo các thuộc tính riêng của mối quan hệ:
  1. **Mối quan hệ 1 (Chi tiết phiếu xuất):** Quan hệ N - M giữa `PHIEUXUAT` và `VATTU`.
     - Sinh ra bảng mới: **CHITIETPHIEUXUAT**
     - Khóa chính kết hợp: `(SoPX, MaVTU)`
     - Thuộc tính riêng: `DGXuat` (Đơn giá xuất), `SLXuat` (Số lượng xuất)
  2. **Mối quan hệ 2 (Chi tiết phiếu nhập):** Quan hệ N - M giữa `PHIEUNHAP` và `VATTU`.
     - Sinh ra bảng mới: **CHITIETPHIEUNHAP**
     - Khóa chính kết hợp: `(SoPN, MaVTU)`
     - Thuộc tính riêng: `DGNhap` (Đơn giá nhập), `SLNhap` (Số lượng nhập)
  3. **Mối quan hệ 3 (Chi tiết đơn đặt hàng):** Quan hệ N - M giữa `DONDH` và `VATTU`.
     - Sinh ra bảng mới: **CHITIETDONDH**
     - Khóa chính kết hợp: `(SoDH, MaVTU)`

---

### Bước 3: Xác định các thuộc tính đa trị và tạo thành 1 bảng mới
- Trong thực thể `NHACC`, thuộc tính **`SĐT`** (Số điện thoại) là thuộc tính đa trị vì một nhà cung cấp có thể có nhiều số liên lạc.
- Quy tắc chuyển đổi: Tách thuộc tính đa trị ra thành một bảng mới gồm: khóa chính của thực thể gốc và chính thuộc tính đa trị đó. Cả hai cột hợp thành khóa chính của bảng mới.
- Kết quả tạo bảng mới: **NHACC_SDT** (`MaNCC`, `SDT`). Khóa ngoại `MaNCC` tham chiếu về `NHACC(MaNCC)`.

---

### Bước 4: Liệt kê lại danh sách các bảng sau khi chuyển đổi xong

*(Quy ước: Cột gạch chân là Khóa chính - PK; cột ghi chú FK là Khóa ngoại tham chiếu)*

1. **PHIEUXUAT** (<u>SoPX</u>, NgayXuat)
2. **PHIEUNHAP** (<u>SoPN</u>, NgayNhap)
3. **VATTU** (<u>MaVTU</u>, TenVTU)
4. **NHACC** (<u>MaNCC</u>, TenNCC, DiaChi)
5. **NHACC_SDT** (<u>MaNCC</u>, <u>SDT</u>)
   - *Khóa ngoại (FK):* `MaNCC` tham chiếu đến `NHACC(MaNCC)`
6. **DONDH** (<u>SoDH</u>, NgayDH, MaNCC)
   - *Khóa ngoại (FK):* `MaNCC` tham chiếu đến `NHACC(MaNCC)`
7. **CHITIETPHIEUXUAT** (<u>SoPX</u>, <u>MaVTU</u>, DGXuat, SLXuat)
   - *Khóa ngoại (FK):* `SoPX` tham chiếu đến `PHIEUXUAT(SoPX)`
   - *Khóa ngoại (FK):* `MaVTU` tham chiếu đến `VATTU(MaVTU)`
8. **CHITIETPHIEUNHAP** (<u>SoPN</u>, <u>MaVTU</u>, DGNhap, SLNhap)
   - *Khóa ngoại (FK):* `SoPN` tham chiếu đến `PHIEUNHAP(SoPN)`
   - *Khóa ngoại (FK):* `MaVTU` tham chiếu đến `VATTU(MaVTU)`
9. **CHITIETDONDH** (<u>SoDH</u>, <u>MaVTU</u>)
   - *Khóa ngoại (FK):* `SoDH` tham chiếu đến `DONDH(SoDH)`
   - *Khóa ngoại (FK):* `MaVTU` tham chiếu đến `VATTU(MaVTU)`
