+++
title = "Cơ học Lượng tử #1: Phương trình Schrödinger và Bản chất Sóng - Hạt"
date = '2026-10-16T14:30:00+07:00'
draft = false
hideMeta = false
tags = ["vật lý", "cơ học lượng tử", "phương trình schrödinger", "hàm sóng", "nguyên lý bất định"]
categories = ["Vật lý", "Khoa học"]
math = true
slug = "co-hoc-luong-tu-phuong-trinh-schrodinger"
description = "Khám phá nền tảng của cơ học lượng tử thông qua phương trình sóng Schrödinger, nguyên lý bất định Heisenberg và sự sụp đổ của hàm sóng."
summary = "Tổng quan về phương trình Schrödinger phụ thuộc thời gian, ý nghĩa xác suất của hàm sóng theo Born và nguyên lý bất định Heisenberg trong cơ học lượng tử."
+++

Vào những thập niên đầu của thế kỷ 20, nền vật lý cổ điển của Isaac Newton và James Clerk Maxwell đã va phải những bức tường nghịch lý không thể vượt qua: bức xạ vật đen, hiệu ứng quang điện và tính bền vững của mô hình nguyên tử. Để giải quyết cuộc khủng hoảng này, cơ học lượng tử ra đời, làm thay đổi vĩnh viễn thế giới quan khoa học về cấu trúc của thực tại.

Ở trung tâm của cơ học lượng tử không-tương-đối-tính là phương trình Schrödinger — một phương trình vi phân mô tả sự tiến hóa theo thời gian của trạng thái lượng tử.

## 1. Giả thuyết De Broglie và Lưỡng tính Sóng - Hạt

Năm 1924, Louis de Broglie đưa ra một giả thuyết mang tính cách mạng: nếu sóng điện từ có thể biểu hiện tính chất hạt (photon của Einstein), thì các hạt vật chất như electron cũng phải sở hữu bản chất sóng. 

Bước sóng De Broglie $\lambda$ liên hệ nghịch với động lượng $p$ của hạt:

$$\lambda = \frac{h}{p} = \frac{2\pi\hbar}{p}$$

Trong đó $h$ là hằng số Planck và $\hbar = \frac{h}{2\pi}$ là hằng số Planck rút gọn.

Hệ quả trực tiếp của giả thuyết này là trạng thái của một hạt vi mô không còn có thể mô tả bằng một tọa độ xác định $\mathbf{r}(t)$ và vận tốc $\mathbf{v}(t)$ như trong cơ học cổ điển. Thay vào đó, nó được đại diện bởi một hàm sóng phức $\Psi(\mathbf{r}, t)$.

## 2. Phương trình Schrödinger Phụ thuộc Thời gian

Năm 1926, Erwin Schrödinger thiết lập phương trình sóng mô tả động lực học của hàm sóng $\Psi(\mathbf{r}, t)$:

$$i\hbar \frac{\partial}{\partial t}\Psi(\mathbf{r}, t) = \hat{H}\Psi(\mathbf{r}, t)$$

Trong đó:
- $i = \sqrt{-1}$ là đơn vị ảo.
- $\hat{H}$ là toán tử Hamilton, tương ứng với toán tử năng lượng toàn phần của hệ.

Đối với một hạt khối lượng $m$ chuyển động trong trường thế $V(\mathbf{r}, t)$, toán tử Hamilton có dạng:

$$\hat{H} = -\frac{\hbar^2}{2m}\nabla^2 + V(\mathbf{r}, t)$$

Với $\nabla^2 = \frac{\partial^2}{\partial x^2} + \frac{\partial^2}{\partial y^2} + \frac{\partial^2}{\partial z^2}$ là toán tử Laplace. Khai triển đầy đủ, phương trình Schrödinger một chiều có dạng:

$$i\hbar \frac{\partial \Psi(x, t)}{\partial t} = -\frac{\hbar^2}{2m}\frac{\partial^2 \Psi(x, t)}{\partial x^2} + V(x, t)\Psi(x, t)$$

Phương trình này là phương trình vi phân tuyến tính bậc nhất theo thời gian và bậc hai theo không gian. Tính tuyến tính đảm bảo nguyên lý chồng chập lượng tử (quantum superposition): nếu $\Psi_1$ và $\Psi_2$ là các nghiệm, thì tổ hợp tuyến tính $c_1\Psi_1 + c_2\Psi_2$ cũng là một trạng thái lượng tử hợp lệ.

## 3. Tiên đề Xác suất của Max Born

Hàm sóng $\Psi(x, t)$ là một đại lượng phức, do đó nó không thể đo đạc trực tiếp. Ý nghĩa vật lý sâu sắc của hàm sóng được Max Born sáng tỏ thông qua quy tắc Born:

Mật độ xác suất tìm thấy hạt tại vị trí $x$ ở thời điểm $t$ tỷ lệ với bình phương độ lớn của hàm sóng:

$$\rho(x, t) = |\Psi(x, t)|^2 = \Psi^*(x, t)\Psi(x, t)$$

Xác suất tìm thấy hạt trong khoảng $[a, b]$ tại thời điểm $t$ được tính bằng tích phân:

$$P(a \le x \le b) = \int_a^b |\Psi(x, t)|^2 dx$$

Vì hạt chắc chắn phải tồn tại ở một nơi nào đó trong không gian, hàm sóng phải thỏa mãn điều kiện chuẩn hóa:

$$\int_{-\infty}^{\infty} |\Psi(x, t)|^2 dx = 1$$

Định lý bảo toàn xác suất chỉ ra rằng nếu hàm sóng được chuẩn hóa tại $t = 0$, phương trình Schrödinger sẽ bảo toàn tính chuẩn hóa này tại mọi thời điểm $t > 0$, nhờ tính hermit (tự liên hợp) của toán tử Hamilton $\hat{H}^\dagger = \hat{H}$.

## 4. Trạng thái Dừng và Phương trình Không Phụ thuộc Thời gian

Khi thế năng $V(x)$ không phụ thuộc vào thời gian, ta có thể giải phương trình Schrödinger bằng phương pháp tách biến:

$$\Psi(x, t) = \psi(x) \phi(t)$$

Thay vào phương trình và chia hai vế cho $\psi(x)\phi(t)$, ta thu được hằng số phân tách $E$ (năng lượng):

$$\frac{i\hbar}{\phi(t)}\frac{d\phi(t)}{dt} = \frac{1}{\psi(x)}\left[ -\frac{\hbar^2}{2m}\frac{d^2\psi(x)}{dx^2} + V(x)\psi(x) \right] = E$$

Phần thời gian cho nghiệm dao động pha:

$$\phi(t) = e^{-iEt/\hbar}$$

Phần không gian dẫn đến **Phương trình Schrödinger Không Phụ thuộc Thời gian**:

$$\hat{H}\psi(x) = E\psi(x)$$

Hay viết tường minh:

$$-\frac{\hbar^2}{2m}\frac{d^2\psi(x)}{dx^2} + V(x)\psi(x) = E\psi(x)$$

Đây là bài toán tìm giá trị riêng và hàm riêng: các mức năng lượng $E_n$ chỉ có thể nhận các giá trị rời rạc (lượng tử hóa) khi thế năng $V(x)$ ràng buộc hạt trong một không gian hữu hạn (ví dụ: giếng thế vô hạn, dao động tử điều hòa, hoặc nguyên tử hydro).

Trong trạng thái dừng, mật độ xác suất không đổi theo thời gian:

$$|\Psi(x, t)|^2 = |\psi(x) e^{-iEt/\hbar}|^2 = |\psi(x)|^2$$

## 5. Nguyên lý Bất định Heisenberg

Một hệ quả toán học trực tiếp của bản chất hàm sóng là Nguyên lý Bất định Heisenberg (1927). Đối với hai toán tử không giao hoán bất kỳ $\hat{A}$ và $\hat{B}$, độ lệch chuẩn của phép đo thỏa mãn hệ thức Robertson-Schrödinger:

$$\sigma_A \sigma_B \ge \frac{1}{2}|\langle [\hat{A}, \hat{B}] \rangle|$$

Với toán tử tọa độ $\hat{x} = x$ và toán tử động lượng $\hat{p} = -i\hbar \frac{\partial}{\partial x}$, hệ thức giao hoán cơ bản là:

$$[\hat{x}, \hat{p}] = \hat{x}\hat{p} - \hat{p}\hat{x} = i\hbar$$

Dẫn đến hệ thức bất định tọa độ - động lượng kinh điển:

$$\sigma_x \sigma_p \ge \frac{\hbar}{2}$$

Điều này chứng minh rằng sự bất định không bắt nguồn từ hạn chế của thiết bị đo, mà là thuộc tính nội tại của chính bản chất lượng tử. Một hạt không thể đồng thời sở hữu một vị trí hoàn toàn xác định và một động lượng hoàn toàn xác định.

## 6. Lời kết

Phương trình Schrödinger đã đặt nền móng toán học vững chắc cho cơ học lượng tử, mở đường cho sự phát triển của vật lý chất rắn, hóa học lượng tử, công nghệ bán dẫn và gần đây nhất là máy tính lượng tử. 

Trong bài viết tiếp theo, chúng ta sẽ chuyển hướng sang một góc nhìn vĩ mô hơn: làm thế nào hàng tỷ tỷ hạt lượng tử vi mô kết hợp lại để tạo nên các định luật nhiệt động lực học và khái niệm Entropy bí ẩn trong **[Bài 2: Vật lý Thống kê: Từ Vi mô đến Vĩ mô: Entropy và Mũi tên Thời gian](../vat-ly-thong-ke-entropy-va-mui-ten-thoi-gian/)**.
