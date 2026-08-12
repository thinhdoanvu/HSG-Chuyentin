# Chương 4: Sắp xếp & Tìm kiếm nhị phân

---

## 4.1. Sắp xếp với Comparator tuỳ chỉnh

**Vấn đề:** Hàm `sort()` mặc định chỉ sắp xếp tăng dần theo phép so sánh `<` tự nhiên. Nhưng đề bài thường yêu cầu sắp xếp theo **nhiều tiêu chí** (VD: điểm giảm dần, nếu bằng điểm thì tên tăng dần theo alphabet) hoặc theo tiêu chí **không tự nhiên** (VD: sắp xếp các số theo giá trị tuyệt đối, hoặc theo số lượng ước số).

### Cách viết Comparator

```cpp
vector<pair<int,string>> hs = {{80,"Binh"}, {90,"An"}, {80,"An"}};

// Cách 1: dùng lambda function (phổ biến nhất trong thi đấu)
sort(hs.begin(), hs.end(), [](const pair<int,string> &a, const pair<int,string> &b) {
    if (a.first != b.first) return a.first > b.first; // điểm giảm dần
    return a.second < b.second;                          // tên tăng dần nếu bằng điểm
});

// Cách 2: dùng hàm so sánh riêng (khi comparator phức tạp, tái sử dụng nhiều nơi)
bool cmp(const pair<int,string> &a, const pair<int,string> &b) {
    if (a.first != b.first) return a.first > b.first;
    return a.second < b.second;
}
// sort(hs.begin(), hs.end(), cmp);
```
```python
hs = [(80,"Binh"), (90,"An"), (80,"An")]
hs.sort(key=lambda p: (-p[0], p[1]))  # điểm giảm dần (đảo dấu), tên tăng dần
```

> **Quy tắc bắt buộc của comparator trong C++:** hàm phải định nghĩa quan hệ **thứ tự nghiêm ngặt** (strict weak ordering) — trả về `true` nếu a **thực sự đứng trước** b, và với a = b phải trả về `false`. Viết sai quy tắc này (VD: dùng `<=` thay vì `<`) gây **undefined behavior**, thường biểu hiện thành crash hoặc kết quả sai không rõ nguyên nhân khi n lớn.

### Ví dụ 1 (Dễ) — Sắp xếp giảm dần đơn giản
```cpp
vector<int> a = {5, 2, 8, 1, 9};
sort(a.begin(), a.end(), greater<int>()); // {9, 8, 5, 2, 1}
```
```python
a = [5, 2, 8, 1, 9]
a.sort(reverse=True)
```

### Ví dụ 2 (Trung bình) — Sắp xếp theo tỉ lệ (không so sánh trực tiếp được vì số thực)
Bài toán: sắp xếp các "vật" theo tỉ lệ giá trị/khối lượng giảm dần (dùng trong bài Fractional Knapsack, Chương 25). Nếu chia trực tiếp để so sánh, dễ gặp sai số dấu phẩy động — nên **so sánh chéo bằng nhân** để giữ số nguyên.
```cpp
struct Item { long long value, weight; };
sort(items.begin(), items.end(), [](const Item &a, const Item &b) {
    // so sánh a.value/a.weight > b.value/b.weight bằng nhân chéo, tránh chia (tránh sai số)
    return a.value * b.weight > b.value * a.weight;
});
```
```python
items.sort(key=lambda it: -it[0] / it[1])  # Python: chia trực tiếp thường đủ chính xác cho hầu hết bài
```

### Ví dụ 3 (Khó) — Sắp xếp để ghép chuỗi tạo số lớn nhất
Bài toán: cho danh sách các số (dạng chuỗi), ghép chúng lại theo thứ tự nào đó để tạo thành số lớn nhất có thể. Ví dụ: `["3", "30", "34", "5", "9"]` → ghép thành `"9534330"`.

**Phân tích:** Không thể so sánh 2 số bằng giá trị số học thông thường (`"30" > "3"` nhưng thứ tự đúng lại là `"3"` trước `"30"` vì `"330" > "303"`). Cần **comparator tuỳ chỉnh**: so sánh `a+b` với `b+a` (nối chuỗi).
```cpp
string largestNumber(vector<string> &nums) {
    sort(nums.begin(), nums.end(), [](const string &a, const string &b) {
        return a + b > b + a; // nếu "ab" > "ba" thì a đứng trước b
    });
    string result;
    for (auto &s : nums) result += s;
    // Xử lý trường hợp toàn số 0: kết quả "000" phải trả về "0"
    if (result[0] == '0') return "0";
    return result;
}
```
```python
from functools import cmp_to_key

def largest_number(nums):
    def compare(a, b):
        if a + b > b + a: return -1
        if a + b < b + a: return 1
        return 0
    nums.sort(key=cmp_to_key(compare))
    result = "".join(nums)
    return "0" if result[0] == '0' else result
```

**Bài tập minh hoạ:** Cho danh sách các cuộc họp, mỗi cuộc họp có (giờ bắt đầu, giờ kết thúc). Sắp xếp và tìm số phòng họp tối thiểu cần để tổ chức tất cả các cuộc họp mà không bị trùng giờ trong cùng 1 phòng.

**Lời giải:** Tách thành 2 danh sách sự kiện (bắt đầu +1, kết thúc -1), sắp xếp theo thời gian (kết thúc xử lý trước nếu trùng giờ), quét và tìm giá trị lớn nhất — kỹ thuật này sẽ gặp lại ở "sweep line" (Chương 21).
```cpp
int minMeetingRooms(vector<pair<int,int>> &meetings) {
    vector<pair<int,int>> events; // (thời điểm, loại: -1 kết thúc, +1 bắt đầu)
    for (auto &[s, e] : meetings) { events.push_back({s, 1}); events.push_back({e, -1}); }
    sort(events.begin(), events.end()); // (t,-1) đứng trước (t,1) tự nhiên vì -1 < 1
    int cur = 0, maxRooms = 0;
    for (auto &[t, type] : events) { cur += type; maxRooms = max(maxRooms, cur); }
    return maxRooms;
}
```

---

## 4.2. Tìm kiếm nhị phân cổ điển

**Nêu bài toán:** Cho mảng đã sắp xếp `a` gồm n phần tử, tìm vị trí của giá trị `x` (hoặc xác nhận không tồn tại).

**Phân tích:** Nếu mảng chưa sắp xếp, phải duyệt tuyến tính O(n). Nhưng vì mảng **đã có thứ tự**, tại mỗi bước ta có thể loại bỏ **một nửa** không gian tìm kiếm dựa vào so sánh với phần tử ở giữa.

**Giải pháp đơn thuần:** Duyệt tuyến tính O(n) — luôn đúng nhưng không tận dụng được tính chất đã sắp xếp của mảng.

**Khó khăn:** Với n = 10^9 (ví dụ tìm trong không gian giá trị rất lớn), O(n) không khả thi trong giới hạn thời gian.

**Cách tiếp cận mới:** Tìm kiếm nhị phân — mỗi bước thu hẹp không gian tìm kiếm còn **một nửa**, đạt độ phức tạp O(log n).

### Cài đặt chuẩn (2 biến thể quan trọng)
```cpp
// Biến thể 1: tìm kiếm nhị phân cơ bản, trả về chỉ số hoặc -1 nếu không tồn tại
int binarySearch(vector<int> &a, int target) {
    int lo = 0, hi = (int)a.size() - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2; // tránh tràn số so với (lo+hi)/2 khi lo,hi rất lớn
        if (a[mid] == target) return mid;
        else if (a[mid] < target) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;
}

// Biến thể 2: dùng STL có sẵn - lower_bound/upper_bound (khuyên dùng trong thi đấu)
auto it = lower_bound(a.begin(), a.end(), target); // vị trí đầu tiên >= target
auto it2 = upper_bound(a.begin(), a.end(), target); // vị trí đầu tiên > target
bool exists = (it != a.end() && *it == target);
```
```python
import bisect

def binary_search(a, target):
    lo, hi = 0, len(a) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if a[mid] == target: return mid
        elif a[mid] < target: lo = mid + 1
        else: hi = mid - 1
    return -1

# Dùng module bisect (khuyên dùng)
pos = bisect.bisect_left(a, target)   # vị trí đầu tiên >= target
exists = pos < len(a) and a[pos] == target
```

> **Lỗi kinh điển:** viết `int mid = (lo + hi) / 2;` khi `lo, hi` là số nguyên lớn (gần giới hạn `int`) có thể **tràn số** khi cộng `lo + hi`. Luôn viết `mid = lo + (hi - lo) / 2` để an toàn.

### Ví dụ 1 (Dễ) — Tìm chỉ số xuất hiện đầu tiên/cuối cùng của 1 giá trị trong mảng có phần tử trùng lặp
```cpp
pair<int,int> findFirstLast(vector<int> &a, int target) {
    int first = lower_bound(a.begin(), a.end(), target) - a.begin();
    int last = upper_bound(a.begin(), a.end(), target) - a.begin() - 1;
    if (first >= (int)a.size() || a[first] != target) return {-1, -1};
    return {first, last};
}
```
```python
def find_first_last(a, target):
    first = bisect.bisect_left(a, target)
    last = bisect.bisect_right(a, target) - 1
    if first >= len(a) or a[first] != target:
        return (-1, -1)
    return (first, last)
```

### Ví dụ 2 (Trung bình) — Tìm kiếm nhị phân trên mảng xoay (Rotated Sorted Array)
Bài toán: mảng đã sắp xếp bị "xoay" tại 1 điểm không xác định (VD: `[4,5,6,7,0,1,2]`). Tìm target trong O(log n).

**Phân tích:** Mảng không còn đơn điệu toàn cục, nhưng **luôn có ít nhất 1 nửa (trái hoặc phải của `mid`) vẫn đơn điệu** — kiểm tra nửa nào đơn điệu để quyết định thu hẹp về bên nào.
```cpp
int searchRotated(vector<int> &a, int target) {
    int lo = 0, hi = a.size() - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] == target) return mid;
        if (a[lo] <= a[mid]) { // nửa trái đơn điệu
            if (a[lo] <= target && target < a[mid]) hi = mid - 1;
            else lo = mid + 1;
        } else {                 // nửa phải đơn điệu
            if (a[mid] < target && target <= a[hi]) lo = mid + 1;
            else hi = mid - 1;
        }
    }
    return -1;
}
```
```python
def search_rotated(a, target):
    lo, hi = 0, len(a) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if a[mid] == target: return mid
        if a[lo] <= a[mid]:
            if a[lo] <= target < a[mid]: hi = mid - 1
            else: lo = mid + 1
        else:
            if a[mid] < target <= a[hi]: lo = mid + 1
            else: hi = mid - 1
    return -1
```

### Ví dụ 3 (Khó) — Tìm kiếm nhị phân trên hàm không tường minh (Implicit Binary Search)
Bài toán: cho n giếng nước và m trạm bơm, xây trạm bơm để giếng gần nhất luôn trong bán kính r. Tìm bán kính r **nhỏ nhất** để mọi giếng đều được phủ. Ở đây không có "mảng" tường minh để tìm kiếm — mà là tìm kiếm trên **không gian đáp án liên tục** (số thực).

**Phân tích:** Nếu bán kính r khả thi (phủ được hết giếng), thì mọi r' > r chắc chắn cũng khả thi (tính đơn điệu) → áp dụng nhị phân, kiểm tra `check(r)` bằng cách khác (không so sánh mảng), sẽ học chi tiết ở mục 4.3.

**Bài tập minh hoạ:** Tìm phần tử "đỉnh núi" (peak element) trong mảng — phần tử lớn hơn cả 2 phần tử lân cận (biên coi như -∞).

**Lời giải:** Dù mảng không sắp xếp toàn cục, luôn tồn tại đỉnh núi và có thể tìm bằng nhị phân: nếu `a[mid] < a[mid+1]`, đỉnh núi chắc chắn nằm bên phải `mid` (dốc đang lên); ngược lại nằm ở `mid` hoặc bên trái.
```cpp
int findPeakElement(vector<int> &a) {
    int lo = 0, hi = a.size() - 1;
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] < a[mid+1]) lo = mid + 1;
        else hi = mid;
    }
    return lo;
}
```
```python
def find_peak_element(a):
    lo, hi = 0, len(a) - 1
    while lo < hi:
        mid = (lo + hi) // 2
        if a[mid] < a[mid+1]: lo = mid + 1
        else: hi = mid
    return lo
```

---

## 4.3. Tìm kiếm nhị phân trên đáp án (Binary Search on Answer)

**Nêu bài toán:** Có n cuốn sách với số trang `p[i]`, cần chia cho k người sao cho mỗi người nhận một đoạn sách **liên tiếp**, và người nhận nhiều trang nhất phải nhận **ít trang nhất có thể** (minimize the maximum).

**Phân tích:** Đây **không phải** bài toán tìm kiếm trên mảng có sẵn — mà là bài toán **tối ưu hoá**. Nhận thấy: nếu với ngưỡng trang tối đa X ta có thể chia sách sao cho không ai vượt quá X trang (dùng ≤ k người), thì **mọi X' > X** chắc chắn cũng chia được (tính đơn điệu). Đây chính là điều kiện để áp dụng tìm kiếm nhị phân — không tìm trên mảng, mà tìm trên **không gian giá trị đáp án**.

**Giải pháp đơn thuần:** Thử lần lượt từng giá trị X từ nhỏ đến lớn, kiểm tra tính khả thi — độ phức tạp O(tổng_trang × n), quá chậm khi tổng trang lớn.

**Khó khăn:** Với tổng trang có thể lên tới 10^9, thử tuần tự từng giá trị là bất khả thi.

**Cách tiếp cận mới:** Nhị phân trên **giá trị đáp án X**: với mỗi X thử nghiệm, dùng hàm `check(X)` (thường tham lam, O(n)) để kiểm tra tính khả thi, thu hẹp khoảng nhị phân dựa vào kết quả `check`.

### Cách nhận diện bài toán binary search on answer
- Đề bài hỏi "giá trị nhỏ nhất/lớn nhất sao cho..." (minimize the maximum / maximize the minimum).
- Tồn tại hàm kiểm tra `check(x)` chạy nhanh (thường O(n) hoặc O(n log n)).
- Tính chất **đơn điệu**: nếu x khả thi thì mọi giá trị "tốt hơn x" cũng khả thi.

### Code khung mẫu
```cpp
long long lo = LOWER_BOUND, hi = UPPER_BOUND, ans = -1;
while (lo <= hi) {
    long long mid = lo + (hi - lo) / 2;
    if (check(mid)) {       // mid khả thi -> thử giá trị nhỏ hơn (nếu đang tìm min)
        ans = mid;
        hi = mid - 1;
    } else {
        lo = mid + 1;
    }
}
```
```python
lo, hi, ans = LOWER_BOUND, UPPER_BOUND, -1
while lo <= hi:
    mid = (lo + hi) // 2
    if check(mid):
        ans = mid
        hi = mid - 1
    else:
        lo = mid + 1
```

### Ví dụ 1 (Dễ) — Koko ăn chuối (Koko Eating Bananas)
Bài toán: n đống chuối, đống i có `piles[i]` quả. Koko ăn với tốc độ k quả/giờ (mỗi giờ chỉ ăn từ 1 đống, nếu đống ít hơn k thì ăn hết đống đó và nghỉ phần giờ còn lại). Có h giờ. Tìm tốc độ k **nhỏ nhất** để ăn hết trong h giờ.
```cpp
bool canFinish(vector<int> &piles, int k, int h) {
    long long hours = 0;
    for (int p : piles) hours += (p + k - 1) / k; // ceil(p / k)
    return hours <= h;
}
int minEatingSpeed(vector<int> &piles, int h) {
    int lo = 1, hi = *max_element(piles.begin(), piles.end()), ans = hi;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (canFinish(piles, mid, h)) { ans = mid; hi = mid - 1; }
        else lo = mid + 1;
    }
    return ans;
}
```
```python
def min_eating_speed(piles, h):
    def can_finish(k):
        return sum((p + k - 1) // k for p in piles) <= h
    lo, hi, ans = 1, max(piles), max(piles)
    while lo <= hi:
        mid = (lo + hi) // 2
        if can_finish(mid): ans, hi = mid, mid - 1
        else: lo = mid + 1
    return ans
```

### Ví dụ 2 (Trung bình) — Chia sách cho k người (Book Allocation / Split Array Largest Sum)
```cpp
bool canAllocate(vector<int> &pages, int k, long long maxPages) {
    int people = 1;
    long long current = 0;
    for (int p : pages) {
        if (p > maxPages) return false; // 1 cuốn đã vượt quá ngưỡng -> không khả thi
        if (current + p > maxPages) { people++; current = p; }
        else current += p;
    }
    return people <= k;
}
long long minLargestSum(vector<int> &pages, int k) {
    long long lo = *max_element(pages.begin(), pages.end());
    long long hi = accumulate(pages.begin(), pages.end(), 0LL);
    long long ans = hi;
    while (lo <= hi) {
        long long mid = lo + (hi - lo) / 2;
        if (canAllocate(pages, k, mid)) { ans = mid; hi = mid - 1; }
        else lo = mid + 1;
    }
    return ans;
}
```
```python
def min_largest_sum(pages, k):
    def can_allocate(max_pages):
        people, current = 1, 0
        for p in pages:
            if p > max_pages: return False
            if current + p > max_pages:
                people += 1; current = p
            else:
                current += p
        return people <= k

    lo, hi, ans = max(pages), sum(pages), sum(pages)
    while lo <= hi:
        mid = (lo + hi) // 2
        if can_allocate(mid): ans, hi = mid, mid - 1
        else: lo = mid + 1
    return ans
```

### Ví dụ 3 (Khó) — Nhị phân trên số thực (giếng nước và trạm bơm, tiếp Ví dụ 3 mục 4.2)
Với đáp án là số thực, thay vì dùng điều kiện dừng `lo <= hi`, dùng **số vòng lặp cố định** (vì độ chính xác số thực không có "phần tử liền kề" rõ ràng như số nguyên).
```cpp
double lo = 0.0, hi = 1e9;
for (int iter = 0; iter < 100; iter++) { // 100 vòng đủ để đạt độ chính xác ~10^-30
    double mid = (lo + hi) / 2.0;
    if (check(mid)) hi = mid;
    else lo = mid;
}
// đáp án xấp xỉ lo (hoặc hi, giá trị 2 biến đã hội tụ đủ gần nhau)
```
```python
lo, hi = 0.0, 1e9
for _ in range(100):
    mid = (lo + hi) / 2
    if check(mid): hi = mid
    else: lo = mid
```

**Bài tập minh hoạ:** Cho mảng n số nguyên dương và số nguyên m, tìm cách chia mảng thành **đúng m đoạn con liên tiếp** sao cho tổng lớn nhất trong các đoạn là **nhỏ nhất có thể**.

**Lời giải:** Đây chính là bài "Split Array Largest Sum" — về bản chất giống hệt Ví dụ 2 (chia sách), chỉ khác tên gọi. Đây là minh chứng cho kỹ năng "nhận diện bài toán lõi" đã học ở Chương 1 — cùng 1 kỹ thuật, nhiều lớp vỏ bài toán khác nhau. Code giống hệt `minLargestSum` ở trên, thay `pages` bằng mảng đề cho, `k` bằng `m`.

---

## 4.4. Tìm kiếm tam phân (Ternary Search)

**Nêu bài toán:** Cho một hàm số `f(x)` có dạng **đơn mốt** (unimodal) — tăng dần rồi giảm dần (hoặc ngược lại), tìm giá trị x tại đó `f(x)` đạt cực trị (max hoặc min).

**Phân tích:** Tìm kiếm nhị phân dựa vào so sánh **bằng/khác** một target cụ thể — không áp dụng trực tiếp được cho bài toán tìm cực trị. Cần một biến thể khác: chia không gian tìm kiếm thành **3 phần** thay vì 2, so sánh giá trị hàm tại 2 điểm chia để loại bỏ 1/3 không gian mỗi bước.

**Giải pháp đơn thuần:** Duyệt qua tất cả giá trị x có thể, tính f(x) và so sánh — O(n), không khả thi khi không gian x rất lớn hoặc là số thực.

**Cách tiếp cận mới:** Ternary Search — mỗi bước chọn 2 điểm `m1, m2` chia đoạn `[lo, hi]` thành 3 phần bằng nhau, so sánh `f(m1)` với `f(m2)` để xác định cực trị nằm ở 2/3 đoạn nào, loại bỏ 1/3 còn lại.

### Code khung mẫu (tìm cực đại của hàm đơn mốt)
```cpp
double ternarySearchMax(function<double(double)> f, double lo, double hi) {
    for (int iter = 0; iter < 100; iter++) {
        double m1 = lo + (hi - lo) / 3;
        double m2 = hi - (hi - lo) / 3;
        if (f(m1) < f(m2)) lo = m1; // cực đại không nằm bên trái m1
        else hi = m2;                // cực đại không nằm bên phải m2
    }
    return (lo + hi) / 2;
}
```
```python
def ternary_search_max(f, lo, hi):
    for _ in range(100):
        m1 = lo + (hi - lo) / 3
        m2 = hi - (hi - lo) / 3
        if f(m1) < f(m2): lo = m1
        else: hi = m2
    return (lo + hi) / 2
```

> **Điều kiện bắt buộc:** hàm phải thực sự **đơn mốt** (unimodal) trên đoạn `[lo, hi]` — chỉ có đúng 1 đỉnh (hoặc đáy). Nếu hàm có nhiều cực trị cục bộ, ternary search có thể cho kết quả sai — khác với binary search on answer (mục 4.3) chỉ cần tính **đơn điệu** của hàm kiểm tra, ternary search cần tính chất mạnh hơn.

### Ví dụ 1 (Dễ) — Tìm cực tiểu của hàm bậc 2
```cpp
double f(double x) { return (x - 3) * (x - 3) + 5; } // parabol, cực tiểu tại x=3
// ternarySearchMin tương tự ternarySearchMax nhưng đảo điều kiện so sánh
```

### Ví dụ 2 (Trung bình) — Tìm điểm trên đoạn thẳng gần 1 điểm cho trước nhất
Bài toán: cho 1 điểm P và 1 đoạn thẳng AB, tìm khoảng cách ngắn nhất từ P đến 1 điểm bất kỳ trên đoạn AB.
```cpp
double distFromPointOnSegment(double t, Point A, Point B, Point P) {
    // t trong [0,1] tham số hoá điểm trên đoạn AB
    double x = A.x + t * (B.x - A.x), y = A.y + t * (B.y - A.y);
    return sqrt((x - P.x)*(x - P.x) + (y - P.y)*(y - P.y));
}
// Hàm khoảng cách theo t là đơn mốt (giảm dần rồi tăng dần) -> áp dụng ternary search tìm t tối ưu
```

### Ví dụ 3 (Khó) — Ternary Search trên mảng số nguyên rời rạc (không phải hàm liên tục)
Với mảng số nguyên đơn mốt (tăng rồi giảm), có thể dùng ternary search rời rạc, nhưng **binary search kiểu "tìm đỉnh núi"** (đã học ở Ví dụ 3, mục 4.2 — `findPeakElement`) thường đơn giản và hiệu quả hơn cho trường hợp rời rạc. Đây là lưu ý quan trọng: **không phải mọi bài toán "tìm cực trị" đều cần ternary search** — nếu chuyển được về dạng so sánh 2 phần tử liền kề như `findPeakElement`, nên ưu tiên binary search vì cài đặt đơn giản hơn và không có vấn đề độ chính xác số thực.

**Bài tập minh hoạ:** Cho hàm chi phí `cost(x) = a*x^2 + b*x + c` với a > 0 (parabol lõm lên trên), tìm giá trị x nguyên để `cost(x)` nhỏ nhất.

**Lời giải:** Vì hàm là đơn mốt (chỉ có 1 cực tiểu), áp dụng ternary search trên số nguyên (dùng `lo < hi - 2` làm điều kiện dừng để tránh vòng lặp vô hạn do làm tròn số nguyên):
```cpp
long long ternarySearchIntMin(function<long long(long long)> cost, long long lo, long long hi) {
    while (hi - lo > 2) {
        long long m1 = lo + (hi - lo) / 3;
        long long m2 = hi - (hi - lo) / 3;
        if (cost(m1) < cost(m2)) hi = m2;
        else lo = m1;
    }
    long long best = lo;
    for (long long x = lo; x <= hi; x++) if (cost(x) < cost(best)) best = x; // duyệt nốt đoạn nhỏ còn lại
    return best;
}
```

---

## Tổng kết Chương 4

- **Sắp xếp với comparator:** luôn nhớ quy tắc "strict weak ordering" — với 2 phần tử bằng nhau, comparator phải trả về `false`.
- **Tìm kiếm nhị phân cổ điển:** áp dụng khi mảng đã (hoặc có thể coi là) đơn điệu — không chỉ dùng để tìm target, mà còn tìm biên (`lower_bound`/`upper_bound`), tìm đỉnh núi, tìm điểm xoay.
- **Binary Search on Answer:** kỹ thuật quan trọng nhất chương — nhận diện qua cụm từ "giá trị nhỏ nhất/lớn nhất sao cho...", cần hàm `check()` chạy nhanh và có tính đơn điệu.
- **Ternary Search:** dùng cho hàm đơn mốt liên tục (số thực) — cẩn trọng: nhiều bài tưởng cần ternary search thực chất có thể giải bằng binary search đơn giản hơn nếu chuyển được về dạng rời rạc so sánh 2 lân cận.
- **Kỹ năng cốt lõi:** khi thấy đề bài có dạng "tối thiểu hoá giá trị lớn nhất" hoặc "tối đa hoá giá trị nhỏ nhất", phản xạ đầu tiên nên là kiểm tra tính đơn điệu để áp dụng Binary Search on Answer — đây là một trong những kỹ thuật xuất hiện nhiều nhất trong đề thi HSG và Codeforces Div 2.

---

## Bài tập cuối chương — Chương 4

### PHẦN A: ĐỀ BÀI (14 bài, không kèm lời giải)

**Bài 1 — Sort Array by Parity (LeetCode 905) — Dễ**
Cho mảng số nguyên, sắp xếp sao cho tất cả số chẵn đứng trước, số lẻ đứng sau (không yêu cầu giữ thứ tự trong từng nhóm).

**Bài 2 — Merge Intervals (LeetCode 56) — Trung bình**
Cho danh sách các khoảng (interval), gộp tất cả các khoảng chồng lấn nhau thành khoảng lớn nhất có thể.

**Bài 3 — Custom Sort String (LeetCode 791) — Trung bình**
Cho 1 chuỗi `order` định nghĩa thứ tự ưu tiên ký tự, sắp xếp chuỗi `s` theo đúng thứ tự đó (ký tự không có trong `order` đặt ở cuối, giữ nguyên thứ tự tương đối).

**Bài 4 — Binary Search (LeetCode 704) — Dễ**
Cài đặt tìm kiếm nhị phân cơ bản trên mảng đã sắp xếp, trả về chỉ số hoặc -1.

**Bài 5 — Find First and Last Position of Element in Sorted Array (LeetCode 34) — Trung bình**
Cho mảng đã sắp xếp có phần tử trùng lặp, tìm vị trí xuất hiện đầu tiên và cuối cùng của target.

**Bài 6 — Search in Rotated Sorted Array (LeetCode 33) — Trung bình**
Tìm target trong mảng đã sắp xếp nhưng bị xoay tại 1 điểm không xác định.

**Bài 7 — Factory Machines (CSES 1620) — Trung bình**
Có n máy, máy thứ i cần `t[i]` phút để sản xuất 1 sản phẩm (có thể sản xuất song song nhiều sản phẩm liên tiếp). Tìm thời gian tối thiểu để tổng cộng tất cả các máy sản xuất được ít nhất k sản phẩm.

**Bài 8 — Array Division (CSES 1085) — Trung bình**
Cho mảng n số nguyên, chia thành k đoạn con liên tiếp, tìm cách chia để tổng lớn nhất trong các đoạn là nhỏ nhất có thể (giống Ví dụ 2, mục 4.3 nhưng đổi tên biến).

**Bài 9 — Aggressive Cows (dạng đề phổ biến trên nhiều OJ, gốc từ POI) — Trung bình-Khó**
Cho n vị trí chuồng bò trên 1 đường thẳng, đặt c con bò vào c chuồng khác nhau sao cho khoảng cách nhỏ nhất giữa 2 con bò bất kỳ là **lớn nhất có thể**. (Lưu ý: đây là bài toán "maximize the minimum" — dạng đối ng�ứng với Ví dụ 1, 2 ở mục 4.3.)

**Bài 10 — Median of Two Sorted Arrays (LeetCode 4) — Khó**
Cho 2 mảng đã sắp xếp kích thước m và n, tìm trung vị (median) của mảng gộp cả 2 mà không cần gộp thực sự, độ phức tạp yêu cầu O(log(min(m,n))).

**Bài 11 — Peak Index in a Mountain Array (LeetCode 852) — Dễ-Trung bình**
Cho mảng dạng "núi" (tăng dần rồi giảm dần), tìm chỉ số của đỉnh núi.

**Bài 12 — Capacity To Ship Packages Within D Days (LeetCode 1011) — Trung bình**
Có n kiện hàng cần vận chuyển trong đúng D ngày (theo thứ tự, mỗi ngày chở 1 số kiện liên tiếp không vượt quá sức chứa tàu). Tìm sức chứa tối thiểu của tàu.

**Bài 13 — Tìm cực trị hàm đơn mốt bằng Ternary Search (tự thiết kế) — Trung bình-Khó**
Cho hàm `f(x) = |x - 5| + |x - 10| + (x-7)^2 / 100` trên đoạn [0, 20]. Dùng ternary search tìm giá trị x (số thực, sai số cho phép 10^-6) để f(x) đạt giá trị nhỏ nhất.

**Bài 14 — Koko Eating Bananas biến thể nhiều ràng buộc (tự thiết kế) — Khó**
Biến thể Ví dụ 1 (mục 4.3): Koko phải ăn hết trong h giờ, nhưng có thêm ràng buộc mỗi giờ chỉ được ăn tối đa từ 1 trong 2 đống "ưa thích" cho trước (nếu cả 2 đống ưa thích đã hết, mới được ăn đống khác). Tìm tốc độ nhỏ nhất k để vẫn hoàn thành đúng hạn. (Bài rèn kỹ năng viết hàm `check()` phức tạp hơn cho binary search on answer.)

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Sort Array by Parity</summary>

Dùng kỹ thuật 2 con trỏ (Chương 6 sẽ học sâu hơn), hoặc đơn giản dùng `partition`/comparator:
```cpp
vector<int> sortArrayByParity(vector<int> &a) {
    sort(a.begin(), a.end(), [](int x, int y) {
        return (x % 2) < (y % 2); // chẵn (0) đứng trước lẻ (1)
    });
    return a;
}
```
</details>

<details>
<summary>Lời giải Bài 2 — Merge Intervals</summary>

Sắp xếp theo điểm bắt đầu, sau đó duyệt và gộp nếu chồng lấn.
```cpp
vector<vector<int>> merge(vector<vector<int>> &intervals) {
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> result;
    for (auto &iv : intervals) {
        if (!result.empty() && iv[0] <= result.back()[1])
            result.back()[1] = max(result.back()[1], iv[1]);
        else
            result.push_back(iv);
    }
    return result;
}
```
</details>

<details>
<summary>Lời giải Bài 3 — Custom Sort String</summary>

```cpp
string customSortString(string order, string s) {
    unordered_map<char,int> priority;
    for (int i = 0; i < (int)order.size(); i++) priority[order[i]] = i;
    sort(s.begin(), s.end(), [&](char a, char b) {
        return priority.count(a) ? (priority.count(b) ? priority[a] < priority[b] : true)
                                   : (priority.count(b) ? false : false); // ký tự lạ giữ nguyên tương đối
    });
    return s;
}
```
</details>

<details>
<summary>Lời giải Bài 4 — Binary Search</summary>

```cpp
int search(vector<int> &nums, int target) {
    int lo = 0, hi = nums.size() - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;
}
```
</details>

<details>
<summary>Lời giải Bài 5 — Find First and Last Position</summary>

Dùng `lower_bound`/`upper_bound` như Ví dụ 1, mục 4.2.
```cpp
vector<int> searchRange(vector<int> &nums, int target) {
    int first = lower_bound(nums.begin(), nums.end(), target) - nums.begin();
    if (first == (int)nums.size() || nums[first] != target) return {-1, -1};
    int last = upper_bound(nums.begin(), nums.end(), target) - nums.begin() - 1;
    return {first, last};
}
```
</details>

<details>
<summary>Lời giải Bài 6 — Search in Rotated Sorted Array</summary>

Giống hệt Ví dụ 2, mục 4.2 (`searchRotated`).
</details>

<details>
<summary>Lời giải Bài 7 — Factory Machines</summary>

Binary search on answer trên thời gian T: `check(T)` = tổng số sản phẩm làm được trong T phút của tất cả máy (`sum(T / t[i])`) có ≥ k không.
```cpp
bool check(vector<long long> &t, long long T, long long k) {
    long long total = 0;
    for (long long ti : t) total += T / ti;
    return total >= k;
}
long long minTime(vector<long long> &t, long long k) {
    long long lo = 1, hi = (long long)2e18, ans = hi;
    while (lo <= hi) {
        long long mid = lo + (hi - lo) / 2;
        if (check(t, mid, k)) { ans = mid; hi = mid - 1; }
        else lo = mid + 1;
    }
    return ans;
}
```
</details>

<details>
<summary>Lời giải Bài 8 — Array Division</summary>

Giống hệt Ví dụ 2, mục 4.3 (`minLargestSum`), chỉ đổi tên biến `pages` thành mảng đề cho, `k` giữ nguyên ý nghĩa số đoạn.
</details>

<details>
<summary>Lời giải Bài 9 — Aggressive Cows</summary>

Đây là dạng "maximize the minimum" — vẫn dùng binary search on answer nhưng chiều tìm kiếm đảo ngược: nếu khoảng cách d khả thi (đặt được c con bò với khoảng cách tối thiểu d), thử d **lớn hơn**; nếu không khả thi, thử d nhỏ hơn.
```cpp
bool canPlace(vector<int> &pos, int cows, int minDist) {
    int count = 1, last = pos[0];
    for (int i = 1; i < (int)pos.size(); i++) {
        if (pos[i] - last >= minDist) { count++; last = pos[i]; }
    }
    return count >= cows;
}
int maxMinDistance(vector<int> pos, int cows) {
    sort(pos.begin(), pos.end());
    int lo = 0, hi = pos.back() - pos.front(), ans = 0;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (canPlace(pos, cows, mid)) { ans = mid; lo = mid + 1; } // khả thi -> thử LỚN hơn
        else hi = mid - 1;
    }
    return ans;
}
```
</details>

<details>
<summary>Lời giải Bài 10 — Median of Two Sorted Arrays</summary>

Binary search trên mảng ngắn hơn để tìm điểm chia (partition) sao cho nửa trái gộp của cả 2 mảng có đúng số phần tử cần thiết và mọi phần tử nửa trái ≤ mọi phần tử nửa phải.
```cpp
double findMedianSortedArrays(vector<int> &a, vector<int> &b) {
    if (a.size() > b.size()) swap(a, b); // đảm bảo a là mảng ngắn hơn
    int m = a.size(), n = b.size();
    int lo = 0, hi = m;
    while (lo <= hi) {
        int i = lo + (hi - lo) / 2; // số phần tử lấy từ a cho nửa trái
        int j = (m + n + 1) / 2 - i; // số phần tử lấy từ b cho nửa trái

        int aLeft = (i == 0) ? INT_MIN : a[i-1];
        int aRight = (i == m) ? INT_MAX : a[i];
        int bLeft = (j == 0) ? INT_MIN : b[j-1];
        int bRight = (j == n) ? INT_MAX : b[j];

        if (aLeft <= bRight && bLeft <= aRight) {
            if ((m + n) % 2 == 0)
                return (max(aLeft, bLeft) + min(aRight, bRight)) / 2.0;
            else
                return max(aLeft, bLeft);
        } else if (aLeft > bRight) hi = i - 1;
        else lo = i + 1;
    }
    return -1.0; // không xảy ra nếu input hợp lệ
}
```
</details>

<details>
<summary>Lời giải Bài 11 — Peak Index in a Mountain Array</summary>

Giống hệt `findPeakElement` (Bài tập minh hoạ, mục 4.2), vì mảng dạng núi đảm bảo chỉ có đúng 1 đỉnh.
</details>

<details>
<summary>Lời giải Bài 12 — Capacity To Ship Packages Within D Days</summary>

Binary search on answer trên sức chứa C: `check(C)` = số ngày cần thiết để chở hết hàng với sức chứa C có ≤ D không.
```cpp
bool canShip(vector<int> &weights, int cap, int days) {
    int daysNeeded = 1; long long cur = 0;
    for (int w : weights) {
        if (cur + w > cap) { daysNeeded++; cur = 0; }
        cur += w;
    }
    return daysNeeded <= days;
}
int shipWithinDays(vector<int> &weights, int days) {
    int lo = *max_element(weights.begin(), weights.end());
    int hi = accumulate(weights.begin(), weights.end(), 0);
    int ans = hi;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (canShip(weights, mid, days)) { ans = mid; hi = mid - 1; }
        else lo = mid + 1;
    }
    return ans;
}
```
</details>

<details>
<summary>Lời giải Bài 13 — Ternary Search hàm đơn mốt</summary>

```cpp
double f(double x) {
    return abs(x - 5) + abs(x - 10) + (x - 7) * (x - 7) / 100.0;
}
double findMin(double lo, double hi) {
    for (int iter = 0; iter < 200; iter++) {
        double m1 = lo + (hi - lo) / 3;
        double m2 = hi - (hi - lo) / 3;
        if (f(m1) > f(m2)) lo = m1; // đang tìm MIN nên đảo điều kiện so với ternarySearchMax
        else hi = m2;
    }
    return (lo + hi) / 2;
}
```
</details>

<details>
<summary>Lời giải Bài 14 — Koko Eating Bananas biến thể</summary>

Ý tưởng: hàm `check(k)` cần mô phỏng lại logic ưu tiên 2 đống "ưa thích" trước — vẫn giữ nguyên khung binary search on answer, chỉ thay đổi cách tính `hours` bên trong `check`.
```cpp
bool check(vector<int> &piles, int favA, int favB, int k, int h) {
    long long hours = 0;
    // Ưu tiên ăn 2 đống ưa thích trước
    hours += (piles[favA] + k - 1) / k;
    hours += (piles[favB] + k - 1) / k;
    for (int i = 0; i < (int)piles.size(); i++) {
        if (i == favA || i == favB) continue;
        hours += (piles[i] + k - 1) / k;
    }
    return hours <= h;
}
// Phần khung binary search giữ nguyên như minEatingSpeed (Ví dụ 1, mục 4.3),
// chỉ thay lời gọi canFinish() bằng check() phiên bản mới ở trên.
```
> **Ghi chú:** đây là bài rèn tư duy — phần khó không nằm ở khung binary search (đã quen thuộc) mà ở việc thiết kế đúng hàm `check()` phản ánh đúng ràng buộc phức tạp hơn của đề bài.
</details>
