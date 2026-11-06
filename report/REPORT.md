# Báo cáo bài thực hành Ngày 1 – Đọc prediction YOLO11 để hiểu định dạng nhãn

**Ngày chạy:** 06/11/2026

**Runtime Colab:** CPU

**Python / PyTorch / Ultralytics:** 3.13.18 / 2.11.0+cpu / 8.4.145

**Checkpoint:** `yolo11n-cls.pt `, `yolo11n.pt `, `yolo11n-seg.pt `

**Ngưỡng detection/segmentation dùng khi tạo JSON:** `0.35`

**Thay đổi so với notebook nguồn:** Không

### Từ cần nhớ (đọc nhanh trước khi viết)

Prediction : Kết quả model vừa đoán ra; chưa phải nhãn đúng 
Ground truth : Nhãn do người gán theo quy tắc 
Taxonomy  Danh sách tên lớp có sẵn kèm model (vd. ImageNet-1K, COCO-80) 
Checkpoint : File model `.pt` đã huấn luyện sẵn 
Guideline : Quy tắc gán nhãn do người viết 
Annotator : Người gán nhãn 
Reviewer : QC Người kiểm tra nhãn 
Escalation : Ca khó → hỏi Lab Coach, không tự bịa 
Model score : Số “tin” của model; không phải điểm chất lượng nhãn người 

## 1. Phân loại ảnh

1.  
    "rank": 1,
    "class_id": 468,
    "class_name": "cab",
    "score": 0.510915,
    "taxonomy_name": "ImageNet-1K"

2. Record này nói về một thể riêng trong ảnh. Vì có chứa thông tin loại xe, kích thước xe.

3.  Danh sách tên lớp (class list) do đi kèm taxonomy/checkpoint.

4. Cần ghi cả `class_id`, `class_name` và `taxonomy_name` vì mỗi object cần có id, tên và phân loại.

5. Nếu trong ảnh có nhiều vật / nhiều chủ đề, guideline cần nói rõ id, taxonomy từng vật thể để người gán nhãn chọn **một** lớp?

6. Không lấy score` của model làm ground truth vì từng object có một con số khác nhau.
## 2. Phát hiện vật thể

{
    "sample_id": "kitchen",
    "coco_image_id": 397133,
    "image_width": 640,
    "image_height": 427,
    "task": "object_detection",
    "taxonomy_name": "COCO-80",
    "model_file": "yolo11n.pt",
    "model_sha256": "0ebbc80d4a7680d14987a577cd21342b65ecfd94632bd9a8da63ae6417644ee1",
    "ultralytics_version": "8.4.145",
    "score_threshold": 0.35,
    "class_id": 45,
    "class_name": "bowl",
    "score": 0.719062,
    "coordinate_unit": "pixel",
    "bbox_format": "xyxy",
    "bbox_xyxy": [
      32.65,
      342.12,
      100.16,
      384.93
    ],
    "bbox_width": 67.51,
    "bbox_height": 42.81
  },

1.  
    "class_name": "bowl",
    "score": 0.719062,
    "bbox_xyxy": [
      32.65,
      342.12,
      100.16,
      384.93
    ],
    "bbox_width": 67.51,
    "bbox_height": 42.81,

2. 
    Góc trên - trái : (32.65,342.12)
    Góc dưới - phải : (100.16,384.93)
    Rộng: 67.5 px
    Cao: 42.81 px


## 3. Phân đoạn theo từng đối tượng

**Mở file:** `day1_lab_outputs/segmentation_predictions.json` và `visuals/segmentation_prediction.png`.  
Chỉ dùng ảnh `sample_id = kitchen`. Chọn **một** record.

1. Chép một record: `instance_id`=… ; `class_name`=… ; `score`=… ; số điểm polygon=`polygon_point_count`=… ; 3–5 điểm đầu của `polygon_xy`=…
2. So với box, polygon cho thêm thông tin gì về hình dạng vật thể?
3. `instance_id` dùng để làm gì? Nó **không** phải class ID và **không** phải tracking ID — bạn hiểu câu này thế nào?
4. Viết **một quy tắc vẽ biên mask** để người gán nhãn làm theo.
5. Trên ảnh `kitchen`, chỉ ra **một** chỗ biên mờ / hai vật chạm nhau / bị che. Bạn dùng guideline hay escalation? Vì sao?

## 4. Vòng đời và kiểm tra chất lượng

Lab hôm nay chỉ **xem prediction**. Chuỗi dưới đây là vòng đời production để bạn nối các khái niệm:

`ảnh thô → guideline → ground truth → huấn luyện → prediction → QC/rework`

Điền bảng (viết ngắn, có dẫn chứng từ JSON/PNG khi nói về lỗi):

| Tác vụ | Ground truth trông như thế nào? (đơn vị nhãn) | Lỗi hoặc chỗ khó quyết định bạn thấy trên ảnh/JSON | Annotator làm gì? | Reviewer xem gì? |
| --- | --- | --- | --- | --- |
| Phân loại ảnh |  |  |  |  |
| Phát hiện vật thể |  |  |  |  |
| Instance segmentation |  |  |  |  |

## 5. An toàn dữ liệu

1. Quy tắc bảo vệ dữ liệu của bạn cho bài lab này là gì? (viết 1 câu)
2. Nếu gặp ảnh hoặc dữ liệu không đúng phạm vi, bạn sẽ dừng và báo cho ai?

## 6. Checklist trước khi nộp link repo

Kiểm tra trên GitHub trong thư mục `report/`:

- [ ] `REPORT.md` (file này đã điền)
- [ ] `day1_lab_outputs/classification_predictions.json`
- [ ] `day1_lab_outputs/detection_predictions.json`
- [ ] `day1_lab_outputs/segmentation_predictions.json`
- [ ] `day1_lab_outputs/IMAGE_ATTRIBUTION.md`
- [ ] `day1_lab_outputs/visuals/classification_top5.png`
- [ ] `day1_lab_outputs/visuals/detection_predictions.png`
- [ ] `day1_lab_outputs/visuals/segmentation_prediction.png`
- [ ] Ô validation cuối notebook báo `PASS`
- [ ] Không có họ tên, MSSV hoặc dữ liệu nhạy cảm trong báo cáo/output
- [ ] Đã nộp **link repository** trên LMS
