+++
title = "Modern C++ #2: Move Semantics, Rvalue References và Con trỏ thông minh (Smart Pointers)"
slug = "move-semantics-va-smart-pointers"
date = '2026-10-16T10:32:00+07:00'
draft = true
tags = ["c++", "move-semantics", "smart-pointers", "rvalue", "raii", "unique-ptr", "shared-ptr"]
categories = ["Lập trình", "C++"]
description = "Đi sâu vào cuộc cách mạng Move Semantics trong C++11, bản chất Lvalue/Rvalue và cách sử dụng bộ ba con trỏ thông minh std::unique_ptr, std::shared_ptr, std::weak_ptr."
summary = "Tìm hiểu cách C++ loại bỏ chi phí sao chép sâu (Deep Copy) qua Rvalue Reference, hàm std::move, quy tắc Rule of 5 và quản trị vòng đời đối tượng an toàn tuyệt đối với Smart Pointers."
+++

## 1. Nỗi đau sao chép sâu (Deep Copy) trong C++98

Hãy tưởng tượng bạn có một hàm tải một tệp dữ liệu lớn vào một đối tượng `std::vector<int>` chứa 100 triệu phần tử:

```cpp
std::vector<int> create_large_vector() {
    std::vector<int> data(100'000'000, 1);
    return data; 
}

std::vector<int> my_data = create_large_vector();
```

Trong C++98:
- Đối tượng `data` nằm trên stack của hàm chuẩn bị bị hủy khi hàm kết thúc.
- Để gán vào `my_data`, C++ buộc phải **cấp phát mới 400MB bộ nhớ trên Heap** và **sao chép từng phần tử** từ `data` sang `my_data` (Deep Copy).
- Ngay sau đó, destructor của `data` lại giải phóng 400MB ban đầu.

Hành động này cực kỳ lãng phí tài nguyên CPU và băng thông bộ nhớ! 
Câu hỏi đặt ra là: **Tại sao ta không đơn giản "chuyển quyền sở hữu" (Move) con trỏ trỏ tới 400MB dữ liệu đó cho `my_data` thay vì copy?**

Đây chính là động lực khai sinh **Move Semantics** trong C++11.

---

## 2. Bản chất Lvalue, Rvalue và Rvalue Reference (`T&&`)

Để hiểu Move Semantics, ta cần phân biệt hai khái niệm giá trị:

### 2.1. Lvalue (Locator Value)
- Là đối tượng có danh tính, có tên biến và có địa chỉ bộ nhớ xác định.
- Bạn có thể lấy địa chỉ của nó bằng toán tử `&`.
- Ví dụ: Các biến thông thường (`int x = 10;`), đối tượng được đặt tên.

### 2.2. Rvalue (Read / Right Value)
- Là các giá trị tạm thời (temporary objects), các biểu thức tính toán nhất thời và sắp bị hủy ngay sau dòng lệnh.
- Bạn **không thể** lấy địa chỉ của nó bằng toán tử `&`.
- Ví dụ: Các hằng số nguyên (`42`), kết quả phép cộng (`x + y`), giá trị trả về của hàm theo kiểu giá trị.

```cpp
int a = 10;      // 'a' là lvalue, '10' là rvalue
int b = a + 5;   // 'b' là lvalue, 'a + 5' là rvalue tạm thời
```

### 2.3. Rvalue Reference (`&&`) và `std::move`

Modern C++ giới thiệu toán tử tham chiếu Rvalue: `Type&&`. Nó cho phép gắn một tham chiếu vào một đối tượng tạm thời để "rút ruột" tài nguyên của nó trước khi nó bị hủy!

> **Sự thật về `std::move`:** Hàm `std::move(x)` thực chất **KHÔNG DI CHUYỂN BẤT KỲ THỨ GÌ CẢ!**
> Nó chỉ đơn thuần là một phép ép kiểu (`static_cast<T&&>(x)`), báo hiệu cho trình biên dịch rằng: *"Tôi không cần đối tượng này nữa, bạn có thể tự do lấy tài nguyên bên trong nó."*

---

## 3. Move Constructor và Move Assignment Operator

Để một lớp hỗ trợ di chuyển tài nguyên, ta hiện thực bộ đôi Move Constructor và Move Assignment:

```cpp
#include <iostream>
#include <utility>

class DynamicArray {
private:
    int* m_data;
    size_t m_size;

public:
    // 1. Constructor thông thường
    DynamicArray(size_t size) : m_size(size), m_data(new int[size]) {
        std::cout << "[Constructor] Cấp phát mảng kích thước " << m_size << "\n";
    }

    // 2. Destructor
    ~DynamicArray() {
        delete[] m_data;
    }

    // 3. Move Constructor: "Đánh cắp" con trỏ, không cấp phát mới!
    DynamicArray(DynamicArray&& other) noexcept 
        : m_data(other.m_data), m_size(other.m_size) {
        // Đưa đối tượng cũ về trạng thái an toàn để không bị double-free
        other.m_data = nullptr;
        other.m_size = 0;
        std::cout << "[Move Constructor] Đã chuyển giao quyền sở hữu thành công!\n";
    }

    // 4. Move Assignment Operator
    DynamicArray& operator=(DynamicArray&& other) noexcept {
        if (this != &other) {
            delete[] m_data; // Giải phóng tài nguyên cũ của chính mình
            
            m_data = other.m_data;
            m_size = other.m_size;

            other.m_data = nullptr;
            other.m_size = 0;
            std::cout << "[Move Assignment] Đã gán chuyển giao!\n";
        }
        return *this;
    }

    // Vô hiệu hóa copy hoặc viết riêng Copy Constructor theo Rule of Five
    DynamicArray(const DynamicArray&) = delete;
    DynamicArray& operator=(const DynamicArray&) = delete;
};
```

> **Quy tắc 5 (Rule of Five):** Nếu lớp của bạn cần tự định nghĩa Destructor, thường bạn cũng cần tự định nghĩa đủ bộ 5 phương thức: Destructor, Copy Constructor, Copy Assignment, Move Constructor, Move Assignment.

---

## 4. Bộ ba Con trỏ thông minh (Smart Pointers)

Quản lý con trỏ thô bằng `new`/`delete` rất dễ quên hoặc gặp lỗi khi có ngoại lệ. Modern C++ trong thư viện `<memory>` cung cấp 3 loại Smart Pointer bao bọc hoàn hảo vòng đời đối tượng theo chuẩn RAII:

### 4.1. `std::unique_ptr` — Sở hữu độc quyền (Exclusive Ownership)

- Mỗi đối tượng trên heap chỉ có **duy nhất một** `std::unique_ptr` sở hữu tại một thời điểm.
- **Không thể Copy**, chỉ có thể **Move**.
- Khi `unique_ptr` ra khỏi scope, đối tượng tự động bị `delete`.
- **Zero-overhead:** Kích thước của `std::unique_ptr` đúng bằng một con trỏ thô (8 bytes trên hệ thống 64-bit), không tốn thêm bất kỳ một byte bộ nhớ hay chu kỳ CPU nào!

```cpp
#include <memory>

struct Task {
    int id;
    Task(int i) : id(i) {}
};

void demo_unique_ptr() {
    // Khuyên dùng: Luôn dùng std::make_unique (từ C++14)
    auto task1 = std::make_unique<Task>(101);
    
    // auto task2 = task1; // LỖI BIÊN DỊCH: Không cho phép copy!
    auto task2 = std::move(task1); // Chuyển quyền sở hữu sang task2
    // Lúc này task1 trở thành nullptr!
}
```

### 4.2. `std::shared_ptr` — Đồng sở hữu (Shared Ownership)

- Nhiều con trỏ `shared_ptr` có thể cùng trỏ tới một đối tượng.
- Sử dụng cơ chế đếm tham chiếu (**Reference Counting**) nằm trong một vùng nhớ quản lý riêng gọi là **Control Block**.
- Mỗi khi copy thêm một `shared_ptr`, biến đếm tham chiếu tăng thêm $1$.
- Khi một `shared_ptr` bị hủy, biến đếm giảm $1$.
- Khi biến đếm chạm mốc $0$, đối tượng bên dưới mới chính thức bị giải phóng.

```cpp
void demo_shared_ptr() {
    // std::make_shared cấp phát cả đối tượng và control block trong 1 khối nhớ duy nhất
    auto sp1 = std::make_shared<Task>(202);
    std::cout << "Số con trỏ cùng trỏ: " << sp1.use_count() << "\n"; // 1

    {
        auto sp2 = sp1; // Copy hợp lệ!
        std::cout << "Số con trỏ cùng trỏ: " << sp1.use_count() << "\n"; // 2
    } // sp2 ra khỏi scope, biến đếm giảm về 1

    std::cout << "Sau khi sp2 hủy: " << sp1.use_count() << "\n"; // 1
} // sp1 hủy, biến đếm về 0 -> Đối tượng Task(202) được giải phóng
```

### 4.3. `std::weak_ptr` — Phá vỡ vòng lặp tham chiếu (Circular References)

Một nhược điểm chết người của `shared_ptr` là hiện tượng **tham chiếu vòng tròn (Circular Dependency)**: Đối tượng $A$ giữ `shared_ptr` tới $B$, và $B$ cũng giữ `shared_ptr` tới $A$. Cả hai biến đếm sẽ không bao giờ về $0 \implies **Rò rỉ bộ nhớ vĩnh viễn (Memory Leak)!**

`std::weak_ptr` giải quyết triệt để vấn đề này:
- Nó trỏ tới đối tượng do `shared_ptr` quản lý nhưng **KHÔNG** làm tăng biến đếm tham chiếu.
- Khi cần sử dụng, bạn gọi phương thức `.lock()` để chuyển tạm thời thành `shared_ptr` (nếu đối tượng chưa bị hủy).

---

## 5. Bảng tóm tắt lựa chọn Smart Pointer

| Loại Smart Pointer | Quyền sở hữu | Chi phí bổ sung (Overhead) | Khi nào nên dùng? |
| :--- | :--- | :--- | :--- |
| **`std::unique_ptr`** | Độc quyền (1-1) | **0% (Zero cost)** | **Mặc định lựa chọn đầu tiên** cho mọi tài nguyên động. |
| **`std::shared_ptr`** | Đồng sở hữu (1-N) | Control block + phép toán nguyên tử atomic ref-count | Khi đối tượng có nhiều chủ sở hữu độc lập (ví dụ: các nút đồ thị, cache engine). |
| **`std::weak_ptr`** | Quan sát viên (Observer) | Không tăng biến đếm chính | Phá vỡ vòng lặp tham chiếu trong đồ thị/cây hai chiều, cơ chế Observer Pattern. |

---

## 6. Lời kết

Move Semantics và Smart Pointers là linh hồn của Modern C++, giải phóng lập trình viên khỏi gánh nặng quản lý bộ nhớ thủ công mà vẫn bảo toàn hiệu năng tối đa.

Trong **[Bài 3: Template Metaprogramming và C++20 Concepts & Constraints](../templates-va-cpp20-concepts/)**, chúng ta sẽ bước vào thế giới lập trình đa hình thời gian biên dịch (Compile-time Polymorphism) với sức mạnh của Templates và bước đột phá Concepts trong C++20.
