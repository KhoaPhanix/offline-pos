# 04. User Flows / Use Cases

## 1. POS Mobile — Sale (Offline + FEFO)

```
Nhân viên mở app POS (Mobile)
  └─ 1. Chọn kho xuất (mặc định: Kho quầy)
  └─ 2. Quét barcode hoặc tìm kiếm sản phẩm
  └─ 3. Nhập qty + UoM → app tính qtyBase = qty × factorToBase
  └─ 4. App tính tồn theo batch trong kho, auto-pick FEFO
          (tách thành nhiều dòng nếu 1 batch không đủ)
  └─ 5. Nếu batch trong ngưỡng expiryWarnDays (≤ 180 ngày) → hiển thị cảnh báo
          (không chặn bán)
  └─ 6. Nếu tồn không đủ → vẫn cho tạo đơn, hiển thị cảnh báo âm kho
  └─ 7. Cho phép người dùng sửa/đổi batch đã auto-pick
  └─ 8. Thanh toán (tiền mặt) → lưu tx sale vào PouchDB local
  └─ 9. Khi có mạng LAN → PouchDB tự động sync lên CouchDB
```

## 2. PC/PWA — Purchase (Nhập kho có batch/expiry)

```
Chủ cửa hàng mở app Inventory (PWA)
  └─ 1. Chọn kho nhập (ví dụ: Kho chính)
  └─ 2. Chọn sản phẩm (barcode hoặc tìm kiếm)
  └─ 3. Nhập qty + UoM → tính qtyBase
  └─ 4. Nhập lotCode + expDate (bắt buộc); mfgDate (tuỳ chọn)
  └─ 5. Tạo batch mới nếu chưa tồn tại
  └─ 6. Lưu tx purchase vào PouchDB → sync lên CouchDB
```

## 3. PC/PWA — Transfer (Chuyển kho bắt buộc theo batch)

```
Chủ cửa hàng
  └─ 1. Chọn kho nguồn + kho đích
  └─ 2. Chọn sản phẩm
  └─ 3. Chọn batch (bắt buộc) từ danh sách FEFO của kho nguồn
  └─ 4. Nhập qty + UoM → tính qtyBase
  └─ 5. Kiểm tra tồn batch tại kho nguồn (cảnh báo nếu âm, vẫn cho tạo)
  └─ 6. Lưu tx transfer (fromWarehouseId, toWarehouseId, lines với batchId)
  └─ 7. Sync
```

## 4. PC/PWA — Báo cáo tồn kho & hạn dùng

```
Chủ cửa hàng
  └─ 1. Xem tồn theo kho (tổng qtyBase mỗi sản phẩm tại từng kho)
          - Highlight âm kho (tồn < 0)
  └─ 2. Xem tồn theo batch trong kho
          - Highlight batch sắp hết hạn (expDate - today ≤ 180 ngày)
          - Highlight batch đã hết hạn
  └─ 3. Danh sách hàng/batch sắp hết hạn (lọc theo kho + khoảng ngày)
```

## 5. Đồng bộ dữ liệu (Sync)

```
PouchDB local (PWA hoặc Mobile)
  └─ Kết nối LAN → PouchDB.sync(couchDbUrl, { live: true, retry: true })
  └─ Offline → giao dịch được lưu local, hàng đợi sync
  └─ Online lại → sync tự động, conflict resolution theo CouchDB default
```
