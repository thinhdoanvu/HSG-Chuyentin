# Chương 1: Nhập môn Lập trình Thi đấu

## 1.1. Đọc đề và trích xuất bài toán lõi

**Vấn đề:** Đề bài luôn được "bọc" trong một câu chuyện. Kỹ năng đầu tiên không phải là thuật toán, mà là khả năng lột bỏ lớp vỏ ngôn ngữ để nhìn ra bài toán toán học/thuật toán thuần túy bên trong.

### Ví dụ 1 (Dễ) — Lột vỏ trực tiếp
> *"Bờm có N viên kẹo, viên thứ i có độ ngọt a[i]. Bờm muốn biết tổng độ ngọt của tất cả các viên kẹo."*

**Bài toán lõi:** Tính tổng của một mảng N số nguyên. → `sum(a[0..N-1])`.

### Ví dụ 2 (Trung bình) — Cần suy luận thêm 1 bước
> *"Một cửa hàng có N sản phẩm, giá sản phẩm thứ i là a[i]. Khách hàng muốn mua 2 sản phẩm khác nhau sao cho tổng giá đúng bằng S đồng. Hỏi có tồn tại cặp sản phẩm như vậy không?"*

**Bài toán lõi:** Cho mảng N số, kiểm tra tồn tại cặp (i, j) với i ≠ j sao cho `a[i] + a[j] = S`. Đây là bài toán "Two Sum" kinh điển — dấu hiệu nhận biết: "cặp", "tổng bằng giá trị cho trước".

### Ví dụ 3 (Khó) — Câu chuyện dài, nhiều lớp che giấu
> *"Trong một thành phố có N giao lộ và M con đường hai chiều nối giữa các giao lộ, mỗi con đường có một thời gian di chuyển. Một người đưa thư xuất phát từ giao lộ 1, cần đi đến giao lộ N để giao thư, sau đó phải quay lại giao lộ 1. Vì thời gian gấp, người đưa thư muốn tổng thời gian của cả hành trình đi và về là nhỏ nhất. Biết rằng người đưa thư có thể đi qua một con đường nhiều lần nếu cần."*

**Bài toán lõi:** Đây thực chất là bài toán tìm đường đi ngắn nhất 2 chiều trên đồ thị có trọng số không âm — vì đường đi ngắn nhất từ 1→N và N→1 trên đồ thị vô hướng có cùng độ dài, bài toán quy về: tìm đường đi ngắn nhất từ đỉnh 1 đến đỉnh N, rồi nhân đôi kết quả. → Thuật toán Dijkstra (Chương 12).

### Bài tập minh hoạ (tương tự) — tự luyện
> *"Một lớp có N học sinh xếp hàng theo một thứ tự cho trước, chiều cao học sinh thứ i là h[i]. Thầy giáo muốn chọn ra một nhóm học sinh đứng liên tiếp nhau sao cho chiều cao của nhóm đó tăng dần liên tục, và nhóm được chọn phải dài nhất có thể."*

**Lời giải:**
- Bài toán lõi: tìm đoạn con liên tiếp dài nhất mà mảng tăng dần (không phải dãy con bất kỳ như LIS — ở đây bắt buộc **liên tiếp**).
- Cách giải: duyệt tuyến tính, giữ độ dài đoạn tăng hiện tại, reset khi gặp điểm gãy.

```cpp
int longestIncreasingRun(vector<int> &h) {
    int n = h.size();
    int best = 1, cur = 1;
    for (int i = 1; i < n; i++) {
        if (h[i] > h[i-1]) cur++;
        else cur = 1;
        best = max(best, cur);
    }
    return best;
}
```
```python
def longest_increasing_run(h):
    best = cur = 1
    for i in range(1, len(h)):
        if h[i] > h[i-1]:
            cur += 1
        else:
            cur = 1
        best = max(best, cur)
    return best
```

---

## 1.2. Ước lượng độ phức tạp thuật toán từ giới hạn dữ liệu

**Nguyên tắc:** Máy chấm xử lý khoảng **10^8 phép tính/giây**. Dựa vào N trong đề để suy ngược độ phức tạp tối đa được phép.

| Giới hạn N | Độ phức tạp chấp nhận được | Gợi ý thuật toán |
|---|---|---|
| N ≤ 10 | O(N!), O(2^N · N) | Sinh hoán vị, brute-force |
| N ≤ 20–22 | O(2^N) | Bitmask DP, duyệt tập con |
| N ≤ 100–500 | O(N³) | DP 3 chiều, Floyd-Warshall |
| N ≤ 2.000–5.000 | O(N²) | DP 2 chiều, brute-force cặp |
| N ≤ 10^6 | O(N log N) | Sort, Segment Tree |
| N ≤ 10^7–10^8 | O(N) | Duyệt tuyến tính, prefix sum |
| N ≤ 10^18 | O(log N) hoặc O(1) | Lũy thừa nhanh, công thức đóng |

### Ví dụ 1 (Dễ) — Đọc trực tiếp từ bảng
Đề cho N ≤ 5.000, yêu cầu đếm số cặp (i, j) thoả điều kiện nào đó.
→ N ≤ 5.000 → O(N²) chấp nhận được → duyệt 2 vòng lặp lồng nhau là đủ, không cần tối ưu thêm.

### Ví dụ 2 (Trung bình) — Cần phân biệt 2 giới hạn trong cùng 1 đề
Đề cho N ≤ 10^5 (số phần tử) nhưng Q ≤ 10^5 (số truy vấn), mỗi truy vấn hỏi tổng một đoạn.
→ Nếu tính tổng đoạn trực tiếp mỗi truy vấn: O(Q·N) = 10^10 → **TLE chắc chắn**.
→ Cần tiền xử lý O(N) (Prefix Sum, Chương 5) để mỗi truy vấn chỉ mất O(1) → tổng O(N + Q) = 2×10^5, an toàn.

### Ví dụ 3 (Khó) — Giới hạn "ẩn" đòi hỏi suy luận qua nhiều bước
Đề cho N ≤ 10^5 phần tử, K ≤ N, yêu cầu tìm tổng lớn nhất của đúng K đoạn con rời nhau, tổng độ dài không giới hạn.
→ Thoạt nhìn dễ nhầm là bài O(N²) (thử mọi cách chọn K đoạn), nhưng N=10^5 loại ngay khả năng O(N²) (10^10 phép tính).
→ Buộc phải nghĩ tới DP tuyến tính theo N với trạng thái phụ K: `dp[i][k]` = phương án tốt nhất xét i phần tử đầu, đã chọn k đoạn — độ phức tạp O(N·K), chỉ chấp nhận được nếu K cũng nhỏ (ví dụ K ≤ 500) — đây là kỹ năng "đọc đồng thời nhiều giới hạn" để suy ra đúng độ phức tạp cho phép.

### Bài tập minh hoạ (tương tự) — tự luyện
> *"Cho N ≤ 2×10^5 số nguyên. Có Q ≤ 2×10^5 truy vấn, mỗi truy vấn hỏi: phần tử nhỏ nhất lớn hơn hoặc bằng x là bao nhiêu? (x cho trước theo từng truy vấn)"*

**Lời giải:**
- N, Q ≤ 2×10^5 → phải đạt tổng độ phức tạp O((N+Q) log N).
- Brute-force mỗi truy vấn duyệt O(N) → tổng O(N·Q) = 4×10^10 → TLE.
- Giải pháp: sort mảng 1 lần O(N log N), mỗi truy vấn dùng tìm kiếm nhị phân O(log N) → tổng O((N+Q) log N) ≈ 2×10^5 × 18 ≈ 3.6×10^6, an toàn.

```cpp
sort(a.begin(), a.end());
// mỗi truy vấn x:
auto it = lower_bound(a.begin(), a.end(), x);
int result = (it != a.end()) ? *it : -1;
```
```python
import bisect
a.sort()
# mỗi truy vấn x:
pos = bisect.bisect_left(a, x)
result = a[pos] if pos < len(a) else -1
```

---

## 1.3. I/O nhanh (Fast Input/Output)

**Vấn đề:** Với N lớn (≥ 10^5), I/O mặc định có thể chậm tới mức làm thuật toán đúng vẫn bị TLE.

### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n;
    cin >> n;
    vector<int> a(n);
    for (int &x : a) cin >> x;

    for (int x : a) cout << x << " ";
    cout << "\n";
    return 0;
}
```

### Python
```python
import sys
input = sys.stdin.readline

n = int(input())
a = list(map(int, input().split()))
print(*a)
```

Với N cực lớn (≥ 10^6), đọc toàn bộ 1 lần bằng con trỏ:
```python
import sys
data = sys.stdin.read().split()
idx = 0
n = int(data[idx]); idx += 1
a = list(map(int, data[idx:idx+n])); idx += n
```

---

## 1.4. Kỹ năng debug và test biên

### Ví dụ 1 (Dễ) — Quên xử lý N = 0 hoặc N = 1
```cpp
// SAI: giả sử luôn có ít nhất 2 phần tử để so sánh
int findMax(vector<int> &a) {
    int mx = a[0];
    for (int i = 1; i < a.size(); i++) mx = max(mx, a[i]);
    return mx;
}
// Nếu a rỗng (a.size() == 0), a[0] gây lỗi truy cập ngoài vùng nhớ (undefined behavior)
```
**Sửa:** luôn kiểm tra `if (a.empty()) return ...;` trước khi truy cập phần tử đầu tiên.

### Ví dụ 2 (Trung bình) — Tràn số (overflow)
```cpp
int n = 100000;
int a[100000]; // mỗi phần tử tới 10^9
int sum = 0;
for (int i = 0; i < n; i++) sum += a[i];
// SAI: tổng có thể lên tới 10^5 * 10^9 = 10^14, vượt quá giới hạn int (~2.1*10^9)
```
**Sửa:** dùng `long long sum = 0;` khi tổng có khả năng vượt quá ~2×10^9.

### Ví dụ 3 (Khó) — Lỗi tinh vi khi dùng cấu trúc dữ liệu tham chiếu (Python)
```python
# SAI: tạo ma trận 2D, nhưng 3 dòng cùng tham chiếu 1 list
grid = [[0] * 3] * 3
grid[0][0] = 1
print(grid)  # [[1, 0, 0], [1, 0, 0], [1, 0, 0]] <- cả 3 dòng đều đổi!
```
**Sửa:**
```python
grid = [[0] * 3 for _ in range(3)]  # mỗi dòng là 1 list riêng biệt
```

### Kỹ thuật Stress Testing
```python
import random

def brute_force(arr):
    return sorted(arr)

def optimized(arr):
    return sorted(arr)  # thay bằng thuật toán cần kiểm tra

for trial in range(1000):
    n = random.randint(1, 10)
    arr = [random.randint(1, 100) for _ in range(n)]
    if brute_force(arr[:]) != optimized(arr[:]):
        print("MISMATCH! Test case:", arr)
        break
else:
    print("Tất cả 1000 test đều khớp!")
```

---

## Tổng kết Chương 1

- Kỹ năng nền tảng nhất: **đọc đề đúng** (trích xuất bài toán lõi) và **ước lượng độ phức tạp** trước khi code.
- Luôn đọc giới hạn dữ liệu đầu tiên — đó là kim chỉ nam chọn thuật toán.
- I/O nhanh là điều kiện cần chứ không đủ — sai độ phức tạp thì I/O nhanh không cứu được.
- Luôn tự test edge case (N=0, N=1, số âm, tràn số, tham chiếu list trong Python) trước khi nộp bài.

---

## Bài tập cuối chương (10+ bài, sưu tầm nhiều nguồn)

| # | Tên bài | Nguồn | Độ khó | Kỹ năng luyện |
|---|---|---|---|---|
| 1 | Watermelon | Codeforces 4A | Dễ | Đọc đề, lột vỏ bài toán |
| 2 | Weird Algorithm | CSES 1068 | Dễ | I/O cơ bản, mô phỏng |
| 3 | Two Sum | LeetCode 1 | Dễ | Trích xuất bài toán lõi (Ví dụ 2, mục 1.1) |
| 4 | Theatre Square | Codeforces 1A | Dễ-TB | Ước lượng công thức, tránh brute-force sai |
| 5 | Missing Number | CSES 1083 | Dễ-TB | I/O nhanh với N lớn |
| 6 | Increasing Array | Codeforces 1547B / tương tự | TB | Suy luận độ phức tạp từ 2 giới hạn |
| 7 | Contains Duplicate | LeetCode 217 | TB | Chọn đúng cấu trúc dữ liệu theo N |
| 8 | Chuỗi con tăng dài nhất liên tiếp | Đề HSG tỉnh (dạng phổ biến) | TB | Tương tự bài tập minh hoạ mục 1.1 |
| 9 | Bin Packing (ước lượng độ phức tạp) | Codeforces 1526C1 | TB-Khó | Nhận diện giới hạn "ẩn" (Ví dụ 3, mục 1.2) |
| 10 | Restaurant Customers | CSES 1619 | TB | Xử lý input nhiều dòng, edge case N=0 |
| 11 | Sum of Two Values | CSES 1640 | TB | Kết hợp đọc đề + chọn cấu trúc dữ liệu |
| 12 | Bài toán tổng hợp trong đề thi HSG Quốc gia (bảng ước lượng độ phức tạp với nhiều ràng buộc N, K, Q khác nhau) | Đề HSG QG các năm gần đây | Khó | Tổng hợp toàn bộ kỹ năng Chương 1 |

> **Gợi ý luyện tập:** giải theo đúng thứ tự bảng trên (độ khó tăng dần). Với mỗi bài, trước khi code, hãy tự viết ra: (1) bài toán lõi là gì, (2) N cho phép độ phức tạp nào, (3) cấu trúc dữ liệu/kỹ thuật dự kiến dùng — rồi mới bắt đầu code.
