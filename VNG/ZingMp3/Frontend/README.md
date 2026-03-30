# ZingMp3 Interview Questions

### 0. Mô tả làm sao user thấy được UI trên trình duyệt khi truy cập 1 trang web

- Trình duyệt phân giải DNS để lấy IP.
- Thiết lập kết nối TCP/TLS.
- Gửi HTTP Request.
- Nhận HTTP Response (HTML).
- **Critical Rendering Path**:
  - Xây dựng DOM (từ HTML).
  - Xây dựng CSSOM (từ CSS).
  - Kết hợp DOM + CSSOM = Render Tree.
  - Layout: Tính toán kích thước, vị trí.
  - Paint: Vẽ các điểm ảnh lên màn hình.
  - Compositing: Gộp các lớp lại để hiển thị.

### 1. So sánh Map vs WeakMap. Hoisting hoạt động như thế nào, định nghĩa và ví dụ

- **Map vs WeakMap**:
  - `Map` có thể dùng bất kỳ kiểu dữ liệu nào làm key. `WeakMap` chỉ dùng object làm key.
  - `Map` ngăn chặn garbage collection của key. `WeakMap` cho phép GC thu hồi object nếu không còn reference nào khác (giúp tránh memory leak).
  - `WeakMap` không có thuộc tính `size` và không iterable.
- **Hoisting**:
  - Là cơ chế của JS đưa các khai báo biến/hàm lên đầu scope trước khi thực thi.
  - `var` và `function` được hoists hoàn toàn (truy cập được trước khi khai báo).
  - `let`, `const` bị hoisting nhưng nằm trong "Temporal Dead Zone" (không truy cập được cho đến khi đến dòng khai báo).
  - Ví dụ:
  ```javascript
  console.log(a); // undefined (var)
  var a = 5;

  // console.log(b); // ReferenceError (let)
  let b = 10;
  ```

### 2. Tại sao Map và Set lại cho truy xuất O(1). Cũng truy xuất O(1) với Object mà sao lại dùng nó ?

- `Map`/`Set` sử dụng cấu trúc **Hash Table** bên dưới, ánh xạ key trực tiếp vào một vị trí trong bộ nhớ qua hash function, cho phép truy xuất trung bình $O(1)$.
- **Tại sao dùng Map thay vì Object?**
  - Key của Object chỉ có thể là String hoặc Symbol. Key của Map có thể là bất cứ gì (Object, Function, v.v.).
  - Map duy trì thứ tự chèn.
  - Map có hiệu năng tốt hơn khi thêm/xóa phần tử thường xuyên.
  - Map có sẵn thuộc tính `size` và dễ dàng lặp qua (iterable).

### 3. Http2 có gì, Http1 có gì. Http vs Https

- **HTTP/1.1**: Gửi request tuần tự (mỗi TCP connection xử lý 1 request tại 1 thời điểm), dễ bị Head-of-line blocking. Header dạng text cồng kềnh.
- **HTTP/2**:
  - Binary protocol (hiệu năng cao hơn).
  - Multiplexing: Gửi nhiều request/response đồng thời trên 1 TCP connection.
  - Header Compression (HPACK).
  - Server Push.
- **HTTP vs HTTPS**: HTTPS là HTTP + lớp bảo mật TLS/SSL để mã hóa dữ liệu truyền tải.

### 4. DNS là gì, hoạt động ra sao

- DNS (Domain Name System) là "danh bạ" của Internet, chuyển đổi domain (google.com) thành địa chỉ IP (8.8.8.8).
- **Hoạt động**:
  1. Browser check cache (Browser -> OS -> Router -> ISP).
  2. Nếu không thấy, gửi tới Recursive DNS Server.
  3. Root Nameserver (`.`).
  4. TLD Nameserver (`.com`, `.net`).
  5. Authoritative Nameserver (nơi giữ records cuối cùng).
  6. Kết quả trả về và được cache lại.

### 5. Khi nào nên cache? LRU cache hoạt động như thế nào? Nếu có 1 user info mà 1 ngày user vào 1 lần thì có nên cache hay không ?

- **Khi nào nên cache**: Khi dữ liệu ít thay đổi, tốn chi phí tính toán lớn, hoặc latency tải từ DB/Network cao.
- **LRU (Least Recently Used)**: Thuật toán loại bỏ phần tử "ít được truy cập nhất trong thời gian gần đây" khi bộ nhớ đệm đầy. Thường dùng kết hợp Hash Map và Doubly Linked List.
- **User info 1 ngày vào 1 lần**:
  - Không nên cache lâu dài ở Server-side memory (tốn RAM cho user hiếm khi quay lại).
  - Nên dùng Browser Cache (LocalStorage/Cookie/Cache-Control) hoặc Redis với TTL ngắn. Tuy nhiên, nếu hệ thống có hàng triệu user như vậy, việc cache mọi user sẽ rất lãng phí.

### 6. So sánh: SSG, SSR, ISR

- **SSG (Static Site Generation)**: Render HTML lúc build time. Tốc độ cực nhanh nhưng data bị cũ nếu không build lại.
- **SSR (Server Side Rendering)**: Render HTML mỗi khi có request. Data luôn mới nhưng Server tốn tài nguyên và TTFB (Time to First Byte) lâu hơn.
- **ISR (Incremental Static Regeneration)**: Kết hợp cả hai. Build static trước, sau đó tự động cập nhật lại trang ở background sau một khoảng thời gian định trước (revalidate) mà không cần build lại toàn bộ site.

### 7. Khi nào dùng CSR, SSR, nêu ưu nhược điểm của 2 cái này. E thấy Zing MP3 đang dùng cơ chế rendering nào ? SSR của Nextjs vs PHP/Java/Python có gì khác nhau

- **CSR (Client Side Rendering)**:
  - Ưu: App mượt sau khi load xong (SPA), giảm tải cho server.
  - Nhược: SEO kém, lần đầu load chậm (White screen).
- **SSR**:
  - Ưu: SEO tốt, FCP (First Contentful Paint) nhanh.
  - Nhược: Server tải nặng, mỗi lần chuyển trang có thể bị khựng nếu không tối ưu.
- **Zing MP3**: Hiện tại Zing MP3 bản web thường dùng SSR (Next.js) để tối ưu SEO cho bài hát/ca sĩ và load nhanh nội dung ban đầu.
- **Next.js SSR vs PHP/Java/Python**:
  - PHP/Java: Render xong HTML là kết thúc nhiệm vụ ở server. Client nhận HTML "tĩnh".
  - Next.js SSR: Có quá trình **Hydration**. Sau khi HTML hiển thị, React "sống lại" trên trình duyệt, biến trang web thành một Single Page Application (SPA) đầy đủ tính tương tác.

### 8. Key trong react dùng để làm gì

- Giúp React định danh chính xác phần tử nào đã thay đổi, được thêm vào hoặc bị xóa trong một list.
- Tối ưu quá trình **Reconciliation** (diffing algorithm), giúp React render lại hiệu quả hơn thay vì phải xóa và tạo lại toàn bộ DOM node.

### 9. Virtualized list nó có tính năng chính là gì mà mình lại dùng nó

- **Tính năng**: Chỉ render các phần tử đang hiển thị trong "khung nhìn" (viewport) của user.
- **Tại sao dùng**: Khi có list hàng nghìn record, render tất cả sẽ gây lag, treo trình duyệt và tốn memory. Virtualization giúp giữ số lượng DOM node ổn định dù list dài bao nhiêu.

### 10. Build tool dùng để làm gì.

- Gom nhiều file (Module Bundling).
- Transpile code (ES6+ về ES5 dùng Babel, TS về JS).
- Minify/Uglify code (giảm dung lượng file).
- Optimize assets (nén ảnh, xử lý CSS).
- HMR (Hot Module Replacement) khi development.

### 11. Linked list vs Array list khác nhau gì, để CRUD 1 phần tử tại vị trí cụ thể thì làm sao, độ phức tạp thuật toán như thế nào.

- **Array List**: Lưu trữ liên tục trong bộ nhớ. Truy cập index $O(1)$. Thêm/Xóa ở giữa $O(n)$ do phải dịch chuyển các phần tử.
- **Linked List**: Các node lưu rải rác, liên kết qua pointer. Truy cập index $O(n)$. Thêm/Xóa tại vị trí đã biết $O(1)$ (chỉ cần đổi pointer).
- **CRUD tại vị trí cụ thể**:
  - Array: Truy cập $O(1)$, nhưng Update/Delete có thể tốn $O(n)$ nếu phải shift.
  - Linked List: Phải duyệt từ đầu đến vị trí đó tốn $O(n)$, sau đó thực hiện thao tác tốn $O(1)$.

### 12. Dùng axios để request có điểm gì tốt hơn fetch mặc định.

- Tự động transform dữ liệu JSON.
- Hỗ trợ tốt hơn cho việc Interceptors (Request/Response).
- Hỗ trợ Request Timeout.
- Có khả năng Cancel request dễ dàng hơn (trước khi `AbortController` phổ biến).
- Hỗ trợ tốt hơn trên các trình duyệt cũ.

### 13. Nếu 1 request tới API trong 1s ko có kq thì làm sao cancel request đó rồi đưa ra 1 thông báo ?

- Dùng `AbortController`:
  ```javascript
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), 1000);

  fetch(url, { signal: controller.signal }).catch((err) => {
    if (err.name === "AbortError") console.log("Timeout!");
  });
  ```
- Với Axios: Dùng cấu hình `timeout: 1000` hoặc `CancelToken`.

### 14. MongoDB là gì, được sử dụng khi nào? Khi nào dùng dữ liệu quan hệ

- **MongoDB**: NoSQL (Document-based). Dùng khi schema không cố định, dữ liệu dạng JSON-like, cần scale ngang (sharding) dễ dàng.
- **Dữ liệu quan hệ (SQL)**: Dùng khi dữ liệu có cấu trúc chặt chẽ, cần đảm bảo tính nhất quán cao (ACID), ví dụ: Giao dịch tài chính, quản lý hóa đơn.

### 15. Các tính chất của OOP là gì, nêu ví dụ cho các tính chất đó.

- **Đóng gói (Encapsulation)**: Che giấu thông tin (private/public). Ví dụ: Class BankAccount có số dư private.
- **Kế thừa (Inheritance)**: Lớp con dùng lại code lớp cha. Ví dụ: Dog kế thừa Animal.
- **Đa hình (Polymorphism)**: Một phương thức có nhiều cách thực thi. Ví dụ: `makeSound()` của Dog là "Gâu", của Cat là "Meo".
- **Trừu tượng (Abstraction)**: Chỉ đưa ra các đặc điểm cần thiết, ẩn chi tiết cài đặt. Ví dụ: Interface `Shape` có hàm `draw()`.

### 16. Hash và encrypt có gì khác nhau mà sao ko chỉ kêu là encrypt thôi ?

- **Hash**: Là hàm một chiều. Không thể (về lý thuyết) dịch ngược từ mã hash về dữ liệu gốc. Dùng để lưu password, checksum.
- **Encrypt**: Là hàm hai chiều. Có thể giải mã (Decrypt) nếu có Key tương ứng. Dùng để truyền tin bảo mật.

### 17. Token lưu thế nào ? Cookie có những thuộc tính nào và không define expire được không ? Cookie tồn tại đến thời điểm nào, nếu ko define các thuộc tính của nó thì có bị mất không ?

- **Token**: Lưu ở LocalStorage (dễ bị XSS) hoặc HttpOnly Cookie (an toàn hơn, chống XSS).
- **Cookie Attributes**: `Domain`, `Path`, `Expires`/`Max-Age`, `HttpOnly`, `Secure`, `SameSite`.
- **Expire**: Nếu không define, nó là **Session Cookie** (mất khi đóng trình duyệt).
- **Tồn tại**: Đến lúc hết hạn hoặc browser bị close (nếu là session cookie).

### 18. Nêu cách hoạt động Session storage. Nếu duplicate 1 tab trên trình duyệt thì session storage có còn không ?

- **Hoạt động**: Lưu dữ liệu cho mỗi "session" của 1 tab. Tắt tab là mất.
- **Duplicate tab**: Có. Dữ liệu từ tab cũ được copy sang tab mới, nhưng sau đó hai tab hoạt động độc lập (thay đổi tab này không ảnh hưởng tab kia).

### 19. Vấn đề: Có 1 tòa nhà 100 tầng và 2 quả trứng. Làm thế nào để biết tầng tối đa khi thả quả trứng xuống thì nó ko bể ?

- Dùng chiến thuật nhảy tầng. Để tối ưu, ta không nhảy các khoảng bằng nhau (như 10, 20...).
- Dùng công thức $x + (x-1) + (x-2) + ... + 1 \ge 100$. Giải ra $x = 14$.
- Thả tầng 14, nếu không bể thả tiếp 14+13=27, rồi +12... Nếu trứng 1 bể ở tầng nào, dùng trứng 2 thử từng tầng một từ mốc an toàn cuối cùng đi lên.

### 20. Preload và prefetch khác gì nhau.

- **Preload**: Ưu tiên cao. Tải tài nguyên cần thiết cho trang **hiện tại** ngay lập tức (ví dụ font, main JS).
- **Prefetch**: Ưu tiên thấp. Tải tài nguyên có khả năng sẽ dùng cho **trang tiếp theo** khi trình duyệt đang rảnh.

### 21. Phân biệt type và interface trong typescript. Omit và gì đó nữa ?

- **Interface**: Có thể merge (Declaration merging), chuyên cho cấu trúc Object.
- **Type**: Dùng được cho Union Types, Tuples, Primitives. Linh hoạt hơn nhưng không thể reopen để merge.
- **Omit<T, K>**: Tạo type mới từ T nhưng loại bỏ các key K.
- **Pick<T, K>**: Chỉ lấy các key K từ T.
- **Partial<T>**: Biến tất cả property của T thành optional.
