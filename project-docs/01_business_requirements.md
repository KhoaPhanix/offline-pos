# 01. Business Requirements — Offline-First Inventory + POS (Small Business)

## 1. Mục tiêu
- Phát triển phần mềm quản lý **kho** (ưu tiên PC) và **bán hàng** (ưu tiên mobile) cho hộ kinh doanh nhỏ (tạp hoá, cửa hàng nhỏ).
- Hỗ trợ vận hành **ngoại tuyến (Offline-First)**: mất mạng vẫn tạo phiếu/đơn, có mạng lại thì đồng bộ.
- Chạy **nội bộ tại cửa hàng (LAN)**, không yêu cầu truy cập từ xa.
- Quản lý:
  - **Nhiều kho** (multi-warehouse)
  - **Đơn vị tính (UoM)** có quy đổi về đơn vị cơ sở
  - **Lô/hạn dùng**; bán hàng hỗ trợ **FEFO**
- Hạn dùng: **chỉ cảnh báo sắp hết hạn**, **không chặn bán**.
- **Cho phép bán âm kho**.

## 2. Đối tượng sử dụng
- **Chủ cửa hàng**: quản lý sản phẩm/kho, nhập/chuyển kho, theo dõi tồn và hàng sắp hết hạn.
- **Nhân viên bán hàng**: tạo đơn bán trên mobile.

## 3. Phạm vi cửa hàng
- Mô hình: hộ kinh doanh nhỏ (tạp hoá).
- Số kho: nhiều kho (ví dụ Kho chính + Kho quầy).
- Môi trường vận hành: nội bộ cửa hàng, các thiết bị dùng chung Wi-Fi/LAN.
- CouchDB tự host trên PC/miniPC nội bộ — không sử dụng VPS hoặc dịch vụ cloud trả phí.

## 4. Nghiệp vụ bắt buộc (In scope)

### 4.1 Sản phẩm
- CRUD sản phẩm.
- Barcode (có thể theo UoM).
- Đơn vị tính (UoM) + quy đổi về `baseUom`.

### 4.2 Kho
- CRUD kho.
- Xem tồn theo kho.
- Xem tồn theo lô (batch) trong kho (phục vụ FEFO + cảnh báo).

### 4.3 Nhập kho (purchase) — PC/PWA
- Tạo phiếu nhập.
- Bắt buộc quản lý lô/hạn dùng: **Có**.
- Thông tin lô tối thiểu: `lotCode`, `expDate` (có thể có `mfgDate`).

### 4.4 Bán hàng (sale) — Mobile POS
- Tạo đơn bán **offline**.
- Auto-pick lô theo **FEFO** (hết hạn trước, bán trước): **Có**.
- Cho người dùng **sửa/đổi batch** đã auto-pick: **Có**.
- Chỉ **cảnh báo sắp hết hạn** (trong ngưỡng `expiryWarnDays` = 180 ngày), **không chặn bán**: **Có**.
- **Cho phép bán âm kho**: **Có**.

### 4.5 Chuyển kho (transfer) — PC/PWA
- Chuyển hàng giữa các kho: **Có**.
- Mỗi dòng chuyển kho **bắt buộc khai báo `batchId`** (giữ truy xuất nguồn gốc và hạn dùng xuyên kho): **Có**.

### 4.6 Đồng bộ (Sync)
- PC/PWA và Mobile đồng bộ dữ liệu 2 chiều khi có mạng LAN nội bộ.

### 4.7 Báo cáo tối thiểu
- Tồn kho theo kho.
- Tồn theo lô/batch trong kho.
- Danh sách batch/hàng **sắp hết hạn** theo kho (≤ 180 ngày).
- Hiển thị cảnh báo **âm kho** khi tồn < 0.

## 5. Nghiệp vụ tuỳ chọn (chưa chốt / có thể làm giai đoạn sau)
- Đổi/trả hàng.
- Quản lý khách hàng.
- In hoá đơn.

## 6. Ghi chú
- Vì cho phép bán âm kho, hệ thống cần hiển thị cảnh báo "Âm kho" ở màn hình tồn theo kho và theo batch để chủ shop xử lý nhập bù/điều chỉnh sau.
