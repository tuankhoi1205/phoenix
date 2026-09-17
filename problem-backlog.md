# Problem backlog

Những chỗ gặp trong lúc gán nhãn mà **guideline chưa trả lời được**, cộng các pain point về công cụ.

Ghi ngay khi gặp, kể cả lúc chưa biết xử lý thế nào. Một edge case không được ghi lại thì
mỗi người sẽ tự xử lý theo một kiểu — và đó là nguồn lớn nhất của nhãn không nhất quán.

## Danh sách

| Mã | Tóm tắt | Loại | Mục guideline | Trạng thái | Kết quả |
|---|---|---|---|---|---|
| [P-001](#p-001) | Segmentation: xe đứng dày đặc, sát nhau thì tô thế nào | Guideline chưa nói tới | §1, §3, §4 | ↗️ Hỏi BTC | — |
| [P-002](#p-002) | Segmentation: vùng không xác định được class (vd. vùng đen) | Guideline chưa nói tới | §1 RULE 03, §6 | ↗️ Hỏi BTC | — |
| [P-003](#p-003) | Có cần giữ lỗ bên trong mask của vật thể không| Guideline chưa nói tới | — | 🔴 Mở | — |
| [P-002](#p-002) | Khó xác định biên giữa road và sidewalk| Guideline mơ hồ| — | 🔴 Mở | — |

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

**Segmentation: xe đứng dày đặc, sát nhau thì tô thế nào**

- **Loại:** Guideline chưa nói tới
- **Mục guideline:** §1 (semantic, không tách instance), §3 (không chồng lấn, occlusion, object nhỏ/xa),
  §4 (car vs truck vs bus) — không mục nào nói riêng về cụm xe dày đặc
- **Người phát hiện:** Phạm Hữu Hải (@HaiPH1) · 16/09/2026
- **Ảnh:** `w1/segmentation/G06/G06_S002.jpg`
- **Mô tả:** Nhiều xe đứng sát nhau, chồng lên nhau theo phối cảnh. Guideline không nói có phải
  tách biên từng xe hay được tô gộp cả cụm, và xử lý thế nào với khe hở nhỏ giữa các xe hoặc
  xe ở xa quá nhỏ để phân biệt loại.
- **Các cách hiểu:**
  1. Tô gộp cả cụm xe cùng class thành một vùng (semantic không cần tách instance), khe hở nhỏ bỏ qua.
  2. Bám biên từng xe, khe hở thấy được mặt đường thì tô `road`.
  3. Xe ở xa, quá nhỏ để phân biệt `car` / `truck` / `bus` thì không đoán mà đưa review (theo §3, §4).
- **Xử lý tạm trong lúc chờ:** Đã tạo Issue để hỏi, chờ trả lời.
- **Kết quả:** ↗️ Chờ trả lời Issue

## P-002

**Segmentation: vùng không xác định được class (vd. vùng đen)**

- **Loại:** Guideline chưa nói tới
- **Mục guideline:** §1 RULE 03 (không ép vào class gần giống), §6 (chỉ dùng ignore/unlabeled nếu batch
  có cấu hình) — chưa rõ batch này có label ignore/unlabeled hay không
- **Người phát hiện:** Phạm Hữu Hải (@HaiPH1) · 16/09/2026
- **Ảnh:** `w1/segmentation/G06/G06_S002.jpg`
- **Mô tả:** Trong ảnh có vùng (ví dụ một vùng đen) không nhìn ra thuộc class nào trong 19 class.
  Guideline cấm đoán class và cấm tự tạo class ignore, nhưng không nói vùng đó để trống hay gán gì.
- **Các cách hiểu:**
  1. Để trống (không gán class), tạo Issue `UNCERTAIN_CLASS` cho reviewer.
  2. Gán vào label ignore/unlabeled — chỉ làm được nếu batch có cấu hình label này.
  3. Gán theo vùng xung quanh (vd. vùng đen giữa mặt đường → `road`) — dễ thành đoán, trái RULE 03.
- **Xử lý tạm trong lúc chờ:** Đã tạo Issue để hỏi, chờ trả lời.
- **Kết quả:** ↗️ Chờ trả lời Issue

## P-003

**Có cần giữ lỗ bên trong mask của vật thể không**

- **Loại:** Guideline chưa nói tới
- **Mục guideline:** —
- **Người phát hiện:** @tuankhoi1205 · 17/09/2026
- **Link CVAT:** (bổ sung frame khi gặp)
- **Mô tả:** Một số vật thể có vùng nền nhìn xuyên qua, chẳng hạn khoảng trống giữa
  bánh xe, tay người hoặc các bộ phận của vật thể. Chưa rõ mask phải giữ vùng trống
  này hay tô kín toàn bộ.
- **Các cách hiểu:**
  1. Tô kín toàn bộ vùng bên trong đường bao ngoài.
  2. Khoét lỗ tại nơi nền thật sự nhìn thấy xuyên qua.
- **Xử lý tạm trong lúc chờ:** Giữ lỗ nếu vùng nền nhìn thấy rõ; không khoét nếu ảnh
  quá mờ hoặc không xác định được biên.
- **Kết quả:** 🔴 Mở

## P-004

**Khó xác định biên giữa road và sidewalk**

- **Loại:** Guideline mơ hồ
- **Mục guideline:** —
- **Người phát hiện:** @tuankhoi1205 · 17/09/2026
- **Link CVAT:** (bổ sung frame khi gặp)
- **Mô tả:** Ở một số ảnh, mép đường bị mờ, có bóng đổ hoặc không có vạch phân cách
  rõ nên khó xác định pixel thuộc `road` hay `sidewalk`.
- **Các cách hiểu:**
  1. Theo đường mép vật lý của vỉa hè.
  2. Theo sự thay đổi màu sắc nhìn thấy trong ảnh.
- **Xử lý tạm trong lúc chờ:** Ưu tiên đường mép vật lý nếu nhìn thấy; nếu không rõ
  thì bám theo biên màu ổn định nhất và ghi lại frame để review.
- **Kết quả:** 🔴 Mở
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
