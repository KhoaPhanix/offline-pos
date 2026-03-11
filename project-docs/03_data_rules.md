# 03. Data Rules — Multi-warehouse + UoM + Batch/Expiry

## 1. Nguyên tắc thiết kế (đã chốt)
- **Event log / append-only transactions**: purchase, sale, adjustment, transfer là các tx riêng biệt. Không cập nhật trực tiếp field tồn kho để giảm conflict khi sync PouchDB ↔ CouchDB.
- **Tồn kho** được tính tổng hợp từ `qtyBase` của tất cả các tx liên quan đến kho và batch.
- `_id` convention: `<type>:<natural-key>`.

## 2. Doc types (đã chốt)

| type | _id pattern | Mô tả |
|------|-------------|-------|
| `product` | `product:<sku>` | Sản phẩm |
| `warehouse` | `warehouse:<code>` | Kho |
| `batch` | `batch:<productId>:<lotCode>:<expDate>` | Lô hàng |
| `tx` | `tx:<uuid>` | Giao dịch (purchase / sale / adjustment / transfer) |

## 3. UoM — Đơn vị tính (đã chốt)
- Mỗi sản phẩm có `baseUom` (đơn vị cơ sở, ví dụ `pack`).
- Mỗi UoM khai báo `factorToBase` (hệ số quy đổi về `baseUom`).
- Tất cả tồn kho và giao dịch lưu `qtyBase = qty × factorToBase`.

### Cấu trúc product (tóm tắt)
```json
{
  "_id": "product:<sku>",
  "type": "product",
  "sku": "...",
  "name": "...",
  "baseUom": "pack",
  "uoms": [
    { "uom": "pack",   "factorToBase": 1  },
    { "uom": "bundle", "factorToBase": 10 },
    { "uom": "carton", "factorToBase": 30 }
  ],
  "barcodes": [
    { "barcode": "...", "uom": "pack" }
  ]
}
```

## 4. Batch / Expiry (đã chốt)
- Nhập kho **bắt buộc** chỉ định batch (`lotCode`, `expDate`; tuỳ chọn `mfgDate`).
- POS auto-pick lô theo **FEFO** (First Expired, First Out) — lô hết hạn sớm nhất được chọn trước.
- Người dùng **có thể sửa batch** đã auto-pick.
- Cảnh báo sắp hết hạn khi `expDate - today ≤ expiryWarnDays` (mặc định **180 ngày**); **không chặn bán**.

### Cấu trúc batch
```json
{
  "_id": "batch:<productId>:<lotCode>:<expDate>",
  "type": "batch",
  "productId": "product:<sku>",
  "lotCode": "LO001",
  "mfgDate": "YYYY-MM-DD",
  "expDate": "YYYY-MM-DD"
}
```

## 5. Multi-warehouse (đã chốt)

| Loại giao dịch | Tác động tồn kho |
|---------------|----------------|
| `purchase` | + qtyBase tại `toWarehouseId` |
| `sale` | − qtyBase tại `fromWarehouseId` |
| `adjustment` | ± qtyBase tại `warehouseId` |
| `transfer` | − qtyBase tại `fromWarehouseId`, + qtyBase tại `toWarehouseId` |

## 6. Transfer theo batch (đã chốt)
- Mỗi dòng (`line`) của giao dịch `transfer` **bắt buộc** có `batchId`.
- Mục đích: giữ trace hạn dùng và nguồn gốc lô hàng xuyên kho.

### Cấu trúc tx transfer (tóm tắt)
```json
{
  "_id": "tx:<uuid>",
  "type": "tx",
  "txType": "transfer",
  "fromWarehouseId": "warehouse:main",
  "toWarehouseId": "warehouse:counter",
  "lines": [
    {
      "productId": "product:<sku>",
      "batchId": "batch:<productId>:<lotCode>:<expDate>",
      "uom": "carton",
      "qty": 2,
      "qtyBase": 60
    }
  ]
}
```

## 7. Bán âm kho (đã chốt)
- Hệ thống **cho phép** tạo giao dịch sale dù `qtyBase` tồn không đủ.
- UI / báo cáo phải **hiển thị cảnh báo âm kho** (tồn < 0) để chủ cửa hàng xử lý nhập bù hoặc điều chỉnh sau.
