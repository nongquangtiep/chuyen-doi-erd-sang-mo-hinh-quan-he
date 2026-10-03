# [Bài tập] Chuyển đổi ERD sang mô hình quan hệ

## Hướng dẫn thực hiện và các bước phân tích

---

### Bước 1: Xác định các thực thể có trong mô hình ERD
Mô hình ERD bao gồm 5 thực thể chính cùng các thuộc tính tương ứng (thuộc tính gạch chân là khóa chính):

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
   - Thuộc tính đa trị: `SĐT` (hình elip viền kép)

---

### Bước 2: Xác định các mối quan hệ giữa các thực thể để sinh ra các trường và các bảng tương ứng

* **Mối quan hệ 1 - N (Mối quan hệ 4: Cung cấp giữa NHACC và DONDH):**
  - Một nhà cung cấp có thể cung cấp nhiều đơn đặt hàng (1 - N).
  - **Quy tắc:** Thêm khóa chính của bên 1 (`MaNCC` của `NHACC`) làm khóa ngoại (FK) tại bảng bên N (`DONDH`).
  - Bảng `DONDH` nhận thêm cột `MaNCC`.

* **Mối quan hệ N - N (Nhiều - Nhiều):**
  Mỗi mối quan hệ N - N được chuyển đổi thành một bảng liên kết mới. Khóa chính của bảng mới là tổ hợp khóa chính của hai thực thể tham gia và chứa các thuộc tính riêng của mối quan hệ:
  1. **Chi tiết phiếu xuất (Mối quan hệ 1):** Giữa `PHIEUXUAT` (N) và `VATTU` (N).
     - Bảng mới: **CHITIETPHIEUXUAT**
     - Khóa chính kết hợp: `(SoPX, MaVTU)`
     - Thuộc tính riêng: `DGXuat`, `SLXuat`
  2. **Chi tiết phiếu nhập (Mối quan hệ 2):** Giữa `PHIEUNHAP` (N) và `VATTU` (N).
     - Bảng mới: **CHITIETPHIEUNHAP**
     - Khóa chính kết hợp: `(SoPN, MaVTU)`
     - Thuộc tính riêng: `DGNhap`, `SLNhap`
  3. **Chi tiết đơn đặt hàng (Mối quan hệ 3):** Giữa `DONDH` (N) và `VATTU` (N).
     - Bảng mới: **CHITIETDONDH**
     - Khóa chính kết hợp: `(SoDH, MaVTU)`

---

### Bước 3: Xác định các thuộc tính đa trị và tạo thành 1 bảng mới
* Thuộc tính **`SĐT`** của thực thể `NHACC` là thuộc tính đa trị (một nhà cung cấp có thể có nhiều số điện thoại).
* **Quy tắc:** Tách thành bảng mới gồm khóa chính của thực thể cha và chính thuộc tính đa trị đó. Cả hai thuộc tính hợp thành khóa chính của bảng mới.
* Sinh ra bảng: **NHACC_SDT** (`MaNCC`, `SDT`).

---

### Bước 4: Liệt kê lại danh sách các bảng sau khi chuyển đổi xong

*(Quy ước: Thuộc tính gạch chân là Khóa chính - PK, thuộc tính in đậm là Khóa ngoại - FK)*

1. **PHIEUXUAT** (<u>SoPX</u>, NgayXuat)
2. **PHIEUNHAP** (<u>SoPN</u>, NgayNhap)
3. **VATTU** (<u>MaVTU</u>, TenVTU)
4. **NHACC** (<u>MaNCC</u>, TenNCC, DiaChi)
5. **NHACC_SDT** (<u>**MaNCC**</u>, <u>SDT</u>)
   - *Khóa ngoại:* `MaNCC` tham chiếu đến `NHACC(MaNCC)`
6. **DONDH** (<u>SoDH</u>, NgayDH, **MaNCC**)
   - *Khóa ngoại:* `MaNCC` tham chiếu đến `NHACC(MaNCC)`
7. **CHITIETPHIEUXUAT** (<u>**SoPX**</u>, <u>**MaVTU**</u>, DGXuat, SLXuat)
   - *Khóa ngoại:* `SoPX` tham chiếu đến `PHIEUXUAT(SoPX)`
   - *Khóa ngoại:* `MaVTU` tham chiếu đến `VATTU(MaVTU)`
8. **CHITIETPHIEUNHAP** (<u>**SoPN**</u>, <u>**MaVTU**</u>, DGNhap, SLNhap)
   - *Khóa ngoại:* `SoPN` tham chiếu đến `PHIEUNHAP(SoPN)`
   - *Khóa ngoại:* `MaVTU` tham chiếu đến `VATTU(MaVTU)`
9. **CHITIETDONDH** (<u>**SoDH**</u>, <u>**MaVTU**</u>)
   - *Khóa ngoại:* `SoDH` tham chiếu đến `DONDH(SoDH)`
   - *Khóa ngoại:* `MaVTU` tham chiếu đến `VATTU(MaVTU)`
