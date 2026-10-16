+++
title = "Group Testing #2: Kiểm thử thích nghi (Adaptive Group Testing) và Thuật toán Hwang"
date = '2026-10-16T10:05:00+07:00'
draft = true
tags = ["group testing", "adaptive", "binary splitting", "hwang", "lý thuyết thông tin", "thuật toán"]
categories = ["Toán học", "Group Testing"]
math = true
description = "Tìm hiểu giới hạn lý thuyết thông tin và các thuật toán kiểm thử thích nghi (adaptive group testing) kinh điển, tiêu biểu là Generalized Binary Splitting của Hwang."
summary = "Phân tích cận dưới số phép thử từ góc nhìn Entropy, thuật toán tìm kiếm nhị phân cho 1 phần tử lỗi và thuật toán chia nhị phân tổng quát của Hwang đạt tiệm cận tối ưu O(d log(N/d))."
+++

## 1. Kiểm thử thích nghi (Adaptive Group Testing) là gì?

Trong bài trước, chúng ta đã làm quen với mô hình hai giai đoạn của Dorfman. Đây là một dạng thích nghi đơn giản: kết quả của giai đoạn 1 quyết định mẫu nào được kiểm tra ở giai đoạn 2.

Tuy nhiên, nếu ta được phép thực hiện nhiều giai đoạn hơn — nghĩa là sau mỗi phép thử, ta biết ngay kết quả và có thể dùng tri thức đó để thiết kế phép thử tiếp theo — thì đó gọi là **Kiểm thử thích nghi hoàn toàn (Fully Adaptive Group Testing)**.

> **Câu hỏi trọng tâm:** Với quyền năng phản hồi theo thời gian thực (feedback), số phép thử ít nhất về mặt lý thuyết để tìm ra $d$ phần tử lỗi trong $N$ phần tử là bao nhiêu?

---

## 2. Cận dưới lý thuyết thông tin (Information-Theoretic Lower Bound)

Xét bài toán xác định tập con $D \subseteq \{1, 2, \dots, N\}$ gồm đúng $d$ phần tử lỗi trong tổng số $N$ phần tử:
- Số cấu hình có thể có của tập $D$ chính là số tổ hợp chập $d$ của $N$:
  $$|\Omega| = \binom{N}{d}$$

Mỗi phép thử nhị phân $y \in \{0, 1\}$ chỉ có thể cung cấp tối đa $1$ bit thông tin (lượng thông tin Shannon: $\log_2 2 = 1$).

Do đó, bất kỳ thuật toán nào muốn phân biệt được $\binom{N}{d}$ trạng thái khác nhau đều phải thỏa mãn bất đẳng thức cây quyết định:
$$
2^T \ge \binom{N}{d} \implies T \ge \left\lceil \log_2 \binom{N}{d} \right\rceil
$$

Sử dụng bất đẳng thức Stirling hoặc xấp xỉ tổ hợp:
$$
\binom{N}{d} \ge \left( \frac{N}{d} \right)^d \implies T \ge d \log_2 \left( \frac{N}{d} \right)
$$

> **Định lý (Cận dưới Entropy):** Mọi thuật toán kiểm thử nhóm (dù thích nghi hay không thích nghi) đều cần ít nhất:
> $$T \ge d \log_2 \left( \frac{N}{d} \right)$$
> phép thử trong trường hợp xấu nhất (worst-case).

---

## 3. Trường hợp cơ bản: $d = 1$ và Tìm kiếm nhị phân (Binary Splitting)

Xét trường hợp đặc biệt khi ta biết chắc chắn chỉ có đúng $d = 1$ phần tử lỗi trong $N$ phần tử:

1. Chia tập $N$ phần tử thành hai nửa bằng nhau: $A$ và $B$ ($|A| = \lfloor N/2 \rfloor$, $|B| = \lceil N/2 \rceil$).
2. Kiểm tra nhóm $A$:
   - Nếu $A$ dương tính ($y = 1$): Phần tử lỗi nằm trong $A$, ta loại bỏ $B$.
   - Nếu $A$ âm tính ($y = 0$): Vì biết có $1$ phần tử lỗi, chắc chắn nó nằm trong $B$, ta loại bỏ $A$ (không cần test $B$!).
3. Lặp lại quá trình trên cho nửa còn lại cho đến khi chỉ còn 1 phần tử.

Số phép thử cần dùng đúng bằng:
$$
T = \lceil \log_2 N \rceil
$$
Đây chính là cận dưới lý thuyết thông tin khi $d = 1$.

---

## 4. Thuật toán chia nhị phân tổng quát của Hwang (GBSA - 1972)

Khi $d > 1$, việc áp dụng chia đôi đơn thuần có thể kém hiệu quả vì cả hai nửa đều có khả năng dương tính. Năm 1972, nhà toán học **Frank K. Hwang** đã công bố thuật toán **Generalized Binary Splitting Algorithm (GBSA)**, một thuật toán kinh điển tiệm cận đạt đến cận dưới lý thuyết thông tin.

### 4.1. Ý tưởng cốt lõi của GBSA

Giả sử tại một bước, ta cần tìm các phần tử lỗi còn lại (gọi là $d'$) trong một tập ứng viên có kích thước $n'$. 

Thay vì chia đôi tập $n'$ (rất dễ dính dương tính khi $n'$ lớn), Hwang đề xuất trích ra một tập con $S$ có kích thước lũy thừa của 2:
$$
|S| = 2^\alpha, \quad \text{với } \alpha = \left\lfloor \log_2 \left( \frac{n' - d' + 1}{d'} \right) \right\rfloor
$$

### 4.2. Các bước thực thi thuật toán

1. **Bước 1 (Kiểm tra khối $S$):** Kiểm tra toàn bộ nhóm $S$.
   - **Trường hợp 1 (Âm tính - $y = 0$):** Toàn bộ $2^\alpha$ phần tử trong $S$ đều sạch! Ta loại $S$ và giữ nguyên số lượng phần tử lỗi cần tìm $d'$.
   - **Trường hợp 2 (Dương tính - $y = 1$):** Nhóm $S$ chứa ít nhất một phần tử lỗi. Ta chuyển sang bước tìm kiếm nhị phân cục bộ.
2. **Bước 2 (Tìm kiếm nhị phân cục bộ):**
   - Thực hiện tìm kiếm nhị phân trên khối $S$ để trích xuất ra đúng một phần tử lỗi chắc chắn. 
   - Vì $|S| = 2^\alpha$, quá trình này tiêu tốn đúng $\alpha$ phép thử.
   - Các phần tử còn lại trong $S$ chưa được khẳng định sẽ được đưa trở lại danh sách ứng viên.
3. **Cập nhật:** Giảm số lỗi cần tìm $d' \leftarrow d' - 1$ và lặp lại cho đến khi tìm đủ $d$ phần tử.

### 4.3. Phân tích độ phức tạp của Hwang GBSA

Hwang đã chứng minh rằng tổng số phép thử $T_{GBSA}$ của thuật toán không vượt quá:

$$
T_{GBSA} \le d \log_2 \left( \frac{N}{d} \right) + O(d)
$$

So sánh với cận dưới lý thuyết thông tin $d \log_2 (N/d)$, thuật toán Hwang chỉ lệch một số hạng tuyến tính theo $d$, đạt tiệm cận tối ưu!

---

## 5. Thuật toán đa giai đoạn của Li (Li's s-stage algorithm)

Thuật toán của Hwang đòi hỏi tuần tự hoàn toàn: test này xong mới quyết định test tiếp theo (độ sâu cây quyết định lên tới $O(d \log(N/d))$). Trong thực tế phòng thí nghiệm, kỹ thuật viên không thể chờ từng mẫu lần lượt.

Năm 1962, C. H. Li đề xuất mô hình $s$ giai đoạn ($s$-stage):
- Cân bằng giữa số phép thử và số vòng đợi (rounds of testing).
- Giai đoạn $1$: Gom nhóm lớn kích thước $k_1$.
- Giai đoạn $2$: Các nhóm dương tính được chia nhỏ tiếp thành các nhóm kích thước $k_2 < k_1$.
- ...
- Giai đoạn $s$: Kiểm tra đơn lẻ.

---

## 6. Đánh giá ưu và nhược điểm của Kiểm thử thích nghi

| Tiêu chí | Ưu điểm | Nhược điểm |
| :--- | :--- | :--- |
| **Số phép đo ($T$)** | Đạt cận dưới thông tin tối ưu $O(d \log(N/d))$. | Khó giảm thêm được nữa. |
| **Độ trễ thời gian (Latency)** | N/A | **Rất lớn** do phải chờ kết quả vòng trước mới chạy tiếp vòng sau. |
| **Tính tự động hóa** | N/A | Khó lập trình cho robot hút mẫu chạy hàng loạt trong các xét nghiệm sinh học. |

Chính vì hạn chế về độ trễ và tính song song, các nhà nghiên cứu đã hướng tới bài toán: **Liệu ta có thể thiết kế tất cả các test ngay từ đầu và chạy đồng thời cùng một lúc hay không?**

Câu trả lời nằm ở bài toán **Kiểm thử phi thích nghi (Non-Adaptive Group Testing)** trong bài tiếp theo.

---

## 7. Tài liệu tham khảo

1. **Hwang, F. K. (1972).** *A method for detecting all defective members in a population by group testing.* Journal of the American Statistical Association, 67(339), 605-608.
2. **Li, C. H. (1962).** *A sequential method for screening experimental variables.* Journal of the American Statistical Association, 57(298), 455-477.
3. **Du, D. Z., & Hwang, F. K. (1993).** *Combinatorial Group Testing and Its Applications.* World Scientific Publishing.
