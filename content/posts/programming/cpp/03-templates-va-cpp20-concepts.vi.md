+++
title = "Modern C++ #3: Template Metaprogramming và C++20 Concepts & Constraints"
date = '2026-10-16T10:33:00+07:00'
draft = true
tags = ["c++", "templates", "metaprogramming", "sfinae", "concepts", "c++20"]
categories = ["Lập trình", "C++"]
description = "Tìm hiểu bản chất lập trình Template, cơ chế SFINAE và bước chuyển mình vĩ đại với Concepts & Constraints trong chuẩn C++20."
summary = "Từ Generic Programming, Two-phase lookup, Type Traits đến cuộc giải thoát khỏi cơn ác mộng thông báo lỗi của SFINAE bằng Concepts và mệnh đề requires trong C++20."
+++

## 1. Generic Programming: Sức mạnh của Template trong C++

Nếu như trong các ngôn ngữ hướng đối tượng truyền thống (như Java hay C#), tính đa hình thường được thực hiện ở thời gian chạy (**Runtime Polymorphism**) thông qua bảng hàm ảo (vtable/virtual functions), thì C++ sở hữu một "vũ khí tối thượng" ở thời gian biên dịch: **Compile-time Polymorphism** thông qua hệ thống **Templates**.

```cpp
// Hàm tổng quát cho mọi kiểu dữ liệu hỗ trợ toán tử +
template <typename T>
T add(T a, T b) {
    return a + b;
}
```

Khi bạn gọi `add(3, 5)` và `add(2.5, 3.1)`, trình biên dịch C++ sẽ tự động sinh ra hai hàm mã máy độc lập tối ưu riêng cho kiểu `int` và `double`. Hoàn toàn không có chi phí gọi gián tiếp qua con trỏ hàm ảo!

---

## 2. Cơ chế biên dịch Template hoạt động như thế nào?

Trình biên dịch xử lý Template qua cơ chế **Two-Phase Name Lookup** (Tra cứu tên hai giai đoạn):
1. **Giai đoạn 1 (Lúc định nghĩa Template):** Kiểm tra cú pháp cơ bản, các tên không phụ thuộc vào tham số kiểu (`non-dependent names`).
2. **Giai đoạn 2 (Lúc khởi tạo thực thể - Instantiation):** Khi có một kiểu dữ liệu cụ thể truyền vào (ví dụ `add<int>`), trình biên dịch thay thế kiểu cụ thể vào và kiểm tra ngữ nghĩa xem kiểu đó có hỗ trợ các toán tử bên trong không.

Chính vì toàn bộ mã Template cần được biên dịch ở giai đoạn 2 tại nơi gọi (Call-site), nên hầu hết mã Template trong C++ phải được đặt trong các tệp **Header (`.h` / `.hpp`)**, không thể tách rời phần cài đặt sang tệp `.cpp` như hàm thông thường (cho đến khi tính năng C++20 Modules ra đời).

---

## 3. Quá khứ đen tối: Type Traits và SFINAE

Template rất mạnh, nhưng trước C++20, nó có một điểm yếu chết người: **Không có cơ chế kiểm tra ràng buộc kiểu (type constraints) một cách rõ ràng.**

Nếu bạn vô tình truyền một cấu trúc dữ liệu không có toán tử `+` vào hàm `add(a, b)`:
Trình biên dịch sẽ phun ra hàng chục trang thông báo lỗi rườm rà, rối rắm, trỏ sâu vào tận mã nguồn nội bộ của thư viện chuẩn STL.

### SFINAE là gì?
Để giới hạn một hàm template chỉ chấp nhận một số kiểu cụ thể, lập trình viên thời C++11/14 phải viện tới một quy tắc kỳ dị gọi là **SFINAE** (*Substitution Failure Is Not An Error* — Thất bại khi thay thế kiểu không phải là một lỗi biên dịch).

Nếu một kiểu không khớp với template overload, trình biên dịch sẽ im lặng bỏ qua overload đó và thử overload tiếp theo thay vì báo lỗi. Người ta tận dụng quy tắc này cùng với `std::enable_if_t`:

```cpp
#include <type_traits>
#include <iostream>

// Chỉ cho phép hàm này tồn tại nếu T là kiểu số học (Arithmetic: int, float, double...)
template <typename T, 
          typename = std::enable_if_t<std::is_arithmetic_v<T>>>
void print_number(T val) {
    std::cout << "Giá trị số: " << val << "\n";
}
```

Nhìn thì có vẻ tiện lợi, nhưng khi các ràng buộc logic phức tạp hơn, cú pháp `std::enable_if_t` biến mã nguồn C++ thành một "mê cung toán tử" cực kỳ khó đọc và khó bảo trì!

---

## 4. C++20 Concepts: Cuộc cách mạng giải cứu lập trình viên

Chuẩn **C++20** đã chính thức chấm dứt kỷ nguyên của SFINAE bằng tính năng được mong chờ nhất suốt 15 năm qua: **Concepts & Constraints**.

Một **Concept** là một vị từ thời gian biên dịch (compile-time predicate) đánh giá xem một kiểu dữ liệu có thỏa mãn một tập hợp các yêu cầu hay không.

### 4.1. Cú pháp định nghĩa Concept với `requires`

```cpp
#include <concepts>
#include <iostream>

// Tự định nghĩa một Concept: Kiểu T phải là số học VÀ có kích thước >= 4 bytes
template <typename T>
concept LargeNumber = std::is_arithmetic_v<T> && (sizeof(T) >= 4);

// Cách 1: Sử dụng trực tiếp làm tên kiểu tham số
void process_large(LargeNumber auto x) {
    std::cout << "Xử lý số lớn: " << x << "\n";
}

// Cách 2: Sử dụng mệnh đề requires rõ ràng
template <typename T>
    requires LargeNumber<T>
T calculate(T a, T b) {
    return a * b;
}
```

### 4.2. Kiểm tra phương thức và cú pháp bằng `requires-expression`

Bạn có thể tạo ra các Concept kiểm tra xem một kiểu có hỗ trợ hàm thành viên cụ thể nào đó hay không:

```cpp
// Yêu cầu kiểu T phải có phương thức .serialize() trả về std::string
template <typename T>
concept Serializable = requires(T obj) {
    { obj.serialize() } -> std::same_as<std::string>;
};

struct User {
    std::string name;
    std::string serialize() { return "{\"user\": \"" + name + "\"}"; }
};

struct NonSerializableItem {};

void save_to_database(Serializable auto entity) {
    std::cout << "Lưu: " << entity.serialize() << "\n";
}
```

Nếu bạn cố tình gọi:
```cpp
NonSerializableItem item;
save_to_database(item); 
```

Trình biên dịch C++20 sẽ đưa ra một thông báo lỗi ngắn gọn, chính xác tuyệt đối:
> *error: constraints not satisfied for class 'NonSerializableItem'. 'obj.serialize()' is not a valid expression.*

---

## 5. So sánh toàn diện: SFINAE vs C++20 Concepts

| Tiêu chí | SFINAE (C++11 / C++14) | C++20 Concepts |
| :--- | :--- | :--- |
| **Cú pháp** | Rất phức tạp, dùng `std::enable_if` hack vào template header | Tự nhiên, ngắn gọn với từ khóa `concept` và `requires` |
| **Thông báo lỗi biên dịch** | Dài hàng trăm dòng, khó tìm ra nguyên nhân gốc | Rõ ràng, chỉ đích danh thuộc tính hoặc phương thức còn thiếu |
| **Thời gian biên dịch (Compile time)** | Chậm do trình biên dịch phải thử và loại trừ nhiều overloads | **Nhanh hơn đáng kể** vì bộ kiểm tra ràng buộc được tối ưu ở cấp độ cú pháp |
| **Khả năng tái sử dụng** | Khó tái sử dụng, thường phải viết lại logic type traits | Tạo thành các khái niệm độc lập (`std::integral`, `std::ranges::range`) |

---

## 6. Lời kết

Concepts không chỉ là một tính năng cú pháp đơn thuần, nó định hình lại hoàn toàn cách chúng ta tư duy về Generic Programming trong C++. Cả thư viện chuẩn STL từ C++20 trở đi đều được viết lại để tận dụng tối đa sức mạnh của Concepts.

Trong **[Bài 4: Thư viện chuẩn STL: Containers, Iterators và C++20 Ranges]({{< relref "04-stl-containers-va-cpp20-ranges.vi.md" >}})**, chúng ta sẽ xem cách Concepts kết hợp với thư viện Ranges tạo nên phong cách lập trình đường ống (pipeline) kỳ diệu trong C++20.
