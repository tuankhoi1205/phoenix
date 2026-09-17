# Problem backlog

Những chỗ gặp trong lúc gán nhãn mà **guideline chưa trả lời được**, cộng các pain point về công cụ.

Ghi ngay khi gặp, kể cả lúc chưa biết xử lý thế nào. Một edge case không được ghi lại thì
mỗi người sẽ tự xử lý theo một kiểu — và đó là nguồn lớn nhất của nhãn không nhất quán.

> Các mục bên dưới là **ví dụ**, tên và link CVAT đều giả. Mẫu trống để copy nằm cuối file.

## Danh sách

| Mã | Tóm tắt | Loại | Mục guideline | Trạng thái | Kết quả |
|---|---|---|---|---|---|
| [P-001](#p-001) | CVAT local không có sẵn công cụ AI segmentation| Pain point công cụ | — | 🛠️ Làm tool  | [source-tool/](source-tool/)  |
| [P-002](#p-002) | Nhãn COCO của model không khớp hoàn toàn với ontology bài lab | Pain point công cụ | — | 🔴 Mở | — |
| [P-003](#p-003) | Phải vẽ lại box y hệt qua nhiều frame liên tiếp | Pain point công cụ | — | 🗣️ Đang bàn | — |

**Loại**

| Loại | Nghĩa là |
|---|---|
| Guideline chưa nói tới | Tình huống không có trong guideline |
| Guideline mơ hồ | Đọc guideline ra được hai cách hiểu trở lên |
| Guideline mâu thuẫn | Hai mục trong guideline nói ngược nhau |
| Pain point công cụ | Guideline rõ, nhưng làm trên CVAT chậm hoặc dễ sai |

**Trạng thái:** 🔴 Mở · 🗣️ Đang bàn · ↗️ Hỏi BTC · ✅ Đã chốt (trỏ sang QĐ) · 🛠️ Làm tool (trỏ sang `source-tool/`) · ⚪ Bỏ (ghi lý do)

---

## P-001

**CVAT local không có sẵn công cụ AI segmentation**

- **Loại:** Pain point công cụ
- **Mục guideline:** —
- **Người phát hiện:** @tuankhoi1205 · 17/09/2026
- **Link CVAT:** http://localhost:8080
- **Mô tả:** CVAT local chỉ có công cụ vẽ thủ công, chưa có detector panoptic trong
  mục AI Tools. Việc tạo mask cho road, sidewalk, building, vegetation và các phương
  tiện bằng polygon hoặc brush mất nhiều thời gian. Việc triển khai Nuclio trực tiếp
  bằng nuctl trên Windows còn lỗi do thiếu môi trường `/bin/sh`, nên phải cấu hình
  Ubuntu WSL và Docker Desktop integration.
- **Hướng đang cân nhắc:**
  1. Tiếp tục vẽ toàn bộ mask thủ công.
  2. Triển khai EoMT-DINOv3 panoptic dưới dạng Nuclio serverless function cho CVAT,
     sau đó chỉ dùng kết quả làm gợi ý và kiểm tra lại bằng mắt.
- **Xử lý tạm trong lúc chờ:** Vẫn vẽ thủ công đối tượng đầu tiên và các vùng khó;
  đang triển khai EoMT-DINOv3 trên CVAT local.
- **Kết quả:** 🛠️ Đang làm trong [source-tool/](source-tool/).

## P-002

**Nhãn COCO của model không khớp hoàn toàn với ontology bài lab**

- **Loại:** Pain point công cụ
- **Mục guideline:** —
- **Người phát hiện:** @tuankhoi1205 · 17/09/2026
- **Link CVAT:** (chưa có frame cụ thể)
- **Mô tả:** EoMT-DINOv3 được pretrained trên COCO Panoptic, trong khi ontology của
  bài lab có các nhãn như `pole`, `traffic sign`, `sidewalk`, `building` và
  `vegetation`. Model không có lớp `pole`; lớp `traffic sign` chỉ có thể ánh xạ gần
  đúng từ `stop sign`. Một số lớp stuff cũng phải ánh xạ, ví dụ
  `pavement-merged → sidewalk`, `tree-merged/grass-merged → vegetation`.
- **Hướng đang cân nhắc:**
  1. Chỉ dùng AI cho các lớp khớp chắc chắn như person, car, bus, truck và road.
  2. Dùng ánh xạ gần đúng cho các lớp stuff, nhưng bắt buộc kiểm tra và sửa thủ công.
  3. Gán `pole` và biển báo không phải stop sign hoàn toàn thủ công.
- **Xử lý tạm trong lúc chờ:** Giữ đầy đủ ontology của task CVAT; không bỏ nhãn chỉ
  vì model không nhận diện được. AI chỉ tạo suggestion, annotator chịu trách nhiệm
  kiểm tra kết quả cuối.
- **Kết quả:** 🔴 Mở — đánh giá lại sau khi chạy thử model trên ảnh của bài lab.

## P-003

**Phải vẽ lại box y hệt qua nhiều frame liên tiếp**

- **Loại:** Pain point công cụ
- **Mục guideline:** —
- **Người phát hiện:** @thanh-vien-d · 18/09/2026
- **Link CVAT:** https://cvat.example.com/tasks/12/jobs/105?frame=200 — frame 200–260, xe đỗ không di chuyển
- **Mô tả:** Ảnh chụp liên tiếp từ camera cố định. Xe đỗ bên đường xuất hiện y nguyên ở hàng chục
  frame, annotator phải vẽ lại ở từng frame. Ước tính chiếm ~40% thời gian job 105.
- **Hướng đang cân nhắc:**
  1. Dùng chế độ *Track* sẵn có của CVAT — cần thử xem có hợp với dữ liệu dạng ảnh rời không.
  2. Viết script đọc file export của CVAT, nhân box sang các frame kế tiếp, rồi import lại.
- **Kết quả:** 🗣️ Đang bàn. Nếu chọn hướng 2 thì đổi trạng thái sang 🛠️ và làm trong
  [`source-tool/`](source-tool/).

---

## Mẫu để copy

```markdown
## P-NNN

**Tóm tắt một dòng**

- **Loại:** Guideline chưa nói tới | Guideline mơ hồ | Guideline mâu thuẫn | Pain point công cụ
- **Mục guideline:** §
- **Người phát hiện:** @ · dd/mm/yyyy
- **Link CVAT:** (bỏ trống nếu không có)
  - https://…/tasks/<id>/jobs/<id>?frame=<n> — frame này có gì
- **Mô tả:**
- **Các cách hiểu:** (với pain point công cụ thì ghi **Hướng đang cân nhắc:**)
  1.
  2.
- **Xử lý tạm trong lúc chờ:**
- **Kết quả:** 🔴 Mở
```

Nhớ thêm một dòng vào bảng **Danh sách** ở đầu file.
