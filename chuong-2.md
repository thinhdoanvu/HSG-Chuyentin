# Chương 2: Độ phức tạp thuật toán

## 2.1. Ký hiệu Big-O và bảng ước lượng theo giới hạn dữ liệu

**Vấn đề:** Cho 2 đoạn code cùng giải quyết 1 bài toán nhưng viết khác nhau. Làm sao biết đoạn nào "nhanh hơn" **mà không cần chạy thử**, đặc biệt khi dữ liệu đầu vào có thể lên tới hàng triệu phần tử?

**Phân tích vấn đề:** Thời gian chạy thực tế phụ thuộc vào tốc độ CPU, ngôn ngữ lập trình, cách viết code — những yếu tố này thay đổi tuỳ máy. Cái ta cần là một cách đo **độc lập với phần cứng**: đếm số phép tính cơ bản (so sánh, cộng, gán...) tăng theo kích thước đầu vào n như thế nào.

**Giải pháp đơn thuần:** Đếm chính xác số phép tính. Ví dụ với vòng lặp `for i in 0..n: x = x + 1`, số phép tính là đúng n.

**Khó khăn với giải pháp đơn thuần:** Đếm chính xác từng phép tính (kể cả hằng số, hệ số) rất phức tạp và không cần thiết — khi n đủ lớn, `3n + 5` và `3n` gần như không khác biệt về xu hướng tăng; điều quan trọng là **bậc tăng** (n, n², log n...), không phải hệ số hay hằng số cộng thêm.

**Cách tiếp cận mới — Big-O:** Big-O(f(n)) mô tả **giới hạn trên** của tốc độ tăng, bỏ qua hằng số nhân và các số hạng bậc thấp hơn. Quy tắc thực hành:
- Vòng lặp đơn chạy n lần → O(n).
- k vòng lặp lồng nhau, mỗi vòng chạy n lần → O(n^k).
- Vòng lặp nhân đôi/chia đôi mỗi bước (`i *= 2` hoặc `i /= 2`) → O(log n).
- Gọi hàm đệ quy: phân tích bằng cây đệ quy hoặc Master Theorem (mục 2.2).

### Ví dụ 1 (Dễ) — Vòng lặp đơn và vòng lặp lồng độc lập
```cpp
// Đoạn A: O(n)
for (int i = 0; i < n; i++) { sum += a[i]; }

// Đoạn B: O(n^2) - 2 vòng lặp lồng nhau, độc lập, mỗi vòng chạy đủ n lần
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++)
        sum += a[i] * a[j];
```

### Ví dụ 2 (Trung bình) — Vòng lặp lồng nhau phụ thuộc (tam giác)
```cpp
// Đếm tổng số lần vòng lặp trong chạy: (n-1) + (n-2) + ... + 1 + 0 = n(n-1)/2
// Vẫn là O(n^2) vì n(n-1)/2 có bậc cao nhất là n^2 (hệ số 1/2 bị bỏ qua trong Big-O)
for (int i = 0; i < n; i++)
    for (int j = i + 1; j < n; j++)
        if (a[i] + a[j] == target) count++;
```
> **Lưu ý quan trọng:** dù vòng lặp trong "ngắn dần", tổng số lần chạy vẫn tỉ lệ bậc 2 với n — đây là lỗi trực giác phổ biến khiến người mới đánh giá nhầm đoạn code này là "nhanh hơn" Ví dụ 1 Đoạn B.

### Ví dụ 3 (Khó) — Phân tích đệ quy bằng cây gọi đệ quy
```cpp
// Đệ quy tính Fibonacci không nhớ kết quả (không memoization)
int fib(int n) {
    if (n <= 1) return n;
    return fib(n-1) + fib(n-2);
}
```
**Phân tích:** Mỗi lời gọi `fib(n)` sinh ra 2 lời gọi con `fib(n-1)` và `fib(n-2)`. Vẽ cây gọi đệ quy: số nút ở độ sâu k xấp xỉ 2^k → tổng số lời gọi xấp xỉ O(2^n) — **tăng theo cấp số nhân**, dù bài toán Fibonacci vốn có thể giải O(n) bằng vòng lặp hoặc O(log n) bằng ma trận (Chương 19). Đây là ví dụ kinh điển cho thấy **cài đặt sai cách** (không lưu lại kết quả đã tính) có thể biến một bài toán dễ thành không khả thi với n vừa phải (n > 40 đã đủ làm chương trình "treo" nhiều phút).

**Bài tập minh hoạ:** Xác định độ phức tạp của đoạn code sau và giải thích lý do.
```cpp
for (int i = 1; i < n; i *= 2)
    for (int j = 0; j < n; j++)
        sum += a[j];
```

**Lời giải:** Vòng lặp ngoài `i` chạy `1, 2, 4, 8, ..., n` → chạy đúng `log2(n) + 1` lần (bậc O(log n)). Vòng lặp trong luôn chạy đủ n lần, độc lập với i. Tổng độ phức tạp: O(log n) × O(n) = **O(n log n)**.

---

## 2.2. Master Theorem

**Vấn đề:** Với các thuật toán chia để trị (Divide and Conquer) như Merge Sort, công thức truy hồi có dạng `T(n) = a·T(n/b) + O(n^d)` (chia bài toán kích thước n thành a bài toán con kích thước n/b, cộng thêm chi phí O(n^d) để gộp kết quả). Cây gọi đệ quy ở đây phức tạp hơn Ví dụ 3 (mục 2.1) vì kích thước bài toán con **giảm dần theo cấp số nhân** chứ không chỉ giảm 1 đơn vị — cần công cụ tổng quát hơn để tính tổng độ phức tạp qua tất cả các tầng đệ quy.

**Phân tích vấn đề:** Tại tầng đệ quy thứ k, có `a^k` bài toán con, mỗi bài kích thước `n/b^k`, chi phí gộp mỗi bài là `O((n/b^k)^d)`. Tổng chi phí toàn tầng k là `a^k · (n/b^k)^d`. Cần so sánh xem tổng này **tăng dần** hay **giảm dần** qua các tầng để biết tầng nào "thống trị" tổng chi phí.

**Giải pháp đơn thuần:** Cộng dồn chi phí qua từng tầng bằng cấp số nhân — khả thi nhưng dễ sai sót và mất thời gian mỗi khi gặp công thức truy hồi mới.

**Khó khăn:** Làm lại phép cộng cấp số nhân từ đầu cho mọi bài toán mới là không hiệu quả — cần một **công thức đóng** áp dụng trực tiếp.

**Cách tiếp cận mới — Master Theorem:** So sánh `d` với `log_b(a)`:

$$T(n) = a \cdot T(n/b) + O(n^d)$$

- Nếu `d > log_b(a)`: chi phí gộp ở tầng gốc thống trị → **T(n) = O(n^d)**
- Nếu `d = log_b(a)`: mọi tầng đóng góp ngang nhau → **T(n) = O(n^d log n)**
- Nếu `d < log_b(a)`: số lượng bài toán con ở tầng lá thống trị → **T(n) = O(n^(log_b a))**

### Ví dụ 1 (Dễ) — Binary Search: a=1, b=2, d=0
```cpp
// T(n) = 1*T(n/2) + O(1)
int binarySearch(vector<int> &a, int target) {
    int lo = 0, hi = a.size() - 1;
    while (lo <= hi) {
        int mid = (lo + hi) / 2;
        if (a[mid] == target) return mid;
        else if (a[mid] < target) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;
}
```
**Phân tích:** a=1, b=2, d=0. `log_b(a) = log_2(1) = 0 = d` → rơi vào trường hợp 2 → **T(n) = O(log n)**.

### Ví dụ 2 (Trung bình) — Merge Sort: a=2, b=2, d=1
```cpp
void mergeSort(vector<int> &a, int l, int r) {
    if (r - l <= 1) return;
    int mid = (l + r) / 2;
    mergeSort(a, l, mid);      // T(n/2)
    mergeSort(a, mid, r);      // T(n/2)
    merge(a, l, mid, r);       // O(n) để gộp 2 mảng đã sắp xếp
}
```
**Phân tích:** a=2, b=2, d=1. `log_b(a) = log_2(2) = 1 = d` → trường hợp 2 → **T(n) = O(n log n)**.

### Ví dụ 3 (Khó) — Thuật toán nhân ma trận ngây thơ theo kiểu chia để trị: a=8, b=2, d=2
```cpp
// Chia ma trận n x n thành 4 ma trận con (n/2)x(n/2), đệ quy nhân 8 cặp con,
// cộng lại tốn O(n^2) (n = kích thước cạnh ma trận)
// T(n) = 8*T(n/2) + O(n^2)
```
**Phân tích:** a=8, b=2, d=2. `log_b(a) = log_2(8) = 3 > d=2` → trường hợp 3 → **T(n) = O(n^3)** — đúng bằng độ phức tạp nhân ma trận thông thường (không có lợi thế tốc độ). Đây là lý do thuật toán Strassen (không thuộc phạm vi chương này) phải giảm số lần gọi đệ quy `a` từ 8 xuống 7 để đạt `log_2(7) ≈ 2.807 < 3`, cải thiện độ phức tạp xuống O(n^2.807).

**Bài tập minh hoạ:** Áp dụng Master Theorem cho `T(n) = 4·T(n/2) + O(n)` — thuật toán này tương ứng ý tưởng gì?

**Lời giải:** a=4, b=2, d=1. `log_b(a) = log_2(4) = 2 > d = 1` → trường hợp 3 → **T(n) = O(n^2)**. Đây chính là độ phức tạp của thuật toán nhân đa thức/số lớn kiểu chia để trị "ngây thơ" (chia đôi mỗi số, nhân 4 cặp con) — cùng bậc với nhân trực tiếp O(n^2), không có cải thiện; thuật toán Karatsuba giảm số lần gọi đệ quy xuống 3 (a=3) để đạt O(n^1.585).

---

## 2.3. Phân tích khấu hao (Amortized Analysis)

**Vấn đề:** Một số cấu trúc dữ liệu có thao tác **thỉnh thoảng rất đắt** (O(n)) nhưng **đa số thời gian rất rẻ** (O(1)). Nếu chỉ nhìn vào trường hợp xấu nhất của 1 thao tác đơn lẻ, ta dễ đánh giá sai tổng thời gian chạy của toàn bộ chuỗi thao tác.

**Phân tích vấn đề:** Ví dụ kinh điển: `vector::push_back` trong C++ khi hết chỗ chứa (capacity đầy) phải cấp phát mảng mới lớn hơn (thường gấp đôi) và copy toàn bộ phần tử cũ sang — thao tác này tốn O(n). Nếu ta bi quan cho rằng **mọi** lần `push_back` đều tốn O(n), ta sẽ kết luận sai rằng n lần `push_back` liên tiếp tốn O(n²).

**Giải pháp đơn thuần (đánh giá bi quan từng thao tác riêng lẻ):** Lấy trường hợp xấu nhất của **1** thao tác rồi nhân với số lần gọi → `n × O(n) = O(n²)`.

**Khó khăn với giải pháp đơn thuần:** Đây là đánh giá **quá bi quan và sai thực tế** — việc cấp phát lại mảng chỉ xảy ra tại các thời điểm kích thước là luỹ thừa của 2 (`1, 2, 4, 8, ...`), tức chỉ O(log n) lần trong tổng n lần gọi, không phải mọi lần.

**Cách tiếp cận mới — Phân tích khấu hao:** Thay vì đánh giá thao tác đắt nhất, ta **tính tổng chi phí của toàn bộ chuỗi n thao tác rồi chia đều cho n** — cho ra chi phí "trung bình khấu hao" mỗi thao tác, dù từng thao tác riêng lẻ có thể không đều.

### Ví dụ 1 (Dễ) — `push_back` với cấp phát tăng gấp đôi
Với n lần `push_back` liên tiếp, tổng chi phí copy dữ liệu khi cấp phát lại là:
`1 + 2 + 4 + 8 + ... + n ≈ 2n` (tổng cấp số nhân) — đây là **tổng cho cả n lần gọi**, không phải cho 1 lần.
→ Chi phí khấu hao mỗi lần `push_back` = `2n / n = O(1)`.
```cpp
vector<int> v;
for (int i = 0; i < n; i++) v.push_back(i); // tổng chi phí O(n), khấu hao O(1)/lần
```

### Ví dụ 2 (Trung bình) — Bộ đếm nhị phân (Binary Counter)
Bài toán: tăng dần 1 số nhị phân n bit thêm 1 đơn vị, n lần liên tiếp, đếm tổng số bit bị lật (từ 0→1 hoặc 1→0).
```cpp
int counter[20] = {0}; // mảng bit, counter[0] là bit thấp nhất
long long totalFlips = 0;

void increment() {
    int i = 0;
    while (counter[i] == 1) { // lật các bit 1 liên tiếp thành 0 (mượn/carry)
        counter[i] = 0;
        totalFlips++;
        i++;
    }
    counter[i] = 1; // bit đầu tiên gặp giá trị 0 -> lật thành 1
    totalFlips++;
}
```
**Phân tích khấu hao:** Bit thấp nhất (bit 0) lật ở **mọi** lần tăng → n lần. Bit 1 lật mỗi 2 lần tăng → n/2 lần. Bit 2 lật mỗi 4 lần → n/4 lần... Tổng số lần lật qua n thao tác: `n + n/2 + n/4 + ... ≈ 2n` → chi phí khấu hao mỗi lần tăng = O(1), dù trường hợp xấu nhất của 1 lần tăng đơn lẻ (khi mọi bit đều là 1) có thể tốn O(log n) (lật hết log n bit).

### Ví dụ 3 (Khó) — Monotonic Stack: mỗi phần tử push/pop đúng 1 lần
```cpp
vector<int> nextGreaterElement(vector<int> &a) {
    int n = a.size();
    vector<int> result(n, -1);
    stack<int> st;
    for (int i = 0; i < n; i++) {
        while (!st.empty() && a[st.top()] < a[i]) { // vòng while TRÔNG giống O(n) mỗi bước
            result[st.top()] = a[i];
            st.pop();
        }
        st.push(i);
    }
    return result;
}
```
**Phân tích khấu hao:** Vòng `while` bên trong khiến đoạn code trông giống O(n²) (for lồng while). Nhưng: mỗi phần tử chỉ được `push` vào stack **đúng 1 lần** (tại vòng for chứa nó) và chỉ có thể `pop` **tối đa 1 lần** trong suốt toàn bộ quá trình. Tổng số lần push + pop qua toàn bộ thuật toán ≤ 2n → tổng độ phức tạp **O(n)**, dù nhìn qua tưởng O(n²). Đây là kỹ thuật phân tích khấu hao quan trọng nhất cần nắm trước khi học Monotonic Stack/Queue (Chương 6.4, 6.5).

**Bài tập minh hoạ:** Chứng minh độ phức tạp khấu hao của thuật toán Two Pointers sau là O(n), dù có 2 vòng lặp lồng nhau (for chứa while).
```cpp
int l = 0;
for (int r = 0; r < n; r++) {
    while (condition_violated(l, r)) l++;
    // xử lý cửa sổ [l, r]
}
```

**Lời giải:** Con trỏ `r` tăng từ 0 đến n-1 → chạy đúng n lần (vòng for ngoài). Con trỏ `l` chỉ **tăng, không bao giờ giảm**, và bị chặn trên bởi n → tổng số lần `l` tăng trong suốt toàn bộ vòng lặp ngoài tối đa là n lần (không phải n lần **cho mỗi** giá trị r). Tổng số bước của cả `r` và `l` cộng lại ≤ 2n → độ phức tạp tổng thể **O(n)**, dù cấu trúc code trông giống O(n²).

---

## Tổng kết Chương 2

- **Big-O** đo tốc độ tăng của thời gian chạy, bỏ qua hằng số — quy tắc nhanh: vòng lặp lồng k tầng → O(n^k); chia đôi mỗi bước → O(log n); đệ quy nhánh đôi không nhớ kết quả → O(2^n).
- **Master Theorem** là công cụ tính nhanh độ phức tạp của thuật toán chia để trị dạng `T(n) = a·T(n/b) + O(n^d)`, dựa vào so sánh `d` với `log_b(a)`.
- **Phân tích khấu hao** tránh đánh giá quá bi quan cho các cấu trúc dữ liệu có thao tác "thỉnh thoảng đắt" — chìa khoá là tính **tổng chi phí toàn chuỗi thao tác** rồi chia đều, thay vì nhân trường hợp xấu nhất của 1 thao tác với số lần gọi.
- **Kỹ năng cốt lõi:** khi thấy code có `while` lồng trong `for`, đừng vội kết luận O(n²) — luôn tự hỏi "biến điều khiển vòng while có bị reset về giá trị cũ ở mỗi lần lặp ngoài không, hay nó chỉ tăng/giảm đơn điệu xuyên suốt?" — đây chính là dấu hiệu nhận biết cần phân tích khấu hao thay vì nhân trực tiếp.

---

## Bài tập cuối chương — Chương 2

### PHẦN A: ĐỀ BÀI (10 bài, không kèm lời giải)

**Bài 1 — Xác định Big-O (tự thiết kế) — Dễ**
Cho 4 đoạn code (vòng lặp đơn, vòng lặp lồng độc lập, vòng lặp lồng phụ thuộc dạng tam giác, vòng lặp nhân đôi mỗi bước). Xác định độ phức tạp Big-O của từng đoạn và giải thích ngắn gọn.

**Bài 2 — Ước lượng thuật toán từ giới hạn N (tự thiết kế) — Dễ**
Cho 5 bài toán với các giới hạn N khác nhau (N ≤ 12; N ≤ 500; N ≤ 5.000; N ≤ 200.000; N ≤ 10^9). Với mỗi bài, liệt kê các độ phức tạp thuật toán được phép dùng và loại trừ các độ phức tạp chắc chắn TLE.

**Bài 3 — Two Sum (LeetCode 1, xem lại) — Dễ**
So sánh độ phức tạp giữa cách giải brute-force O(n²) và cách giải dùng hashmap O(n) — viết cả 2 cách và đo thời gian chạy thực tế với n = 10, 1.000, 100.000.

**Bài 4 — Merging Sorted Arrays (tự thiết kế theo Master Theorem) — Trung bình**
Cho công thức truy hồi `T(n) = 3·T(n/2) + O(n)`. Áp dụng Master Theorem xác định độ phức tạp, và mô tả một thuật toán chia để trị giả định phù hợp với công thức này (chia 1 bài toán thành 3 bài toán con kích thước bằng nửa).

**Bài 5 — Count Inversions (dạng đề HSG/Codeforces phổ biến) — Trung bình**
Cho mảng n số nguyên, đếm số cặp (i, j) với i < j và `a[i] > a[j]` (số nghịch thế). Yêu cầu: nêu rõ vì sao cách giải O(n²) brute-force sẽ TLE với n = 200.000, và đề xuất hướng cải thiện bằng chia để trị (Merge Sort) đạt O(n log n).

**Bài 6 — Fibonacci Numbers (CSES 1722 hoặc tương tự) — Trung bình**
Tính số Fibonacci thứ n (n có thể lên tới 10^18), kết quả lấy modulo 10^9+7. Yêu cầu: giải thích vì sao đệ quy thường (không nhớ kết quả) không khả thi, và độ phức tạp của cách giải bằng lũy thừa ma trận (liên hệ Master Theorem với thuật toán lũy thừa nhanh).

**Bài 7 — Sliding Window Maximum (CSES, xem lại Chương 6 sau) — Trung bình-Khó**
Giải thích bằng phân tích khấu hao vì sao thuật toán Monotonic Deque cho bài toán "giá trị lớn nhất trong mọi cửa sổ trượt kích thước k" đạt độ phức tạp O(n), dù cấu trúc code có `while` lồng trong `for`.

**Bài 8 — Dynamic Array Implementation (tự thiết kế) — Trung bình-Khó**
Tự cài đặt một mảng động đơn giản (không dùng `vector` có sẵn) hỗ trợ `push_back` với chiến lược cấp phát lại tăng gấp đôi khi đầy. Đo thực nghiệm: so sánh tổng thời gian chạy của n lần `push_back` khi dùng chiến lược "tăng gấp đôi" so với chiến lược "tăng thêm 1 phần tử mỗi lần đầy" — giải thích khác biệt bằng phân tích khấu hao.

**Bài 9 — Josephus Problem (xem lại Chương 3, phân tích lại độ phức tạp) — Khó**
Với cách giải dùng `queue` ở Chương 3 (Bài 11), phân tích độ phức tạp tổng thể của thuật toán qua n người, và so sánh với cách giải dùng công thức truy hồi toán học O(n) hoặc O(log n) (không bắt buộc cài đặt, chỉ cần phân tích độ phức tạp).

**Bài 10 — Karatsuba Multiplication (giới thiệu, tự thiết kế) — Khó**
Cho công thức truy hồi của thuật toán nhân số lớn Karatsuba: `T(n) = 3·T(n/2) + O(n)`. Áp dụng Master Theorem để tính độ phức tạp, so sánh với thuật toán nhân trực tiếp O(n²), và giải thích vì sao giảm số lời gọi đệ quy từ 4 xuống 3 (so với cách chia để trị "ngây thơ") lại tạo ra khác biệt lớn về độ phức tạp.

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Xác định Big-O</summary>

```cpp
// (a) O(n)
for (int i = 0; i < n; i++) sum += a[i];

// (b) O(n^2) - 2 vòng lặp lồng nhau, độc lập
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++)
        sum += a[i] * a[j];

// (c) O(n^2) - dạng tam giác, tổng n(n-1)/2 lần lặp, vẫn bậc n^2
for (int i = 0; i < n; i++)
    for (int j = i+1; j < n; j++)
        sum += a[i] + a[j];

// (d) O(log n) - i nhân đôi mỗi bước
for (int i = 1; i < n; i *= 2)
    sum += i;
```
Giải thích: (a) vòng lặp chạy đúng n lần. (b) hai vòng độc lập, mỗi vòng n lần → n×n. (c) tổng số lần lặp là cấp số cộng n(n-1)/2, nhưng Big-O chỉ giữ bậc cao nhất (n²), bỏ hệ số 1/2. (d) số lần lặp bằng số lần nhân đôi để từ 1 đạt tới n, tức log2(n).
</details>

<details>
<summary>Lời giải Bài 2 — Ước lượng thuật toán từ giới hạn N</summary>

| N | Độ phức tạp được phép | Độ phức tạp chắc chắn TLE |
|---|---|---|
| N ≤ 12 | O(N!), O(2^N · N) | — (hầu hết đều được phép ở N nhỏ) |
| N ≤ 500 | O(N^3) trở xuống | O(N!), O(2^N) |
| N ≤ 5.000 | O(N^2) trở xuống | O(N^3) trở lên |
| N ≤ 200.000 | O(N log N) trở xuống | O(N^2) trở lên |
| N ≤ 10^9 | O(log N), O(1), hoặc O(√N) | O(N) trở lên (không đủ thời gian duyệt hết N phần tử) |
</details>

<details>
<summary>Lời giải Bài 3 — Two Sum: so sánh thực nghiệm</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> bruteForce(vector<int> &nums, int target) {
    int n = nums.size();
    for (int i = 0; i < n; i++)
        for (int j = i+1; j < n; j++)
            if (nums[i] + nums[j] == target) return {i, j};
    return {-1,-1};
}

vector<int> hashmapApproach(vector<int> &nums, int target) {
    unordered_map<int,int> seen;
    for (int i = 0; i < (int)nums.size(); i++) {
        int need = target - nums[i];
        if (seen.count(need)) return {seen[need], i};
        seen[nums[i]] = i;
    }
    return {-1,-1};
}
// Với n=100000, bruteForce (O(n^2)=10^10) sẽ mất hàng chục giây,
// trong khi hashmapApproach (O(n)) chạy dưới 1 giây - chênh lệch rõ rệt khi đo thời gian thực tế.
```
</details>

<details>
<summary>Lời giải Bài 4 — T(n) = 3T(n/2) + O(n)</summary>

a=3, b=2, d=1. `log_b(a) = log_2(3) ≈ 1.585 > d = 1` → trường hợp 3 → **T(n) = O(n^1.585)**. Một thuật toán chia để trị giả định phù hợp: chia mảng thành 2 nửa, nhưng thay vì đệ quy đủ trên cả 2 nửa một cách độc lập hoàn toàn, thuật toán tận dụng lại 1 phần kết quả trung gian để chỉ cần 3 lời gọi đệ quy thay vì 4 (tương tự tinh thần thuật toán Karatsuba ở Bài 10, dù bài toán cụ thể khác nhau).
</details>

<details>
<summary>Lời giải Bài 5 — Count Inversions</summary>

Với n = 200.000, brute-force O(n²) = 4×10^10 phép tính → theo bảng ước lượng (Chương 1), vượt xa 10^8 phép tính/giây cho phép trong 1-2 giây → chắc chắn TLE. Hướng cải thiện: dùng Merge Sort đếm nghịch thế — trong bước "merge" 2 nửa đã sắp xếp, mỗi khi lấy phần tử từ nửa phải trước phần tử còn lại ở nửa trái, toàn bộ phần tử còn lại ở nửa trái đều tạo thành nghịch thế với phần tử đó — đếm dồn trong quá trình merge, không cần so sánh từng cặp. Công thức truy hồi T(n) = 2T(n/2) + O(n) → theo Master Theorem (mục 2.2, giống Merge Sort) → **O(n log n)**, khả thi với n = 200.000.
</details>

<details>
<summary>Lời giải Bài 6 — Fibonacci Numbers</summary>

Đệ quy thường (không nhớ kết quả) có độ phức tạp O(2^n) (xem Ví dụ 3, mục 2.1) — với n tới 10^18, hoàn toàn không khả thi (không thể chạy dù chỉ n=90). Ngay cả DP tuyến tính O(n) cũng không khả thi vì n quá lớn. Giải pháp: biểu diễn công thức truy hồi Fibonacci dưới dạng nhân ma trận 2×2, dùng lũy thừa nhanh (Chương 17) để tính `M^n` trong O(log n) phép nhân ma trận, mỗi phép nhân ma trận 2×2 tốn O(1) (hằng số) → tổng độ phức tạp **O(log n)** — đủ nhanh cho n = 10^18. Đây là ứng dụng thực tế của tư duy "chia đôi mỗi bước → O(log n)" đã học ở Ví dụ 1, mục 2.2 (Binary Search) kết hợp với lũy thừa nhanh.
</details>

<details>
<summary>Lời giải Bài 7 — Sliding Window Maximum (Monotonic Deque)</summary>

Tương tự phân tích Monotonic Stack ở Ví dụ 3 (mục 2.3): mỗi phần tử trong mảng chỉ được đẩy vào deque **đúng 1 lần** (khi vòng for chính duyệt tới nó), và chỉ có thể bị loại khỏi deque (từ đầu hoặc từ cuối) **tối đa 1 lần** trong suốt toàn bộ quá trình chạy. Dù vòng `while` bên trong `for` trông giống có thể lặp nhiều lần cho mỗi bước của `for`, tổng số lần thao tác push/pop trên deque qua toàn bộ thuật toán bị chặn bởi 2n → độ phức tạp tổng thể **O(n)**, không phải O(n·k) như brute-force.
</details>

<details>
<summary>Lời giải Bài 8 — Dynamic Array Implementation</summary>

```cpp
struct MyVector {
    int *data;
    int size, capacity;
    MyVector() : size(0), capacity(1) { data = new int[capacity]; }

    void push_back(int x) {
        if (size == capacity) {
            capacity *= 2; // chiến lược tăng gấp đôi
            int *newData = new int[capacity];
            for (int i = 0; i < size; i++) newData[i] = data[i];
            delete[] data;
            data = newData;
        }
        data[size++] = x;
    }
};
// Nếu thay "capacity *= 2" bằng "capacity += 1" (tăng thêm 1 mỗi lần đầy):
// mỗi lần push_back khi đầy đều phải copy toàn bộ mảng cũ (kích thước tăng dần đều)
// -> tổng chi phí copy qua n lần: 1+2+3+...+n = O(n^2), khấu hao O(n)/lần - CHẬM HƠN NHIỀU
// so với chiến lược tăng gấp đôi (tổng chi phí O(n), khấu hao O(1)/lần).
```
Kết luận: chiến lược tăng gấp đôi là lý do `vector` trong C++ luôn đạt push_back khấu hao O(1); nếu cấp phát lại theo kiểu cộng tuyến tính, độ phức tạp khấu hao sẽ suy biến thành O(n).
</details>

<details>
<summary>Lời giải Bài 9 — Josephus Problem: phân tích độ phức tạp</summary>

Với cách giải dùng `queue`: mỗi người được đưa vào/lấy ra khỏi hàng đợi tối đa 2 lần (1 lần "sống sót" được đẩy về cuối, 1 lần bị loại) → tổng độ phức tạp **O(n)**, khá hiệu quả cho bài toán mô phỏng trực tiếp. Tuy nhiên nếu chỉ cần biết **vị trí người sống sót cuối cùng** (không cần in ra toàn bộ thứ tự bị loại), tồn tại công thức truy hồi toán học `J(n) = (J(n-1) + k) mod n` (với k là bước đếm, ở đây k=2) tính trực tiếp trong O(n) hoặc thậm chí O(log n) nếu dùng biến thể bit-shift khi k=2 — nhanh hơn nhiều so với mô phỏng khi chỉ cần đáp án cuối cùng, không cần toàn bộ thứ tự loại.
</details>

<details>
<summary>Lời giải Bài 10 — Karatsuba Multiplication</summary>

a=3, b=2, d=1. `log_b(a) = log_2(3) ≈ 1.585 > d=1` → trường hợp 3 → **T(n) = O(n^1.585)**, tốt hơn đáng kể so với O(n²) của nhân trực tiếp khi n lớn (ví dụ nhân 2 số hàng nghìn chữ số). So sánh với chia để trị "ngây thơ" 4 lời gọi đệ quy (a=4, b=2, d=1 → `log_2(4)=2` → T(n)=O(n²), không cải thiện gì so với nhân trực tiếp): việc giảm từ 4 xuống 3 lời gọi đệ quy làm giảm `log_b(a)` từ 2 xuống 1.585 — theo Master Theorem, đây chính là yếu tố quyết định bậc độ phức tạp cuối cùng, minh chứng rõ ràng cho việc **giảm số lời gọi đệ quy** (dù chỉ 1 lời gọi) có thể tạo ra khác biệt rất lớn về hiệu năng khi n lớn.
</details>
