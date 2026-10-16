+++
title = "Group Testing #5: Kiểm thử nhóm trong điều kiện có nhiễu (Noisy Group Testing)"
date = '2026-10-16T10:20:00+07:00'
draft = true
tags = ["group testing", "noisy", "xác suất", "belief propagation", "kênh truyền", "lý thuyết thông tin"]
categories = ["Toán học", "Lý thuyết thông tin"]
math = true
description = "Phân tích bài toán kiểm thử nhóm khi kết quả đo bị nhiễu (dương tính giả và âm tính giả), các mô hình kênh truyền thông tin và thuật toán giải mã chịu lỗi."
summary = "Mô hình hóa sai số thực tế trong PCR và sinh học: kênh đối xứng nhị phân BSC, kênh Z, ma trận disjunct chịu lỗi và các giải thuật giải mã hiện đại như Noisy COMP, Quy hoạch tuyến tính (LP) và Belief Propagation."
+++

## 1. Khi lý thuyết chạm trán thực tế: Sự xuất hiện của Nhiễu

Trong các bài viết trước, chúng ta luôn đặt giả định lý tưởng:
- Nếu nhóm không có mẫu bệnh $\rightarrow$ Test luôn ra $0$ (chính xác 100%).
- Nếu nhóm có ít nhất một mẫu bệnh $\rightarrow$ Test luôn ra $1$ (chính xác 100%).

Thế nhưng trong phòng thí nghiệm thực tế (chẳng hạn xét nghiệm PCR phân tử):
1. **Nhiễm bẩn chéo (Cross-contamination):** Một chút phân tử ADN bay sang giếng bên cạnh khiến mẫu sạch bị báo dương tính giả (**False Positive**).
2. **Hiệu ứng pha loãng (Dilution effect) & lỗi hóa chất:** Khi gộp quá nhiều mẫu vào một giếng, nồng độ virus bị pha loãng xuống dưới ngưỡng phát hiện (Limit of Detection - LoD) của máy, dẫn đến kết quả âm tính giả (**False Negative**).

Chỉ cần duy nhất **1 kết quả bị đảo từ $1 \to 0$ (âm tính giả)**, thuật toán COMP cổ điển sẽ lập tức loại bỏ nhầm bệnh nhân thực sự mắc bệnh!

Do đó, xây dựng lý thuyết **Kiểm thử nhóm có nhiễu (Noisy Group Testing)** là điều bắt buộc cho mọi ứng dụng thực tiễn.

---

## 2. Các mô hình kênh nhiễu toán học

Gọi kết quả đo lý tưởng không nhiễu của phép thử thứ $i$ là $u_i = \bigvee_{j=1}^N M_{ij} x_j \in \{0, 1\}$. Kết quả quan sát thực tế bị nhiễu là $y_i \in \{0, 1\}$.

Ta mô hình hóa mối quan hệ giữa $u_i$ và $y_i$ qua kênh xác suất chuyển trạng thái $P(y_i \mid u_i)$:

### 2.1. Kênh đối xứng nhị phân (Binary Symmetric Channel - BSC)

Nhiễu lật bit đối xứng với xác suất lỗi $q \in [0, 0.5)$:
$$
P(y_i = 1 \mid u_i = 0) = q \quad \text{(Dương tính giả)}
$$
$$
P(y_i = 0 \mid u_i = 1) = q \quad \text{(Âm tính giả)}
$$

### 2.2. Kênh Z (Z-Channel / Asymmetric Noise)

Trong nhiều xét nghiệm hóa sinh, tỷ lệ âm tính giả và dương tính giả rất bất đối xứng:
$$
P(y_i = 1 \mid u_i = 0) = q_0 \quad (\text{thường rất nhỏ, } \approx 0)
$$
$$
P(y_i = 0 \mid u_i = 1) = q_1 \quad (\text{do hiệu ứng pha loãng, } q_1 > 0)
$$

---

## 3. Ma trận Disjunct chịu lỗi (Error-Tolerant Disjunct Matrices)

Để chống lại $e$ lỗi đo lường tùy ý (adversarial noise), cấu trúc ma trận $\mathbf{M}$ cần được gia cố thêm khoảng cách Hamming:

> **Định nghĩa ($(d, e)$-disjunct):** Ma trận $\mathbf{M}$ được gọi là $(d, e)$-disjunct nếu với mọi tập $S \subseteq \{1, \dots, N\}$ có $|S| \le d$ và mọi $k \notin S$, cột $\mathbf{m}_k$ có ít nhất $e + 1$ vị trí số $1$ không bị phủ bởi hợp Boolean của các cột trong $S$:
> $$
> \left| \text{supp}(\mathbf{m}_k) \setminus \bigcup_{j \in S} \text{supp}(\mathbf{m}_j) \right| \ge e + 1
> $$

Với tính chất này, dù có tối đa $e$ phép thử bị nhiễu lật ngược kết quả, chữ ký của cột $\mathbf{m}_k$ vẫn có ít nhất một vị trí độc lập giúp ta nhận diện chính xác!

Số phép đo cần thiết để chống lỗi tăng lên tỷ lệ với $e$:
$$
t = O((d^2 + d \cdot e) \log N)
$$

---

## 4. Các thuật toán giải mã có khả năng chống nhiễu

### 4.1. Noisy COMP (Thuật toán COMP theo ngưỡng)

Thay vì loại bỏ ứng viên ngay khi gặp 1 test âm tính, Noisy COMP đếm số lần mâu thuẫn:
- Với mỗi mẫu $j$, đếm số phép thử mà $M_{ij} = 1$ nhưng $y_i = 0$:
  $$v_j = \sum_{i: M_{ij} = 1} \mathbb{I}(y_i = 0)$$
- **Quy tắc quyết định:** Nếu $v_j \le \tau$ (với $\tau$ là ngưỡng cho phép chịu đựng lỗi), ta tuyên bố mẫu $j$ bị lỗi ($\hat{x}_j = 1$). Ngược lại, nếu số lần mâu thuẫn vượt quá $\tau$, mẫu $j$ được phân loại là sạch.

### 4.2. Giải mã bằng Quy hoạch tuyến tính (Linear Programming - LP Decoding)

Ta có thể nới lỏng bài toán tối ưu rời rạc thành bài toán quy hoạch tuyến tính khả vi:
$$
\min_{\mathbf{x} \in [0, 1]^N, \mathbf{\xi} \in \mathbb{R}^t} \sum_{j=1}^N x_j + \lambda \sum_{i=1}^t \xi_i
$$
thỏa mãn các ràng buộc tuyến tính mô phỏng kênh nhiễu. Phương pháp này kết nối chặt chẽ với kỹ thuật giải mã mã sửa sai trong lý thuyết thông tin.

### 4.3. Lan truyền niềm tin (Belief Propagation - BP)

Biểu diễn bài toán dưới dạng đồ thị nhân tử (Factor Graph) hai phía:
- Một bên là $N$ biến ngẫu nhiên $x_1, \dots, x_N$ (Variable nodes).
- Một bên là $t$ phép đo $y_1, \dots, y_t$ (Check / Factor nodes).

Các thông điệp xác suất hậu nghiệm (posterior log-likelihood ratios) được truyền qua lại giữa các nút qua các chu kỳ lặp:
$$
\mu_{j \to i}(x_j) \longleftrightarrow \mu_{i \to j}(x_j)
$$
Khi đồ thị thưa (Sparse graph), thuật toán BP đạt được độ chính xác phi thường gần sát với giới hạn lý thuyết thông tin trong thời gian $O(t \log t)$.

---

## 5. Giới hạn dung lượng thông tin (Capacity of Noisy Group Testing)

Scarlett & Cevher (2016) và Aldridge (2019) đã thiết lập giới hạn dung lượng lý thuyết thông tin cho bài toán kiểm thử nhóm có nhiễu:

$$
C = 1 - H_2(q)
$$
trong đó $H_2(q) = -q \log_2 q - (1-q) \log_2(1-q)$ là hàm Entropy nhị phân.

Số phép đo tối thiểu cần thiết trong kênh nhiễu BSC với xác suất lỗi $q$ là:
$$
T \ge \frac{d \log_2(N/d)}{1 - H_2(q)}
$$

Công thức này phản ánh một vẻ đẹp toán học thuần khiết: **Nhiễu làm suy giảm dung lượng kênh đúng bằng giá trị Entropy của nhiễu, tương tự như Định lý Kênh truyền nổi tiếng của Claude Shannon!**

---

## 6. Tổng kết

Việc đưa nhiễu vào mô hình biến Group Testing từ một bài toán thuần túy tổ hợp thành một bài toán giao thoa giữa Lý thuyết thông tin, Xác suất thống kê và Đồ thị học.

Trong bài viết cuối cùng của chuỗi bài, chúng ta sẽ mở rộng tầm mắt nhìn ra bức tranh toàn cảnh: Mối liên hệ kỳ diệu giữa Group Testing và **Compressed Sensing (Nén cảm nhận)** cũng như các ứng dụng đời thực trong đại dịch COVID-19 và Dữ liệu lớn.

---

## 7. Tài liệu tham khảo

1. **Scarlett, J., & Cevher, V. (2016).** *Phase transitions in group testing.* In Proceedings of the 2016 Annual ACM-SIAM Symposium on Discrete Algorithms (SODA).
2. **Aldridge, M. (2019).** *Individual testing is optimal for nonadaptive group testing under very high noise.* IEEE Transactions on Information Theory, 65(4), 2107-2114.
3. **Cheraghchi, M. (2013).** *Noise-resilient group testing: Limits and constructions.* IEEE Transactions on Information Theory, 59(9), 5760-5776.
