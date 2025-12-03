# 📚 Image Processing Toolkit (Streamlit – 8 tuần DIP)

Ứng dụng web xử lý ảnh được xây dựng bằng **Python + Streamlit**.  
Dự án mô phỏng đầy đủ nội dung thực hành của 8 tuần học, bao gồm:

- Week 01 – Digital imaging fundamentals  
- Week 02 – Point processing  
- Week 03 – Histogram processing  
- Week 04 – Spatial filtering  
- Week 05 – Frequency domain  
- Week 06 – PCA & compression  
- Week 07 – Restoration & morphology  
- Week 08 – Segmentation & JPEG-like (DCT)

Ứng dụng được xây dựng chỉ bằng **một file duy nhất – `app.py`** chứa toàn bộ code và giao diện.

----------------------------------------------------------------------------------------------------------

# 1. Cấu trúc dự án
```
.
├── app.py                # toàn bộ xử lý ảnh + UI Streamlit
├── requirements.txt
└── README.md
```

-----------------------------------------------------------------------------------------------------------

# 2. Cài đặt môi trường
## Yêu cầu
- Python ≥ 3.8
- pip

## Cài thư viện
`requirements.txt`:

```
streamlit
opencv-python-headless
scikit-image
scikit-learn
numpy
```

Cài đặt:

```bash
pip install -r requirements.txt
```

-----------------------------------------------------------------------------------------------------------

# 3. Chạy ứng dụng
## Local
```bash
streamlit run app.py
```
→ mở trình duyệt tại **http://localhost:8501**

## Google Colab (tuỳ chọn)
```python
!pip install streamlit opencv-python-headless scikit-image scikit-learn pyngrok
from pyngrok import ngrok
ngrok.set_auth_token("TOKEN")
public_url = ngrok.connect(8501)
!streamlit run app.py --server.port 8501 --server.headless true
```

---------------------------------------------------------------------------------------------------------------

# 4. Cách sử dụng giao diện
- Upload ảnh  
- Chọn tuần (Week01 → Week08)  
- Chọn thuật toán  
- Điều chỉnh tham số  
- Xem ảnh kết quả  

----------------------------------------------------------------------------------------------------------------

# 5. Hàm trợ giúp
### `read_image(file)`
- Đọc file upload → giải mã BGR → chuyển sang RGB

### `to_gray(img)`
- Chuyển sang grayscale nếu ảnh RGB

### `normalize_to_uint8(img)`
- Chuẩn hóa min–max về [0,255]
- Dùng cho FFT, Sobel, DCT, log transform…

-----------------------------------------------------------------------------------------------------------------

# 6. GIẢI THUẬT THEO 8 TUẦN  
# **WEEK 01 – DIGITAL IMAGING FUNDAMENTALS**
### 1) Grayscale – `w1_to_grayscale`
- Dùng `cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)`
- Chuyển 3 kênh R/G/B → 1 kênh mức xám  
- Giúp chuẩn hóa dữ liệu cho các thuật toán tuần sau  
- Giảm độ phức tạp (từ 3 chiều → 1 chiều)
### 2) Resize – `w1_resize`
- Dùng `cv2.resize`  
- Kích thước mới = `(w*scale, h*scale)`  
- INTER_AREA dùng cho thu nhỏ (chất lượng tốt)
### 3) Rotate – `w1_rotate`
- Tạo ma trận quay:
  ```
  M = getRotationMatrix2D(center, angle, scale)
  ```
- Dùng `warpAffine(img, M)`  
- Ứng dụng: xoay ảnh, augmentation,…

---------------------------------------
# **WEEK 02 – POINT PROCESSING**
### 1) Negative – `w2_negative`
```
output = 255 - input
```
- Đảo sáng → tạo ảnh âm bản  
- Dùng để phân tích vùng tối/sáng
### 2) Log transform – `w2_log_transform`
```
s = c * log(1 + r)
```
- r chuẩn hóa về [0,1]  
- Làm rõ chi tiết vùng tối  
- Nén vùng sáng
### 3) Gamma correction – `w2_gamma_correction`
```
s = r^gamma
```
- gamma < 1: sáng hơn  
- gamma > 1: tối hơn  
- Dùng để hiệu chỉnh mức sáng
### 4) Contrast stretching – `w2_contrast_stretch`
- Hàm piecewise-linear
- Ba đoạn:
  - `r < m1`: kéo về tối  
  - `m1 → m2`: giãn độ tương phản  
  - `> m2`: đẩy lên 255  
- Làm rõ các mức xám trung gian

-----------------------------------------------
# **WEEK 03 – HISTOGRAM PROCESSING**
### 1) Histogram Equalization – `w3_hist_equalization`
- Dùng `cv2.equalizeHist`  
- Điều chỉnh histogram để phân bố mức xám đều hơn  
- Tăng contrast toàn cục
### 2) CLAHE – `w3_clahe`
- Adaptive histogram equalization  
- Chia ảnh thành nhiều tile nhỏ  
- Mỗi tile equalize riêng rồi ghép lại  
- clipLimit giới hạn tăng tương phản quá mức  
- Giảm việc "cháy sáng" hoặc noise bị phóng đại

------------------------------------------------------
# **WEEK 04 – SPATIAL FILTERING**
### 1) Mean filter – `w4_mean_filter`
- Kernel k×k toàn 1  
- Tính trung bình giá trị  
- Giảm nhiễu nhẹ nhưng làm mờ biên
### 2) Gaussian filter – `w4_gaussian_filter`
- Kernel tạo bởi hàm Gaussian  
- Giảm nhiễu mượt hơn mean filter  
- Thích hợp cho xử lý trước (preprocessing)
### 3) Median filter – `w4_median_filter`
- Lấy median của cửa sổ k×k  
- Khử nhiễu muối–tiêu rất tốt  
- Giữ biên rõ hơn mean & gaussian
### 4) Sharpen – `w4_sharpen`
Kernel:
```
0 -1  0
-1 5 -1
0 -1  0
```
- Tăng độ nét, nhấn mạnh cạnh
### 5) Sobel edge – `w4_sobel_edge`
- Tính đạo hàm theo:
  - dx = 1, dy = 0 (hướng X)
  - dx = 0, dy = 1 (hướng Y)
- Magnitude:
```
sqrt(gx^2 + gy^2)
```
- Tách biên theo gradient
------------------------------------------------------------------------
# **WEEK 05 – FREQUENCY DOMAIN (FFT)**
### 1) Ideal Low-pass Filter – `w5_ideal_lowpass`
- FFT → shift → tạo mask hình tròn giữ tần số thấp  
- Loại bỏ tần số cao (biên, nhiễu)  
- Ảnh mờ hơn nhưng ít noise
### 2) Ideal High-pass Filter – `w5_ideal_highpass`
- Ngược lại low-pass  
- Giữ tần số cao, bỏ tần số thấp  
- Nhấn mạnh cạnh, chi tiết nhỏ  
- Nhưng dễ gây nhiễu
-------------------------------------------------------------------------
# **WEEK 06 – PCA COMPRESSION**
### PCA Compression – `w6_pca_compress`
- Chuyển grayscale → chuẩn hóa  
- Áp dụng PCA theo chiều rộng (columns)  
- Nén ảnh bằng cách giữ `n_components` thành phần chính  
- Tái tạo ảnh bằng inverse PCA  
- Giảm chiều dữ liệu nhưng vẫn giữ thông tin quan trọng
Đây là cách nén tuyến tính giống JPEG nhưng đơn giản hơn.

------------------------------------------------------------------------
# **WEEK 07 – RESTORATION & MORPHOLOGY**
### 1) Gaussian noise – `w7_add_gaussian_noise`
- Thêm nhiễu phân bố chuẩn  
- Mô phỏng ảnh bị hạt nhiễu
### 2) Median denoise – `w7_median_denoise`
- Loại nhiễu bằng median filter  
- Hiệu quả với noise muối–tiêu
### 3) Morphology – `w7_morphology_operation`
- Ảnh được Otsu threshold trước  
- Các phép:
  - **Erode**: làm nhỏ vùng trắng  
  - **Dilate**: làm lớn vùng trắng  
  - **Open** = erode → dilate (loại nhiễu)  
  - **Close** = dilate → erode (lấp lỗ hổng)  
Ứng dụng: xử lý ảnh nhị phân, tách đối tượng.
-------------------------------------------------------------------------
# **WEEK 08 – SEGMENTATION & JPEG-LIKE**
### 1) Global threshold – `w8_global_threshold`
- Dùng 1 ngưỡng cố định T  
- >T = trắng, <= T = đen  
- Đơn giản nhưng phụ thuộc ánh sáng
### 2) Otsu threshold – `w8_otsu_threshold`
- Tìm ngưỡng tối ưu theo histogram  
- Không cần chọn T thủ công
### 3) K-means segmentation – `w8_kmeans_segmentation`
- Gom cụm pixel thành K cụm màu  
- Dùng vector RGB  
- Output = ảnh với K màu biểu diễn vùng/đối tượng
### 4) Block DCT (JPEG-like) – `w8_block_dct_compress`
- Chia ảnh thành block 8×8  
- DCT từng block  
- Lượng tử hóa bằng Q  
- IDCT tái tạo lại  
- Q càng lớn → nén mạnh → blocking rõ  
-----------------------------------------------------------------------
## Week 01 – Digital imaging fundamentals
###  Grayscale
- Chuyển RGB → Gray  
- Dùng khi cần xử lý đơn kênh
###  Resize
- Scale theo tỉ lệ S  
- `cv2.resize`  
- INTER_AREA khi thu nhỏ → đẹp, ít răng cưa
###  Rotate
- Tạo ma trận quay 2D quanh tâm  
- `cv2.warpAffine` để áp dụng
-----------------------------------
## Week 02 – Point processing
###  Negative
```
output = 255 - input
```
→ Đảo sáng, tạo ảnh âm bản
###  Log transform
```
s = c * log(1 + r)
```
- Tăng chi tiết vùng tối  
- Nén bớt vùng sáng
###  Gamma correction
```
s = r^gamma
```
- gamma < 1 → sáng hơn  
- gamma > 1 → tối hơn  
###  Contrast stretching
- Giãn mức xám theo đoạn (piecewise linear)
- Làm nổi bật vùng trung gian
-------------------------------------------------------
## Week 03 – Histogram processing
###  Histogram Equalization
- Làm phẳng histogram  
- Tăng tương phản toàn ảnh
###  CLAHE
- EQ theo từng vùng nhỏ (tile)  
- Giảm hiện tượng over-enhance  
- Hữu ích cho ảnh thiếu sáng
---------------------------------------------------------
## Week 04 – Spatial filtering
###  Mean filter
- Lọc trung bình trong cửa sổ k×k  
- Làm mượt ảnh
###  Gaussian filter
- Lọc Gaussian  
- Mềm hơn mean filter, ít phá biên
###  Median filter
- Lấy median của vùng k×k  
- Khử nhiễu muối-tiêu tốt nhất
###  Sharpen
Kernel:
```
0 -1  0
-1 5 -1
0 -1  0
```
→ Tăng độ sắc nét
###  Sobel edge
- Tính đạo hàm theo X, Y  
- Gradient magnitude  
→ Phát hiện biên
-------------------------------------------------
## Week 05 – Frequency domain
###  Ideal Low-pass Filter
- FFT → shift  
- Mask hình tròn giữ tần số thấp  
- IFFT → ảnh mờ
###  Ideal High-pass Filter
- Giữ tần số cao  
- Nhấn mạnh biên  
- Nhưng dễ gây nhiễu
---------------------------------------------------
## Week 06 – PCA (Principal Component Analysis)
### PCA Compression
- Biến ảnh (grayscale) thành ma trận  
- Nén chiều theo PCA  
- Dùng `n_components` nhỏ để nén ảnh  
- Tái tạo ảnh bằng `inverse_transform`
→ Minh họa cách các thuật toán nén tuyến tính hoạt động
-----------------------------------------------------------
## Week 07 – Restoration & Morphology
###  Add Gaussian noise
- Thêm nhiễu phân bố chuẩn  
- Mô phỏng ảnh nhiễu thực tế
###  Median denoise
- Khử nhiễu, giữ biên tốt
###  Erosion / Dilation / Opening / Closing
- Dùng threshold + kernel  
- Erode: làm mỏng  
- Dilate: làm dày  
- Open: loại nhiễu nhỏ  
- Close: lấp lỗ  
--------------------------------------------------------------
## Week 08 – Segmentation & JPEG-like
###  Global threshold
- T ngưỡng cố định
- 2 vùng đen – trắng
###  Otsu threshold
- Dùng phân tích histogram để tự động chọn ngưỡng tối ưu  
- Tốt cho ảnh nền + đối tượng rõ ràng
###  K-means segmentation
- Gom cụm theo màu  
- Dùng vector RGB  
- K cụm → K màu
###  Block DCT (JPEG-like)
- Chia block 8×8  
- DCT từng block  
- Lượng tử hóa (Q càng lớn → nén mạnh → mất chi tiết)  
- IDCT để tái tạo
→ Mô phỏng cách JPEG hoạt động
---------------------------------------------------------------


