# 06. Acceptance Criteria & Test Scenarios

## 1. Đồng bộ nội bộ (LAN Sync)
- [ ] PC (PWA) và Mobile kết nối cùng LAN → sync được với CouchDB local.
- [ ] Tạo sản phẩm/kho trên PC → Mobile thấy sau khi sync.
- [ ] Tạo đơn bán trên Mobile → PC thấy sau khi sync.

## 2. Offline-First
- [ ] Mất kết nối LAN → vẫn tạo được đơn sale trên Mobile (lưu PouchDB local).
- [ ] Mất kết nối LAN → vẫn tạo được phiếu purchase / transfer trên PC.
- [ ] Kết nối LAN lại → cả 2 phía tự động sync, dữ liệu nhất quán.

## 3. FEFO + Expiry Warning
- [ ] Có nhiều batch cùng sản phẩm, khác `expDate` → sale auto-pick batch hết hạn sớm nhất trước.
- [ ] Batch đầu tiên không đủ qty → tự động tách sang batch tiếp theo theo FEFO.
- [ ] Batch có `expDate - today ≤ 180 ngày` → hiển thị cảnh báo, **không** chặn bán.
- [ ] Batch đã hết hạn (`expDate < today`) → vẫn cho bán, chỉ hiển thị cảnh báo.
- [ ] Người dùng thay đổi batch thủ công → giỏ hàng cập nhật đúng batch mới.

## 4. Bán âm kho (Negative Stock)
- [ ] Tồn batch tại kho = 0 → vẫn tạo được đơn sale (âm kho), hiển thị cảnh báo.
- [ ] Sau sync, báo cáo tồn kho hiển thị số âm với cảnh báo rõ ràng.

## 5. Multi-warehouse
- [ ] Tạo 2 kho (Kho chính, Kho quầy) → tồn hiển thị riêng từng kho.
- [ ] Chuyển kho từ Kho chính → Kho quầy: tồn Kho chính giảm, Kho quầy tăng đúng `qtyBase`.

## 6. Transfer theo batch (bắt buộc batchId)
- [ ] Tạo transfer không chọn batch → hệ thống **không** cho lưu (validation lỗi).
- [ ] Tạo transfer với `batchId` hợp lệ → lưu thành công, tồn theo batch cập nhật đúng ở cả 2 kho.

## 7. UoM & qtyBase
- [ ] Nhập 1 carton (factorToBase = 30) → `qtyBase` = 30.
- [ ] Bán 2 bundle (factorToBase = 10) → `qtyBase` = 20.
- [ ] Tồn = 30, bán 2 bundle (qtyBase=20) → tồn còn lại = 10.

## 8. CouchDB Self-hosted (không phát sinh chi phí cloud)
- [ ] CouchDB chạy bằng Docker Compose trên PC nội bộ, không cần kết nối internet.
- [ ] Không có cấu hình nào yêu cầu VPS hoặc dịch vụ cloud trả phí.
