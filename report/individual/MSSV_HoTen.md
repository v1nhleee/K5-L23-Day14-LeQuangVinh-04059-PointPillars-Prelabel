# Báo cáo Day 14 — Lê Quang Vinh — 04059

> Sao chép file này thành `report/individual/<MSSV>_<HoTen>.md` (không dấu), ví dụ `2A20260000_NguyenVanAn.md`.

- Mã học viên dùng với `--student`: `04059`
- `seed_tag` trong `frames_A.txt`: `D14-CEA1144A4E81`

## 1. Dự đoán trước sweep (bước 5b)

Ghi **trước** khi chạy `scripts/pp_sweep.py`, rồi commit và push ngay (lịch sử commit là bằng chứng thời điểm viết). Không sửa phần này sau khi có kết quả.

- Tham số tôi nghĩ giảm công sửa nhiều nhất: `score_thr` (ví dụ tăng từ `0.1` lên khoảng `0.3`–`0.4`) và `min_points` (ví dụ tăng từ `0` lên khoảng `5`–`10`).
- Vì sao (nhìn ảnh BEV tập A: box thừa nhiều hay box sót nhiều?): Trên tập A (`s01f26`, `s02f10`, `s04f35`), mô hình gốc (`score_thr=0.1`, `min_points=0`) sinh nhiều box có điểm tin cậy thấp hoặc ít điểm LiDAR (như một số box xa/nhiễu hoặc `npts` rất nhỏ), đồng thời vẫn có các đối tượng thật bị sót hoặc lệch nhãn (như `Bus`/`Truck` hoặc `Cyclist`/`Pedestrian` ở một số frame). Tăng `score_thr` và `min_points` kỳ vọng lọc bớt box dương tính giả/nhiễu để giảm số box phải xóa, dù có thể làm tăng nhẹ box thêm nếu ngưỡng quá cao.

## 2. Tập A: sửa tay và tune

| Cấu hình | Xóa | Thêm | Sửa | Chi phí |
|---|---:|---:|---:|---:|
| Gốc | | | | |
| `best_config.json` | | | | |

- Tham số đã đổi trong `best_config.json`: *(chờ `best_config.json`)*
- Kết quả leave-one-out (dán các dòng `scripts/pp_sweep.py` in ra): *(chờ log `pp_sweep.py`)*
- Cấu hình có ổn định trên từng frame không? Vì sao tôi vẫn chọn (hoặc không chọn) nó: *(chờ kết quả leave-one-out và `best_config.json`)*

## 3. Trước/sau (bước 8)

![so sánh](../../d14_04059/compare/<frame>.jpg)

- `scripts/pp_compare.py` báo giảm (hoặc tăng) ...% công sửa trên tập A.
- Dự đoán ở mục 1 đúng hay sai? Giải thích bằng số box xóa/thêm/sửa: *(chờ kết quả `pp_compare.py`)*

## 4. Tập B

Số đo từ `scripts/pp_prelabel.py --frames d14_04059/frames_B.txt --config d14_04059/best_config.json --gt d14_04059/ref_B.json`:

| Xóa | Thêm | Sửa | Chi phí |
|---:|---:|---:|---:|
| | | | |

- Loại box tốn công sửa nhất ở tập B: *(chờ `ref_B.json` và kết quả đo tập B)*

## 5. Giới hạn

- Tập tune chỉ có 3 frame (`s01f26`, `s02f10`, `s04f35`), nên cấu hình tối ưu trên tập A có thể chưa tổng quát hóa tốt cho toàn bộ 12 frame tập B.
- Mô hình PointPillars huấn luyện trên KITTI chủ yếu tập trung 3 lớp (`Car`, `Pedestrian`, `Cyclist`) và không có sẵn nhãn `Truck`/`Bus` nguyên bản, dễ gây sót hoặc sai lớp/kích thước trên dữ liệu VF.
- Công thức chi phí sửa (`0.3·xóa + 1.0·thêm + 0.5·sửa`) chỉ phản ánh số lượng thao tác theo ngưỡng hình học/IoU, chưa đo trực tiếp thời gian thao tác thực tế của người gán nhãn trên CVAT.
