# Offline-First POS + Inventory System

Phần mềm quản lý **kho hàng** (PC/PWA) và **bán hàng** (Mobile POS) dành cho hộ kinh doanh nhỏ, hoạt động **offline-first** và đồng bộ qua mạng LAN nội bộ.

## Tính năng chính

- **Offline-First**: PouchDB local trên cả PC và Mobile; đồng bộ 2 chiều với CouchDB tự host trên LAN.
- **Inventory (PC/PWA)**: Nhập kho, chuyển kho, xem tồn, báo cáo hàng sắp hết hạn.
- **POS (Mobile / React Native)**: Tạo đơn bán offline, auto-pick lô theo FEFO, cho phép sửa batch.
- **Multi-warehouse**: Hỗ trợ nhiều kho (Kho chính, Kho quầy…).
- **UoM**: Đơn vị tính với quy đổi `factorToBase` về `baseUom`.
- **Batch/Lot & Expiry**: Quản lý lô/hạn dùng; cảnh báo sắp hết hạn (mặc định 180 ngày), không chặn bán.
- **Bán âm kho**: Cho phép tạo đơn bán dù tồn kho không đủ.
- **Chuyển kho theo batch**: Mỗi dòng chuyển kho bắt buộc khai báo `batchId`.

## Triển khai

Chạy **nội bộ tại cửa hàng (LAN)**, không yêu cầu VPS hoặc truy cập từ xa:

```
CouchDB (Docker Compose trên PC nội bộ)
       ↕ sync qua LAN
PouchDB (PWA trên PC)    PouchDB (React Native trên Mobile)
```

## Cấu trúc tài liệu (`project-docs/`)

```
project-docs/
├── 01_business_requirements.md   # Yêu cầu nghiệp vụ
├── 02_technical_stack.md         # Kiến trúc & công nghệ
├── 03_data_rules.md              # Quy tắc dữ liệu (UoM, batch, multi-warehouse)
├── 04_user_flows.md              # Luồng người dùng
├── 05_ui_mockups.md              # UI mockups (cập nhật sau)
├── 06_acceptance_tests.md        # Tiêu chí nghiệm thu & test scenarios
└── seed/
    ├── warehouses.json           # Dữ liệu kho mẫu
    ├── products.json             # Dữ liệu sản phẩm mẫu
    └── batches.json              # Dữ liệu lô/batch mẫu
```

Xem thêm: [`project-docs-structure.txt`](./project-docs-structure.txt)
