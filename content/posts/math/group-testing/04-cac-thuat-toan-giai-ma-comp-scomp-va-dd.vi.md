+++
title = "Group Testing #4: Các thuật toán giải mã NAGT — COMP, SCOMP và Definite Defectives (DD)"
date = '2026-10-16T10:15:00+07:00'
draft = true
tags = ["group testing", "decoding", "comp", "scomp", "dd", "thuật toán", "python"]
categories = ["Khoa học máy tính", "Group Testing"]
math = true
description = "Nghiên cứu các thuật toán giải mã kinh điển trong Kiểm thử phi thích nghi: COMP, Definite Defectives (DD), Sequential COMP (SCOMP) và mã nguồn Python minh họa."
summary = "Cách khôi phục trạng thái mẫu lỗi từ kết quả đo Boolean: nguyên lý loại trừ âm tính của COMP, khẳng định phần tử chắc chắn lỗi với DD, giải bài toán Set Cover với SCOMP và so sánh độ phức tạp tính toán."
+++

## 1. Bài toán giải mã ngược (The Decoding Problem)

Sau khi ma trận đo $\mathbf{M} \in \{0, 1\}^{t \times N}$ được thiết kế và $t$ phép thử được tiến hành đồng thời trong phòng thí nghiệm, chúng ta nhận được vector kết quả:

$$
\mathbf{y} = (y_1, y_2, \dots, y_t)^T \in \{0, 1\}^t
$$

Nhiệm vụ của giai đoạn giải mã (decoding) là:
> **Cho biết trước ma trận $\mathbf{M}$ và vector kết quả $\mathbf{y}$, hãy tái tạo lại vector trạng thái gốc $\hat{\mathbf{x}} \in \{0, 1\}^N$ sao cho $\mathbf{M} \odot \hat{\mathbf{x}} = \mathbf{y}$ với độ chính xác cao nhất và thời gian tính toán nhanh nhất.**

Đây là một bài toán ngược (inverse problem) trên đại số Boolean. Trong bài viết này, chúng ta sẽ tìm hiểu 3 thuật toán giải mã phổ biến nhất: **COMP**, **DD**, và **SCOMP**.

---

## 2. Thuật toán COMP (Combinatorial Orthogonal Matching Pursuit)

**COMP** là thuật toán giải mã đơn giản nhất, nhanh nhất và được sử dụng rộng rãi nhất làm tiêu chuẩn đối sánh (baseline).

### 2.1. Nguyên lý loại trừ âm tính (Negative Test Elimination)

Nhận xét then chốt:
> Nếu phép thử thứ $i$ có kết quả âm tính ($y_i = 0$), thì **TẤT CẢ** các mẫu tham gia vào test thứ $i$ đều chắc chắn **KHÔNG BỊ LỖI** ($x_j = 0$ với mọi $j$ mà $M_{ij} = 1$).

Tại sao? Vì chỉ cần duy nhất một mẫu lỗi lọt vào test $i$, theo tính chất phép toán Boolean OR, kết quả đã phải bằng $1$!

### 2.2. Quy trình thuật toán COMP

1. **Khởi tạo:** Giả sử tất cả $N$ mẫu ban đầu đều là ứng viên nghi nhiễm: $\hat{x}_j = 1$ với mọi $j \in \{1, \dots, N\}$.
2. **Quét các test âm tính:** Với mỗi test $i \in \{1, \dots, t\}$ có $y_i = 0$:
   - Với mọi mẫu $j$ mà $M_{ij} = 1$, ta gán $\hat{x}_j = 0$ (loại trừ chắc chắn).
3. **Kết luận:** Tất cả các mẫu không bị loại trừ sau bước 2 được tuyên bố là bị lỗi ($\hat{x}_j = 1$).

Biểu diễn gọn theo đại số:
$$
\hat{x}_j = 1 \iff \mathbf{m}_j \subseteq \mathbf{y}
$$
(tức là chữ ký của mẫu $j$ hoàn toàn nằm trong các test dương tính).

### 2.3. Tính chất của COMP

- **Không bao giờ có Âm tính giả (Zero False Negatives):** COMP không bao giờ bỏ sót một mẫu lỗi thực sự nào ($x_j = 1 \implies \hat{x}_j = 1$).
- **Có thể xuất hiện Dương tính giả (False Positives):** Nếu một mẫu sạch vô tình chỉ tham gia vào các test dương tính (do các mẫu lỗi khác gây ra), COMP sẽ nhận diện nhầm mẫu đó là lỗi.
- **Độ phức tạp thời gian:** Cực kỳ nhanh, chỉ $O(t \cdot N)$ phép toán bitwise.

---

## 3. Thuật toán Definite Defectives (DD)

Để giảm thiểu số lượng dương tính giả của COMP, thuật toán **Definite Defectives (DD)** bổ sung thêm một bước suy luận logic chặt chẽ.

### 3.1. Ý tưởng cốt lõi

Sau khi COMP đã loại trừ tất cả các mẫu sạch chắc chắn, xét một test dương tính $i$ ($y_i = 1$):
- Tập các ứng viên còn sót lại trong test $i$ là:
  $$C_i = \{j : M_{ij} = 1 \text{ và } \hat{x}_j^{(COMP)} = 1\}$$
- Nếu $|C_i| = 1$ (chỉ còn đúng 1 ứng viên duy nhất $j^* \in C_i$):
  - Do test $i$ dương tính, bắt buộc phải có ít nhất một mẫu lỗi trong $C_i$.
  - Vì $C_i$ chỉ có duy nhất $j^*$, ta suy ra: **$j^*$ chắc chắn là mẫu lỗi (Definite Defective)!**

### 3.2. Quy trình thuật toán DD

1. Chạy COMP để tìm tập ứng viên $S_{cand} = \{j : \hat{x}_j^{(COMP)} = 1\}$.
2. Khởi tạo tập chắc chắn lỗi: $S_{DD} = \emptyset$.
3. Với mỗi test $i$ có $y_i = 1$:
   - Đếm số lượng ứng viên trong test $i$: $C_i = \{j \in S_{cand} : M_{ij} = 1\}$.
   - Nếu $|C_i| = 1$, với $j^* \in C_i$, thêm $j^*$ vào $S_{DD}$.
4. Tuyên bố các phần tử trong $S_{DD}$ là lỗi; các phần tử còn lại trong $S_{cand} \setminus S_{DD}$ là "chưa xác định" hoặc phân loại theo ngưỡng.

---

## 4. Thuật toán SCOMP (Sequential COMP)

Thuật toán **Sequential COMP (SCOMP)** kết hợp giữa DD và thuật toán tham lam giải bài toán phủ tập hợp (**Set Cover Problem**).

1. **Bước 1:** Chạy COMP để loại bỏ các mẫu sạch, thu được $S_{cand}$.
2. **Bước 2:** Chạy DD để tìm tập các phần tử chắc chắn lỗi $S_{DD}$. Đánh dấu các test dương tính đã được giải thích bởi $S_{DD}$.
3. **Bước 3 (Tham lam bao phủ - Greedy Step):**
   - Nếu vẫn còn các test dương tính chưa được giải thích:
   - Trong số các ứng viên còn lại thuộc $S_{cand} \setminus S_{DD}$, chọn ứng viên nào giúp bao phủ (giải thích) được nhiều test dương tính chưa được giải quyết nhất.
   - Thêm ứng viên đó vào tập lỗi và lặp lại cho đến khi tất cả các test dương tính đều được giải thích trọn vẹn.

SCOMP cho hiệu năng thực tế gần như tiệm cận tối ưu trong việc triệt tiêu cả dương tính giả lẫn âm tính giả.

---

## 5. Bảng so sánh các thuật toán giải mã

| Thuật toán | Độ phức tạp thời gian | False Negative | False Positive | Khả năng chạy thực tế |
| :--- | :--- | :--- | :--- | :--- |
| **COMP** | $O(t \cdot N)$ | $0\%$ (Không bao giờ sót) | Có thể có nếu $t$ nhỏ | Cực kỳ nhanh, phần cứng đơn giản |
| **DD** | $O(t \cdot N)$ | Có thể bỏ sót một số lỗi | Rất thấp (chính xác tuyệt đối với $S_{DD}$) | Nhanh, bảo thủ (conservative) |
| **SCOMP** | $O(t \cdot N + d \cdot t \cdot |S_{cand}|)$ | Rất thấp | Rất thấp | Cân bằng tuyệt vời giữa độ chính xác và tốc độ |
| **Tối ưu tổ hợp (Exhaustive)** | $O\left(t \cdot \binom{N}{d}\right)$ | $0\%$ | Tối thiểu | Không khả thi khi $N, d$ lớn (NP-hard) |

---

## 6. Triển khai mẫu bằng Python

Dưới đây là một đoạn mã Python trực quan minh họa cách hoạt động của COMP và DD:

```python
import numpy as np

def generate_group_testing_data(n=1000, d=5, t=100, seed=42):
    np.random.seed(seed)
    # Tạo vector trạng thái thực sự với d phần tử lỗi
    x_true = np.zeros(n, dtype=int)
    defective_indices = np.random.choice(n, size=d, replace=False)
    x_true[defective_indices] = 1
    
    # Ma trận đo ngẫu nhiên Bernoulli với xác suất p = 1 / (d + 1)
    p = 1.0 / (d + 1)
    M = (np.random.rand(t, n) < p).astype(int)
    
    # Kết quả đo y qua phép Boolean OR
    y = np.clip(M @ x_true, 0, 1)
    
    return M, y, x_true, defective_indices

def decode_comp(M, y):
    t, n = M.shape
    x_hat = np.ones(n, dtype=int)
    
    # Loại trừ mọi ứng viên trong các test âm tính (y_i = 0)
    for i in range(t):
        if y[i] == 0:
            clean_indices = np.where(M[i, :] == 1)[0]
            x_hat[clean_indices] = 0
            
    return x_hat

def decode_dd(M, y, x_comp):
    t, n = M.shape
    x_dd = np.zeros(n, dtype=int)
    candidates = np.where(x_comp == 1)[0]
    
    for i in range(t):
        if y[i] == 1:
            # Lọc các ứng viên còn lại trong test i
            active_in_test = [j for j in candidates if M[i, j] == 1]
            if len(active_in_test) == 1:
                x_dd[active_in_test[0]] = 1
                
    return x_dd

if __name__ == "__main__":
    M, y, x_true, true_defectives = generate_group_testing_data(n=500, d=5, t=60)
    
    x_comp = decode_comp(M, y)
    x_dd = decode_dd(M, y, x_comp)
    
    print(f"Chỉ số lỗi thực tế: {sorted(true_defectives)}")
    print(f"COMP phát hiện:     {sorted(np.where(x_comp == 1)[0])}")
    print(f"DD khẳng định:      {sorted(np.where(x_dd == 1)[0])}")
```

---

## 7. Tài liệu tham khảo

1. **Aldridge, M., Baldassini, L., & Johnson, O. (2014).** *Group testing algorithms: bounds and simulations.* IEEE Transactions on Information Theory, 60(6), 3671-3687.
2. **Chan, C. L., Cai, S., & Zheng, H. (2011).** *Non-adaptive group testing with definite defectives.* IEEE Transactions on Information Theory.
3. **Aldridge, M. (2017).** *The capacity of noiseless and noisy non-adaptive group testing.* IEEE Transactions on Information Theory.
