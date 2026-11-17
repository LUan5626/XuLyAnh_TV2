# HVI-CIDNet Feature Visualization

## 1. Giới thiệu

Script `inspect_cidnet_features.py` được xây dựng nhằm trích xuất và trực quan hóa các đặc trưng trung gian trong kiến trúc **HVI-CIDNet**, phục vụ việc phân tích quá trình xử lý ảnh thiếu sáng.

Script tập trung vào hai nhánh chính của mạng CIDNet:

- **Nhánh HV:** Trích xuất và trực quan hóa đặc trưng liên quan đến hai thành phần H và V trong không gian màu HVI.
- **Nhánh I:** Trích xuất và trực quan hóa đặc trưng liên quan đến thành phần cường độ ánh sáng.

Ngoài ra, script còn trực quan hóa các kênh HVI đầu vào và ảnh kết quả sau khi mô hình tăng cường độ sáng.

## 2. Chức năng chính

- Hiển thị ba kênh HVI đầu vào: `H_hat`, `V_hat`, `I_max`.
- Trích xuất feature map qua các khối Encoder, Decoder và LCA của nhánh I và HV.
- Tính toán bản đồ đặc trưng trung bình theo trị tuyệt đối (`mean|F|`).
- Trực quan hóa 8 kênh đặc trưng có mức kích hoạt trung bình cao nhất.
- Tổng hợp feature map của từng nhánh thành ảnh so sánh.
- Hỗ trợ chạy trên GPU CUDA hoặc CPU.

## 3. Cấu trúc file

Đặt script tại thư mục gốc của repository HVI-CIDNet:

```text
HVI-CIDNet/
├── net/
│   ├── CIDNet.py
│   └── LCA.py
├── loss/
├── inspect_cidnet_features.py
└── README.md
```

Các module và thư mục khác của repository gốc được giữ nguyên.

## 4. Cài đặt môi trường

Cài đặt các thư viện cần thiết:

```bash
pip install torch torchvision
pip install numpy pillow matplotlib
pip install huggingface_hub safetensors
```

Lưu ý: Phiên bản PyTorch cần phù hợp với cấu hình CUDA nếu sử dụng GPU.

## 5. Cách sử dụng

### 5.1. Kiểm tra các module trong mô hình

```bash
python inspect_cidnet_features.py --list
```

Lệnh này hỗ trợ kiểm tra tên các module trong kiến trúc để đối chiếu với các hook được sử dụng trong script.

### 5.2. Chạy trực quan hóa feature map

```bash
python inspect_cidnet_features.py --input test.png --out feat_out
```

Trong đó:

- `--input`: Đường dẫn ảnh đầu vào.
- `--out`: Thư mục lưu kết quả thực nghiệm.

Mặc định, script tải trọng số từ Hugging Face:

`Fediory/HVI-CIDNet-LOLv1-wperc`

Cần có kết nối Internet khi tải trọng số lần đầu.

### 5.3. Sử dụng trọng số cục bộ

```bash
python inspect_cidnet_features.py --weights model.safetensors --input test.png --out feat_out
```

Có thể sử dụng file trọng số `.pth` hoặc `.safetensors` tương thích với kiến trúc mô hình.

### 5.4. Một số tham số khác

```bash
python inspect_cidnet_features.py --input test.png --max_side 512 --device cpu --out feat_out
```

- `--max_side`: Giới hạn cạnh dài nhất của ảnh đầu vào. Mặc định là 512 pixel; đặt bằng 0 để giữ nguyên kích thước.
- `--device`: Thiết bị thực thi, có thể chọn `cpu` hoặc `cuda`.

## 6. Kết quả đầu ra

Sau khi chạy thành công, script tạo thư mục kết quả với cấu trúc:

```text
feat_out/
├── 00_hvi_channels.png
├── feat_IE_block0.png
├── feat_HVE_block0.png
├── feat_I_LCA0.png
├── feat_HV_LCA0.png
├── ...
├── branch_I_overview.png
└── branch_HV_overview.png
```

Ý nghĩa:

| File | Nội dung |
|---|---|
| `00_hvi_channels.png` | Ảnh đầu vào, ba kênh HVI và ảnh đầu ra |
| `feat_*.png` | Feature map của từng khối được hook |
| `branch_I_overview.png` | Tổng hợp đặc trưng trung gian nhánh I |
| `branch_HV_overview.png` | Tổng hợp đặc trưng trung gian nhánh HV |

Các feature map được trực quan hóa thông qua giá trị trung bình trị tuyệt đối của đặc trưng theo chiều kênh (`mean|F|`) và 8 kênh có mức kích hoạt trung bình cao nhất.

Lưu ý: Các bản đồ được chuẩn hóa riêng để hiển thị. Vì vậy, màu sắc giữa các feature map không thể được sử dụng trực tiếp để so sánh cường độ kích hoạt tuyệt đối.

## 7. Lưu ý

- Script cần được đặt trong thư mục gốc của repository HVI-CIDNet để import đúng module `net.CIDNet`.
- Cấu trúc mã nguồn phải tương thích với phiên bản mà script sử dụng.
- Trọng số mô hình phải tương thích với kiến trúc CIDNet.
- Ảnh đầu vào được chuyển sang RGB và tự động thu nhỏ nếu vượt quá giới hạn kích thước.
- Kích thước ảnh được padding để phù hợp với quá trình downsampling của mô hình.

## 8. Phạm vi thực hiện

Nội dung này thuộc phần công việc **Thành viên 2**, tập trung vào việc phân tích kiến trúc CIDNet, khối LCA và trực quan hóa đặc trưng trung gian của hai nhánh I và HV.

Mục đích là hỗ trợ quan sát cách các đặc trưng được biến đổi qua các khối trong mạng, từ đó minh họa rõ hơn cơ chế hoạt động của kiến trúc HVI-CIDNet.
