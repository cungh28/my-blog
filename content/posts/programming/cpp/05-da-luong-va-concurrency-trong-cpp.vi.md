+++
title = "Modern C++ #5: Lập trình Đa luồng và Xử lý Đồng thời (Concurrency & Multithreading)"
date = '2026-10-16T10:35:00+07:00'
draft = true
tags = ["c++", "multithreading", "concurrency", "mutex", "atomic", "lock-free", "c++20"]
categories = ["Lập trình", "C++"]
description = "Nắm vững nghệ thuật lập trình đa luồng trong C++: từ std::thread, std::jthread đến phòng chống Deadlock với std::scoped_lock và lập trình phi khóa với std::atomic."
summary = "Bảo vệ vùng nhớ dùng chung khỏi Data Race, kỹ thuật khóa RAII an toàn, lập trình bất đồng bộ với std::future và khám phá mô hình Memory Ordering trong lập trình phi khóa (Lock-Free)."
+++

## 1. Kỷ nguyên CPU đa nhân và Nhu cầu xử lý song song

Kể từ khi định luật Moore chạm ngưỡng ranh giới nhiệt lượng (Power Wall), các nhà sản xuất chip như Intel, AMD hay Apple không còn tăng xung nhịp đơn nhân (GHz) một cách đột biến, mà chuyển sang tăng số lượng nhân CPU (Cores).

Một ứng dụng C++ đơn luồng truyền thống chỉ có thể tận dụng tối đa $1/8$ hoặc $1/16$ sức mạnh của một vi xử lý hiện đại. Để khai phóng toàn bộ tiềm năng phần cứng, việc làm chủ **Lập trình Đa luồng (Multithreading)** và **Xử lý Đồng thời (Concurrency)** là một kỹ năng bắt buộc.

---

## 2. Quản lý Luồng: Từ `std::thread` đến `std::jthread` (C++20)

### 2.1. Cạm bẫy chết người của `std::thread` (C++11)

Trong C++11, `std::thread` là lớp cơ bản để tạo luồng. Tuy nhiên, nó có một cạm bẫy lớn:

```cpp
#include <iostream>
#include <thread>

void worker() {
    std::cout << "Đang xử lý tác vụ ngầm...\n";
}

void risky_function() {
    std::thread t(worker);
    // Nếu có ngoại lệ ném ra ở đây trước khi gọi t.join(), 
    // Destructor của std::thread sẽ tự động gọi std::terminate() 
    // và làm SẬP TOÀN BỘ CHƯƠNG TRÌNH NGAY LẬP TỨC!
    t.join();
}
```

### 2.2. Sự cứu rỗi của `std::jthread` trong C++20

C++20 giới thiệu **`std::jthread`** (Joining Thread), áp dụng trọn vẹn triết lý RAII:
- Trong hàm hủy (Destructor), `std::jthread` **tự động gọi `.join()`**, bảo đảm an toàn 100% trước mọi ngoại lệ.
- Tích hợp sẵn cơ chế hủy luồng hợp tác (**Cooperative Cancellation**) thông qua `std::stop_token`.

```cpp
#include <iostream>
#include <thread>
#include <chrono>

void safe_worker(std::stop_token stop_tok) {
    while (!stop_tok.stop_requested()) {
        std::cout << "Luồng con đang chạy...\n";
        std::this_thread::sleep_for(std::chrono::milliseconds(200));
    }
    std::cout << "Luồng con nhận tín hiệu dừng an toàn!\n";
}

int main() {
    {
        std::jthread jt(safe_worker);
        std::this_thread::sleep_for(std::chrono::milliseconds(500));
        // Khi jt ra khỏi khối lệnh này, nó tự động gửi stop_token 
        // và tự động gọi join() mà không cần viết mã thủ công!
    }
    std::cout << "Tiến trình chính kết thúc trọn vẹn.\n";
}
```

---

## 3. Đồng bộ hóa và Phòng chống Deadlock

Khi nhiều luồng cùng đọc và ghi vào một biến dùng chung mà không có cơ chế điều phối, **Data Race (Tranh chấp dữ liệu)** sẽ xảy ra $\rightarrow$ Đây là lỗi **Undefined Behavior** nghiêm trọng nhất trong hệ thống!

### 3.1. Các lớp khóa bảo vệ RAII

Tuyệt đối không bao giờ gọi trực tiếp `mtx.lock()` và `mtx.unlock()` thủ công. Hãy dùng các lớp bao bọc RAII:
- **`std::lock_guard` (C++11):** Khóa đơn giản, tự động mở khóa khi ra khỏi scope.
- **`std::unique_lock` (C++11):** Linh hoạt hơn, cho phép mở khóa sớm, chuyển quyền sở hữu khóa, cần dùng với `std::condition_variable`.

### 3.2. Cơn ác mộng Deadlock và giải pháp `std::scoped_lock` (C++17)

Deadlock xảy ra khi: Luồng 1 giữ khóa $A$ và đợi khóa $B$; trong khi Luồng 2 đang giữ khóa $B$ và đợi khóa $A$. Cả hai luồng sẽ bị treo vĩnh viễn!

Trước C++17, việc khóa nhiều Mutex an toàn đòi hỏi viết mã rất phức tạp. C++17 mang đến **`std::scoped_lock`**:

```cpp
#include <mutex>

std::mutex mtx_account_A;
std::mutex mtx_account_B;

void transfer_money() {
    // std::scoped_lock tự động áp dụng thuật toán Deadlock Avoidance 
    // để khóa tất cả các mutex được truyền vào theo thứ tự an toàn tuyệt đối!
    std::scoped_lock lock(mtx_account_A, mtx_account_B);

    // Chuyển tiền giữa hai tài khoản an toàn...
}
```

---

## 4. Lập trình phi khóa (Lock-Free) với `std::atomic`

Khóa Mutex tuy an toàn nhưng có chi phí:
- Có thể gây ra hiện tượng đổi ngữ cảnh (**Context Switch**) làm chậm tiến trình.
- Luồng có thể bị đẩy vào trạng thái ngủ (**Sleep**) và phụ thuộc vào bộ lập lịch của HĐH.

Trong các hệ thống tài chính tần suất cao (High-Frequency Trading - HFT) hoặc xử lý game engine, người ta sử dụng các phép toán nguyên tử cấp phần cứng: **`std::atomic`**.

### 4.1. Cơ chế Compare-And-Swap (CAS)

Phần cứng CPU hiện đại (x86 có chỉ lệnh `CMPXCHG`, ARM có `LDREX/STREX`) cho phép kiểm tra và ghi đè giá trị trong 1 chu kỳ phần tử duy nhất:

```cpp
#include <atomic>
#include <iostream>

std::atomic<int> counter{0};

void increment() {
    // Thao tác cộng nguyên tử, không cần bất kỳ Mutex nào!
    counter.fetch_add(1, std::memory_order_relaxed);
}
```

### 4.2. Khái niệm Mô hình Thứ tự Bộ nhớ (Memory Ordering)

Tại sao lại có tham số `std::memory_order`? Vì trình biên dịch và CPU có xu hướng **tự ý đảo thứ tự các lệnh thực thi (Instruction Reordering)** để tối ưu hóa hiệu năng, miễn là không ảnh hưởng đến logic của luồng đơn. Trong môi trường đa luồng, việc đảo lệnh này có thể gây ra thảm họa!

1. **`std::memory_order_seq_cst` (Sequentially Consistent):** Chế độ an toàn nhất (mặc định), bảo đảm mọi luồng trên mọi nhân CPU đều nhìn thấy thứ tự các phép toán giống hệt nhau.
2. **`std::memory_order_acquire` / `std::memory_order_release`:** Thiết lập rào chắn bộ nhớ (Memory Barrier). Mọi thao tác ghi trước `release` đều được nhìn thấy sau phép đọc `acquire`.
3. **`std::memory_order_relaxed`:** Chỉ bảo đảm tính nguyên tử của biến đó, không ràng buộc thứ tự với các biến xung quanh (đạt tốc độ tối đa).

---

## 5. Bảng tổng kết các kỹ thuật Concurrency

| Kỹ thuật | An toàn | Tốc độ | Độ phức tạp cài đặt | Trường hợp sử dụng |
| :--- | :--- | :--- | :--- | :--- |
| **`std::async` / `std::future`** | Rất cao | Trung bình | Rất dễ | Tác vụ chạy ngầm độc lập (ví dụ gọi API, đọc tệp). |
| **`std::mutex` + `std::scoped_lock`** | Cao | Tốt | Dễ | Bảo vệ các cấu trúc dữ liệu phức tạp dùng chung. |
| **`std::atomic` (Lock-Free)** | Cần cẩn trọng | **Cực nhanh** | Khó (dễ dính lỗi Memory Model) | Biến đếm, cờ hiệu, hàng đợi phi khóa (Lock-free Queue). |

---

## 6. Lời kết

Concurrency trong C++ giống như lái một chiếc xe đua F1: tốc độ xé gió nhưng đòi hỏi người cầm lái phải hiểu rõ từng chi tiết cơ khí bên dưới.

Trong bài viết cuối cùng của chuỗi bài: **[Bài 6: Tối ưu hóa hiệu năng: Compile-time Evaluation, Zero-cost Abstraction và Tooling]({{< relref "06-toi-uu-hieu-nang-va-advanced-tooling.vi.md" >}})**, chúng ta sẽ mở khóa những kỹ thuật tối ưu hóa chuyên sâu nhất ở thời gian biên dịch (`constexpr`, `consteval`), nguyên lý Zero-cost Abstraction và bộ công cụ chẩn đoán lỗi phần cứng chuyên nghiệp.
