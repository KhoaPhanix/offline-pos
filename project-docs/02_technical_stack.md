# 02. Technical Stack / Architecture

## 1. Nền tảng ứng dụng (đã chốt)

| App | Nền tảng | Người dùng chính |
|-----|----------|-----------------|
| Inventory | Web/PWA | PC — chủ cửa hàng |
| POS | React Native | Mobile — nhân viên bán hàng |

## 2. Offline-First & Sync (đã chốt)

| Thành phần | Công nghệ |
|-----------|-----------|
| Local DB (PWA) | PouchDB (IndexedDB) |
| Local DB (Mobile) | PouchDB (on-device) |
| Sync server | CouchDB (self-hosted, LAN) |
| Sync strategy | 2-way live/retry replication |

Sơ đồ:
```
CouchDB (Docker Compose trên PC nội bộ)
       ↕ PouchDB 2-way sync qua LAN
PouchDB (PWA trên PC)    PouchDB (React Native trên Mobile)
```

## 3. Môi trường triển khai (đã chốt)
- Triển khai **nội bộ tại cửa hàng (LAN only)**.
- CouchDB chạy trên 1 PC/miniPC nội bộ bằng **Docker Compose**.
- PC và Mobile cùng Wi-Fi/LAN nội bộ, sync vào CouchDB đó.
- **Không sử dụng VPS hoặc cloud** — không phát sinh chi phí thuê server.
- Không cần truy cập từ xa.

## 4. Cấu hình `expiryWarnDays` (đã chốt)
- Giá trị mặc định: **180 ngày** (~6 tháng).
- Áp dụng: cảnh báo khi `expDate - today ≤ expiryWarnDays`; không chặn bán.

## 5. Doc-type conventions
- Tất cả document có trường `type` để phân loại (xem `03_data_rules.md`).
- `_id` theo convention: `<type>:<key>` (ví dụ `product:mi-haohao`, `warehouse:main`).

## 6. Tuỳ chọn chưa chốt (mở rộng giai đoạn sau)
- In hoá đơn (thermal printer).
- Quản lý khách hàng.
- Đổi/trả hàng.
