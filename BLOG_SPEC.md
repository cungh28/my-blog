# Quy Chuẩn Viết Bài & Dịch Thuật Cho Blog (Blog Writing & Translation Specification)

Tài liệu này quy định toàn bộ tiêu chuẩn về định dạng, cú pháp, phong cách và các quy tắc kỹ thuật khi viết bài mới hoặc dịch bài trên blog **Blog của Cung / Cung's Blog** (sử dụng Hugo và theme PaperMod).

Mọi prompt tạo bài viết, dịch thuật (dù sử dụng AI hay viết thủ công) đều **BẮT BUỘC** tuân thủ các quy tắc trong tài liệu này để tránh gây lỗi biên dịch (build failure) hoặc làm hỏng giao diện.

---

## 1. Quy tắc Tối giản: Tuyệt đối KHÔNG sử dụng Icon / Emoji

- **Quy định nghiêm ngặt:** Không sử dụng bất kỳ emoji hay icon đồ họa nào (ví dụ: 📐, 💻, ⚛️, 🌿, 🚀, 💡, 📌, 📝, 🔍, ⚠️, ⭐, 🎯, 🔥, ✨, 🎉, ☕, 🏃, v.v.) trong:
  - Tiêu đề bài viết (`title`).
  - Tiêu đề các mục (`##`, `###`, `####`).
  - Danh sách (`-`, `1.`).
  - Nội dung đoạn văn.
- **Lý do:** Định hướng blog hướng tới phong cách học thuật, chỉn chu, gãy gọn, tinh tế và tập trung tối đa vào chiều sâu nội dung.
- **Thay thế:** Thay vì dùng icon làm điểm nhấn, hãy sử dụng từ ngữ chính xác, cấu trúc đề mục rõ ràng, in đậm (`**từ khóa**`) hoặc trích dẫn (`> quote`).

---

## 2. Quy ước Tổ chức Thư mục (Leaf Bundle) & Đặt tên Slug Đa ngôn ngữ

Blog áp dụng kiến trúc **Leaf Bundle** chuẩn mực của Hugo để quản lý bài viết và tài nguyên cục bộ:

- **Mỗi bài viết là một thư mục riêng (Leaf Bundle):**
  - **Tên thư mục trên đĩa (Filesystem):** Đặt bằng **Tiếng Anh**, chữ thường, dạng `kebab-case` và **có tiền tố số thứ tự** đối với các bài trong series (ví dụ: `01-stillness-beyond-screens/`, `02-art-of-reading-paper-books/`). Việc đánh số này giúp trình soạn thảo (VS Code / File Explorer) tự động sắp xếp theo đúng trình tự bài viết, vô cùng trực quan và dễ quản lý khi sáng tác.
  - **Slug trên URL xuất bản:** **TUYỆT ĐỐI KHÔNG chứa số thứ tự (như `01-`, `02-`)** trong thuộc tính `slug` của frontmatter. Nhờ cơ chế ưu tiên `slug` của Hugo, URL xuất bản ra ngoài web sẽ hoàn toàn sạch sẽ (ví dụ: `/posts/life/nhung-not-lang-ngoai-man-hinh/` và `/en/posts/life/stillness-beyond-screens/`), bảo đảm tính thẩm mỹ và tránh để độc giả đoán trước đường dẫn của các bài viết chưa công bố.
- **Bên trong thư mục bài viết gồm:**
  - `index.vi.md`: Bản bài viết Tiếng Việt. Frontmatter phải chứa `slug = "slug-tieng-viet-khong-dau"` (tuyệt đối không có tiền tố số).
  - `index.en.md`: Bản bài viết Tiếng Anh. Frontmatter phải chứa `slug = "english-slug-keyword"` (tuyệt đối không có tiền tố số).
  - Các tài nguyên dùng riêng cho bài viết: Hình ảnh (`cover.jpg`, `diagram.png`), file đính kèm... nằm trực tiếp cùng thư mục, cho phép chèn bằng đường dẫn tương đối `![Mô tả](diagram.png)`.
- **Cấu trúc phân cấp tổng thể:**
  - Bài viết toán học: `content/posts/math/<ten-chu-de>/<01-post-folder>/`
  - Bài viết lập trình: `content/posts/programming/<ngon-ngu-hoac-chu-de>/<01-post-folder>/`
  - Bài viết cuộc sống: `content/posts/life/<01-post-folder>/`
  - Trang mục lục chuyên mục: Sử dụng `_index.vi.md` và `_index.en.md` tại thư mục cha.
  - Trang tĩnh độc lập: `content/about.vi.md`, `content/about.en.md`

---

## 3. Tiêu chuẩn Frontmatter (Định dạng TOML)

Phần đầu mỗi file bài viết **BẮT BUỘC** sử dụng định dạng TOML kẹp giữa cặp `+++`:

```toml
+++
title = "Tiêu đề bài viết viết hoa chữ cái đầu tiêu chuẩn"
date = '2026-10-16T10:00:00+07:00'
draft = true
hideMeta = false
tags = ["toán học", "thuật toán", "c++"]
categories = ["Toán học", "Lập trình"]
math = true
description = "Mô tả ngắn gọn từ 1-2 câu tóm lược nội dung để tối ưu SEO và hiển thị card."
summary = "Đoạn tóm tắt hiển thị ở danh sách bài viết ngoài trang chủ."
+++
```

### Các cạm bẫy kỹ thuật PHẢI TRÁNH trong Frontmatter:
1. **Lỗi chú thích (Comment syntax):**
   - Trong khối TOML `+++`, chú thích **BẮT BUỘC** dùng dấu thăng `# comment`.
   - **TUYỆT ĐỐI KHÔNG** dùng cú pháp HTML `<!-- comment -->` bên trong khối `+++`. Hugo sẽ báo lỗi `unmarshal failed: toml: invalid character` và làm sập build ngay lập tức.
2. **Thời gian đăng bài (`date`):**
   - Không được đặt thời gian `date` ở thì tương lai so với thời điểm build hiện tại, vì Hugo theo mặc định sẽ tự động ẩn các bài viết có ngày trong tương lai.
3. **Trường `draft`:**
   - `draft = true`: Bài viết đang soạn thảo, chỉ xem được trên máy local khi chạy lệnh `hugo server -D`.
   - `draft = false`: Bài viết chính thức xuất bản, sẽ được build và đẩy lên website thật trên GitHub Pages.
4. **Trường `math`:**
   - Bắt buộc đặt `math = true` đối với mọi bài viết có sử dụng công thức toán học LaTeX/KaTeX.

---

## 4. Quy tắc Liên kết Nội bộ (Internal Linking)

- **TUYỆT ĐỐI KHÔNG** sử dụng shortcode `{{< relref "..." >}}` trỏ đến các bài viết đang có `draft = true`.
  - *Lý do:* Khi CI/CD chạy lệnh `hugo --minify` để xuất bản, các bài nháp không tồn tại trong danh mục trang, khiến `relref` không tìm thấy và gây lỗi `ERROR REF_NOT_FOUND` làm hủy bỏ toàn bộ tiến trình deploy.
- **Khuyên dùng:** Sử dụng cú pháp liên kết tương đối chuẩn Markdown:
  ```markdown
  [Tên bài viết](01-tieu-de-bai-viet/)
  # hoặc
  [Tên bài viết](../chu-de-khac/01-tieu-de/)
  ```

---

## 5. Quy chuẩn Viết Công thức Toán học (KaTeX / LaTeX)

Blog đã được cấu hình KaTeX và Goldmark Passthrough tự động:

- **Công thức cùng dòng (Inline math):** Đặt giữa cặp dấu `$`:
  ```markdown
  Độ phức tạp tiệm cận là $O(d \log(N/d))$ với $d \ll N$.
  ```
- **Khối công thức riêng biệt (Block math):** Đặt giữa cặp dấu `$$`:
  ```markdown
  $$
  E[T] = N \left( 1 - (1-p)^k + \frac{1}{k} \right)
  $$
  ```
- Không gõ ký hiệu toán học bằng ký tự Unicode thô (ví dụ tránh gõ x^2, hãy gõ `$x^2$`).

---

## 6. Cấu trúc Trình bày Nội dung Bài viết

1. **Phân cấp Tiêu đề (Headings):**
   - Tiêu đề bài viết trong frontmatter đã là `<h1>`.
   - Trong nội dung bài viết, bắt đầu mục lớn bằng `## 1. Tên mục lớn` (H2).
   - Các mục con bên trong dùng `### 1.1. Tên mục con` (H3), tiếp theo là `####` (H4).
   - Tuyệt đối không dùng `#` (H1) trong phần thân bài viết.
2. **Đoạn mã (Code Blocks):**
   - Luôn chỉ định rõ ngôn ngữ để kích hoạt cú pháp tô màu (syntax highlighting):
     ````markdown
     ```cpp
     #include <iostream>
     int main() { ... }
     ```
     ````
3. **Trích dẫn & Lưu ý:**
   - Dùng cú pháp blockquote chuẩn Markdown: `> Nội dung trích dẫn hoặc câu danh ngôn`.
4. **Sơ đồ minh họa:**
   - Khuyến khích dùng cú pháp Mermaid cho các quy trình, kiến trúc hoặc cây quyết định:
     ````markdown
     ```mermaid
     graph TD
         A[Bắt đầu] --> B{Điều kiện?}
         B -- Có --> C[Xử lý]
         B -- Không --> D[Kết thúc]
     ```
     ````

---

## 7. Quy chuẩn Dịch Thuật (Translation Guidelines)

Khi dịch một bài viết giữa Tiếng Việt và Tiếng Anh:

1. **Giữ nguyên cấu trúc:** Đảm bảo số lượng đề mục (headings), công thức toán học, khối code và thứ tự trình bày hoàn toàn khớp nhau giữa 2 file `.vi.md` và `.en.md`.
2. **Thuật ngữ chuyên ngành:**
   - Giữ thuật ngữ tiếng Anh gốc trong ngoặc đơn ở lần xuất hiện đầu tiên nếu cần giải nghĩa (ví dụ: *Kiểm thử phi thích nghi (Non-Adaptive Group Testing)*, *Bộ đếm tham chiếu (Reference Counting)*).
   - Với các thuật ngữ lập trình quen thuộc (như *Thread, Mutex, Deadlock, Smart Pointer, Heap, Stack, Interface*), có thể giữ nguyên tiếng Anh để đảm bảo tính tự nhiên và chuyên nghiệp.
3. **Giọng văn (Tone of Voice):**
   - Tiếng Việt: Thân thiện, tôn trọng, khiêm nhường, xưng "mình" hoặc diễn đạt khách quan, xưng hô với bạn đọc là "bạn".
   - Tiếng Anh: Chuyên nghiệp, trong sáng, súc tích (clear, concise, engaging).

---

## 8. Danh mục Kiểm tra Trước Khi Xuất Bản (Checklist)

Trước khi commit và push lên remote, hãy tự động kiểm tra các tiêu chí sau:

- [ ] File đã được xóa bỏ $100\%$ các emoji/icon đồ họa chưa?
- [ ] Tên file có đúng định dạng `<slug>.vi.md` hoặc `<slug>.en.md` không?
- [ ] Frontmatter có đúng cú pháp TOML `+++` và chú thích bằng `#` (không có `<!--`) không?
- [ ] Các công thức toán có nằm trong cặp `$ ... $` hoặc `$$ ... $$` không?
- [ ] Các liên kết nội bộ có dùng dạng tương đối chuẩn Markdown (không dùng `relref` tới bài draft) không?
- [ ] Lệnh kiểm tra build sản phẩm thành công mà không có lỗi:
  ```bash
  hugo --minify
  ```
