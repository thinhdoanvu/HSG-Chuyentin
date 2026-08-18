# Chương 10: Quy hoạch động nâng cao

---

## 10.1. Tối ưu hoá DP bằng Hàng đợi đơn điệu (Sliding Window Optimization)

**Nêu bài toán:** Có n công việc xếp theo thứ tự thời gian, công việc thứ `i` mang lại lợi ích `loiIch[i]`. Quy định: nếu chọn làm công việc thứ `i`, công việc tiếp theo được chọn phải cách công việc `i` **ít nhất 1 và nhiều nhất k** vị trí (nghĩa là công việc tiếp theo nằm trong khoảng từ `i+1` đến `i+k`). Hãy tìm tổng lợi ích lớn nhất khi bắt buộc phải chọn công việc cuối cùng (vị trí n-1), xuất phát từ việc chọn 1 công việc bất kỳ trong k công việc đầu tiên.

**Phân tích vấn đề:** Đặt `dp[i]` là tổng lợi ích lớn nhất khi công việc cuối cùng được chọn là công việc thứ `i`. Công thức: `dp[i] = loiIch[i] + max(dp[j])` với `j` chạy trong khoảng `[i-k, i-1]`. Đây chính là bài toán "quy hoạch động có transition phụ thuộc vào giá trị lớn nhất trong một cửa sổ trượt" đã được giới thiệu sơ lược ở Ví dụ 3, mục 6.5 — chương này hệ thống hoá lại kỹ thuật đó như một "mẫu hình tối ưu hoá DP" độc lập.

**Giải pháp đơn thuần:** Với mỗi `i`, duyệt tuyến tính qua `k` giá trị `dp[j]` trước đó để tìm max. Độ phức tạp O(n×k).

**Khó khăn với giải pháp đơn thuần:** Khi k tỉ lệ với n (ví dụ cả hai đều xấp xỉ 200.000), độ phức tạp O(n×k) tiến gần O(n²) — không khả thi trong giới hạn thời gian.

**Cách tiếp cận mới:** Nhận thấy cửa sổ `[i-k, i-1]` **trượt dần một cách đơn điệu** khi `i` tăng dần (giống hệt bài toán "giá trị lớn nhất trong mọi cửa sổ trượt kích thước k" ở Chương 6) — nên có thể dùng **Monotonic Deque** để duy trì giá trị `max(dp[j])` trong O(1) khấu hao mỗi bước, thay vì duyệt lại O(k) mỗi lần.

### Minh hoạ lời giải chi tiết

Với `loiIch = [5, 1, 2, 8, 3, 4]`, `k = 2`:

| i | Cửa sổ xét max (j trong [i-k, i-1]) | max(dp[j]) trong cửa sổ | dp[i] = loiIch[i] + max |
|---|---|---|---|
| 0 | (không có, coi dp "ảo" trước vị trí 0 = 0) | 0 | 5 |
| 1 | [dp ảo trước 0, dp[0]] | max(0, 5) = 5 | 6 |
| 2 | [dp[0], dp[1]] | max(5, 6) = 6 | 8 |
| 3 | [dp[1], dp[2]] | max(6, 8) = 8 | 16 |
| 4 | [dp[2], dp[3]] | max(8, 16) = 16 | 19 |
| 5 | [dp[3], dp[4]] | max(16, 19) = 19 | 23 |

Kết quả: `dp[5] = 23`.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long toiUuDPBangHangDoiDonDieu(vector<int> loiIch, int k) {
    int n = (int)loiIch.size();
    vector<long long> dp(n, 0);
    deque<int> hangDoi; // lưu chỉ số, dp tương ứng giảm dần từ đầu đến cuối

    hangDoi.push_back(-1); // trạng thái "ảo" trước vị trí 0, giá trị dp là 0

    for (int i = 0; i < n; i++) {
        while (!hangDoi.empty() && hangDoi.front() < i - k) {
            hangDoi.pop_front();
        }

        long long giaTriTotNhatTruocDo;
        if (hangDoi.front() == -1) {
            giaTriTotNhatTruocDo = 0;
        } else {
            giaTriTotNhatTruocDo = dp[hangDoi.front()];
        }

        dp[i] = loiIch[i] + giaTriTotNhatTruocDo;

        while (!hangDoi.empty() && hangDoi.back() != -1 && dp[hangDoi.back()] < dp[i]) {
            hangDoi.pop_back();
        }
        hangDoi.push_back(i);
    }

    return dp[n - 1];
}

int main() {
    vector<int> loiIch = {5, 1, 2, 8, 3, 4};
    int k = 2;

    cout << toiUuDPBangHangDoiDonDieu(loiIch, k) << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def toi_uu_dp_bang_hang_doi_don_dieu(loi_ich, k):
    n = len(loi_ich)
    dp = [0] * n
    hang_doi = deque()

    hang_doi.append(-1)

    for i in range(n):
        while len(hang_doi) > 0 and hang_doi[0] < i - k:
            hang_doi.popleft()

        if hang_doi[0] == -1:
            gia_tri_tot_nhat_truoc_do = 0
        else:
            gia_tri_tot_nhat_truoc_do = dp[hang_doi[0]]

        dp[i] = loi_ich[i] + gia_tri_tot_nhat_truoc_do

        while len(hang_doi) > 0 and hang_doi[-1] != -1 and dp[hang_doi[-1]] < dp[i]:
            hang_doi.pop()
        hang_doi.append(i)

    return dp[n - 1]


loi_ich = [5, 1, 2, 8, 3, 4]
k = 2

print(toi_uu_dp_bang_hang_doi_don_dieu(loi_ich, k))
```

### Ví dụ 1 (Dễ) — Jump Game VI (nhắc lại từ Chương 6, dùng dạng thức tối ưu hoá DP đầy đủ)

Bài toán: cho mảng điểm số và số k, xuất phát từ vị trí 0, mỗi bước nhảy tối đa k vị trí về phía trước, đích là vị trí cuối cùng. Tìm tổng điểm lớn nhất trên đường đi.

**Phân tích:** `dp[i] = diem[i] + max(dp[j])` với `j` trong `[i-k, i-1]` — đúng khuôn mẫu vừa học, chỉ khác cách khởi tạo `dp[0] = diem[0]` thay vì trạng thái "ảo".

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long jumpGameVI(vector<int> diem, int k) {
    int n = (int)diem.size();
    vector<long long> dp(n, 0);
    dp[0] = diem[0];

    deque<int> hangDoi;
    hangDoi.push_back(0);

    for (int i = 1; i < n; i++) {
        while (!hangDoi.empty() && hangDoi.front() < i - k) {
            hangDoi.pop_front();
        }

        dp[i] = diem[i] + dp[hangDoi.front()];

        while (!hangDoi.empty() && dp[hangDoi.back()] < dp[i]) {
            hangDoi.pop_back();
        }
        hangDoi.push_back(i);
    }

    return dp[n - 1];
}

int main() {
    vector<int> diem = {1, -1, -2, 4, -7, 3};
    int k = 2;

    cout << jumpGameVI(diem, k) << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def jump_game_vi(diem, k):
    n = len(diem)
    dp = [0] * n
    dp[0] = diem[0]

    hang_doi = deque()
    hang_doi.append(0)

    for i in range(1, n):
        while len(hang_doi) > 0 and hang_doi[0] < i - k:
            hang_doi.popleft()

        dp[i] = diem[i] + dp[hang_doi[0]]

        while len(hang_doi) > 0 and dp[hang_doi[-1]] < dp[i]:
            hang_doi.pop()
        hang_doi.append(i)

    return dp[n - 1]


diem = [1, -1, -2, 4, -7, 3]
k = 2

print(jump_game_vi(diem, k))
```

### Ví dụ 2 (Trung bình) — Chi phí tối thiểu chia hàng thành các nhóm với chi phí cố định

Bài toán: có n sản phẩm xếp thành hàng, mỗi lần đóng gói được chọn 1 nhóm sản phẩm liên tiếp có độ dài **không vượt quá k**, chi phí đóng gói 1 nhóm là **hằng số C** cộng thêm tổng giá trị hao hụt của các sản phẩm trong nhóm đó, với hao hụt của sản phẩm ở vị trí thứ `t` trong nhóm (tính từ 0) là `t × giaTriHaoHut[i]`. Hãy tìm tổng chi phí nhỏ nhất để đóng gói hết n sản phẩm.

**Phân tích:** Đặt `dp[i]` = chi phí nhỏ nhất để đóng gói xong `i` sản phẩm đầu tiên. Với mỗi cách chọn nhóm cuối cùng có độ dài `d` (từ 1 đến k), `dp[i] = min(dp[i-d] + C + chiPhiHaoHutCuaNhom(i-d, i))`. Nếu tính trực tiếp `chiPhiHaoHutCuaNhom` mỗi lần, độ phức tạp sẽ là O(n×k²). Ta có thể tối ưu bằng cách duy trì `dp[j] - C_j` (với `C_j` là một hằng số phụ thuộc vào công thức khai triển) trong một cửa sổ trượt để tránh tính lại — đây là ứng dụng nâng cao hơn của kỹ thuật monotonic queue kết hợp khai triển công thức toán học.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long chiPhiToiThieuDongGoi(vector<int> giaTriHaoHut, int k, long long C) {
    int n = (int)giaTriHaoHut.size();
    vector<long long> dp(n + 1, LLONG_MAX);
    dp[0] = 0;

    // Với bài này, vì công thức chi phí phụ thuộc vào vị trí tương đối trong nhóm,
    // ta giữ cách tính trực tiếp trong giới hạn k (đã đủ nhanh nếu k nhỏ, O(n*k))
    // để đảm bảo tính đúng đắn dễ hiểu cho học sinh mới học kỹ thuật này.
    for (int i = 1; i <= n; i++) {
        long long tongHaoHutNhom = 0;
        for (int d = 1; d <= k && d <= i; d++) {
            int viTriDauNhom = i - d;
            // Sản phẩm cuối nhóm (vị trí i-1) có t = d-1 (tính từ 0 trong nhóm)
            tongHaoHutNhom = tongHaoHutNhom + (long long)(d - 1) * giaTriHaoHut[i - 1];

            if (dp[viTriDauNhom] == LLONG_MAX) {
                continue;
            }
            long long ungCu = dp[viTriDauNhom] + C + tongHaoHutNhom;
            if (ungCu < dp[i]) {
                dp[i] = ungCu;
            }
        }
    }

    return dp[n];
}

int main() {
    vector<int> giaTriHaoHut = {1, 2, 3, 4, 5};
    int k = 2;
    long long C = 10;

    cout << chiPhiToiThieuDongGoi(giaTriHaoHut, k, C) << endl;

    return 0;
}
```

#### PYTHON
```python
def chi_phi_toi_thieu_dong_goi(gia_tri_hao_hut, k, C):
    n = len(gia_tri_hao_hut)
    dp = [float('inf')] * (n + 1)
    dp[0] = 0

    for i in range(1, n + 1):
        tong_hao_hut_nhom = 0
        for d in range(1, min(k, i) + 1):
            vi_tri_dau_nhom = i - d
            tong_hao_hut_nhom = tong_hao_hut_nhom + (d - 1) * gia_tri_hao_hut[i - 1]

            if dp[vi_tri_dau_nhom] == float('inf'):
                continue
            ung_cu = dp[vi_tri_dau_nhom] + C + tong_hao_hut_nhom
            if ung_cu < dp[i]:
                dp[i] = ung_cu

    return dp[n]


gia_tri_hao_hut = [1, 2, 3, 4, 5]
k = 2
C = 10

print(chi_phi_toi_thieu_dong_goi(gia_tri_hao_hut, k, C))
```

> **Ghi chú:** ví dụ này minh hoạ rằng không phải mọi bài toán "tối ưu chi phí theo nhóm" đều có thể tối ưu bằng monotonic queue một cách trực tiếp — nếu công thức chi phí phụ thuộc vào **vị trí tương đối trong nhóm** (không phải chỉ tổng giá trị), ta cần các kỹ thuật mạnh hơn như Convex Hull Trick (mục 10.2) hoặc Divide and Conquer Optimization (mục 10.3).

### Ví dụ 3 (Khó) — Tối ưu DP dùng hàng đợi đơn điệu kết hợp mảng cộng dồn

Bài toán: cho mảng n số nguyên và số k, chia mảng thành các đoạn con liên tiếp, mỗi đoạn có độ dài **không vượt quá k**. Chi phí của mỗi đoạn bằng tổng các phần tử trong đoạn đó. Hãy tìm cách chia để tổng chi phí (tổng của tổng các đoạn, tức bằng tổng toàn mảng — cố định) trừ đi **k lần số lượng đoạn** là nhỏ nhất (tức tối thiểu hoá số lượng đoạn, có phạt thêm nếu đoạn ngắn).

**Phân tích:** Đặt `dp[i]` = chi phí tối ưu cho `i` phần tử đầu. `dp[i] = min(dp[j] + (prefix[i]-prefix[j]) - k)` với `j` trong `[i-k, i-1]`. Khai triển: `dp[i] = prefix[i] - k + min(dp[j] - prefix[j])` với `j` trong `[i-k, i-1]` — đây là dạng "tìm min của `dp[j] - prefix[j]` trong cửa sổ trượt", áp dụng trực tiếp monotonic queue.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long toiUuChiaDoanConDoDaiToiDaK(vector<int> a, int k) {
    int n = (int)a.size();
    vector<long long> prefix(n + 1, 0);
    for (int i = 0; i < n; i++) {
        prefix[i + 1] = prefix[i] + a[i];
    }

    vector<long long> dp(n + 1, LLONG_MAX);
    dp[0] = 0;

    // giaTriPhu[j] = dp[j] - prefix[j], dùng deque để tìm min trong cửa sổ trượt
    deque<int> hangDoi;
    hangDoi.push_back(0);

    for (int i = 1; i <= n; i++) {
        while (!hangDoi.empty() && hangDoi.front() < i - k) {
            hangDoi.pop_front();
        }

        long long giaTriPhuNhoNhat = dp[hangDoi.front()] - prefix[hangDoi.front()];
        dp[i] = prefix[i] - k + giaTriPhuNhoNhat;

        long long giaTriPhuCuaI = dp[i] - prefix[i];
        while (!hangDoi.empty()) {
            long long giaTriPhuDuoi = dp[hangDoi.back()] - prefix[hangDoi.back()];
            if (giaTriPhuDuoi > giaTriPhuCuaI) {
                hangDoi.pop_back();
            } else {
                break;
            }
        }
        hangDoi.push_back(i);
    }

    return dp[n];
}

int main() {
    vector<int> a = {3, 1, 4, 1, 5, 9, 2, 6};
    int k = 3;

    cout << toiUuChiaDoanConDoDaiToiDaK(a, k) << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def toi_uu_chia_doan_con_do_dai_toi_da_k(a, k):
    n = len(a)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + a[i]

    dp = [float('inf')] * (n + 1)
    dp[0] = 0

    hang_doi = deque()
    hang_doi.append(0)

    for i in range(1, n + 1):
        while len(hang_doi) > 0 and hang_doi[0] < i - k:
            hang_doi.popleft()

        gia_tri_phu_nho_nhat = dp[hang_doi[0]] - prefix[hang_doi[0]]
        dp[i] = prefix[i] - k + gia_tri_phu_nho_nhat

        gia_tri_phu_cua_i = dp[i] - prefix[i]
        while len(hang_doi) > 0:
            gia_tri_phu_duoi = dp[hang_doi[-1]] - prefix[hang_doi[-1]]
            if gia_tri_phu_duoi > gia_tri_phu_cua_i:
                hang_doi.pop()
            else:
                break
        hang_doi.append(i)

    return dp[n]


a = [3, 1, 4, 1, 5, 9, 2, 6]
k = 3

print(toi_uu_chia_doan_con_do_dai_toi_da_k(a, k))
```

**Bài tập minh hoạ:** Cho mảng n số nguyên và số k, tìm giá trị lớn nhất của `dp[n]`, với `dp[0] = 0` và `dp[i] = a[i] + min(dp[j])` với `j` trong `[max(0,i-k), i-1]` (đối xứng với bài toán chính, dùng min thay vì max).

**Lời giải:** Áp dụng đúng khung mẫu, chỉ đảo chiều so sánh trong deque từ giảm dần thành tăng dần.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long toiUuDPDungMin(vector<int> a, int k) {
    int n = (int)a.size();
    vector<long long> dp(n, 0);
    dp[0] = a[0];

    deque<int> hangDoi;
    hangDoi.push_back(0);

    for (int i = 1; i < n; i++) {
        while (!hangDoi.empty() && hangDoi.front() < i - k) {
            hangDoi.pop_front();
        }

        dp[i] = a[i] + dp[hangDoi.front()];

        while (!hangDoi.empty() && dp[hangDoi.back()] > dp[i]) {
            hangDoi.pop_back();
        }
        hangDoi.push_back(i);
    }

    return dp[n - 1];
}
```

#### PYTHON
```python
from collections import deque


def toi_uu_dp_dung_min(a, k):
    n = len(a)
    dp = [0] * n
    dp[0] = a[0]

    hang_doi = deque()
    hang_doi.append(0)

    for i in range(1, n):
        while len(hang_doi) > 0 and hang_doi[0] < i - k:
            hang_doi.popleft()

        dp[i] = a[i] + dp[hang_doi[0]]

        while len(hang_doi) > 0 and dp[hang_doi[-1]] > dp[i]:
            hang_doi.pop()
        hang_doi.append(i)

    return dp[n - 1]
```

---

## 10.2. Convex Hull Trick (Tối ưu DP dạng đường thẳng)

**Nêu bài toán:** Cho mảng n số nguyên, chia mảng thành các đoạn con liên tiếp (số lượng đoạn không giới hạn). Chi phí của mỗi đoạn bằng **bình phương** tổng giá trị các phần tử trong đoạn đó, cộng thêm một hằng số C cho mỗi đoạn. Hãy tìm cách chia để tổng chi phí là nhỏ nhất.

**Phân tích vấn đề:** Đặt `prefix[i]` là mảng cộng dồn (Chương 5). Đặt `dp[i]` = chi phí nhỏ nhất để chia xong `i` phần tử đầu. Công thức: `dp[i] = min(dp[j] + (prefix[i]-prefix[j])² + C)` với `j` chạy từ 0 đến i-1. Khai triển bình phương: `dp[i] = min(dp[j] + prefix[j]² - 2×prefix[j]×prefix[i] + prefix[i]² + C)`.

**Giải pháp đơn thuần:** Với mỗi `i`, thử tất cả `j` từ 0 đến i-1. Độ phức tạp O(n²).

**Khó khăn với giải pháp đơn thuần:** Với n lên tới 10^5 hoặc 10^6, O(n²) sẽ quá chậm — trong khi bài toán này **không có tính đơn điệu cửa sổ trượt** như mục 10.1 (j có thể là bất kỳ giá trị nào từ 0 đến i-1, không giới hạn trong 1 cửa sổ kích thước k).

**Cách tiếp cận mới — Convex Hull Trick:** Viết lại công thức, tách phần phụ thuộc vào `j` và phần phụ thuộc vào `i`:

```
dp[i] = prefix[i]² + C + min_j( (dp[j] + prefix[j]²) + (-2×prefix[j]) × prefix[i] )
```

Nhìn phần trong dấu `min_j(...)`, đây chính là giá trị của một **đường thẳng** `y = m×x + b` tại điểm `x = prefix[i]`, với **độ dốc** `m = -2×prefix[j]` và **tung độ gốc** `b = dp[j] + prefix[j]²`. Mỗi giá trị `j` tương ứng với **1 đường thẳng khác nhau**. Bài toán trở thành: với mỗi điểm `x = prefix[i]`, tìm đường thẳng cho giá trị **nhỏ nhất** tại điểm đó, trong số tất cả các đường thẳng đã "thêm vào" (ứng với các `j < i` đã xử lý). Duy trì một cấu trúc dữ liệu gọi là "vỏ lồi động" (dynamic convex hull) của các đường thẳng, cho phép thêm đường thẳng mới và truy vấn giá trị nhỏ nhất tại 1 điểm, cả hai đều trong O(log n) (hoặc O(1) khấu hao nếu độ dốc/truy vấn có tính đơn điệu).

### Minh hoạ lời giải chi tiết

Trực giác hình học: mỗi đường thẳng `y = mx + b` là một hàm số. Khi vẽ nhiều đường thẳng trên cùng hệ trục, đường nào cho giá trị nhỏ nhất tại một điểm `x` cụ thể chính là đường **nằm thấp nhất** tại vị trí đó. Nếu ta chỉ giữ lại các đường thẳng **có khả năng** là "thấp nhất" tại một điểm nào đó (loại bỏ những đường luôn bị đường khác che phủ hoàn toàn), tập hợp các đường còn lại tạo thành phần biên dưới của một "vỏ lồi" — do đó có tên Convex Hull Trick.

**Điều kiện quan trọng:** Khi các đường thẳng được thêm vào theo thứ tự **độ dốc đơn điệu** (ở đây, `prefix[j]` tăng dần khi `j` tăng, nên độ dốc `-2×prefix[j]` **giảm dần**), và các truy vấn `x = prefix[i]` cũng đơn điệu tăng dần, ta có thể dùng một cấu trúc **deque đơn giản** (không cần cây cân bằng), đạt độ phức tạp O(n) tổng thể cho cả n lần thêm và n lần truy vấn.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

struct DuongThang {
    long long docDo; // m
    long long tungDoGoc; // b

    long long tinhGiaTri(long long x) {
        return docDo * x + tungDoGoc;
    }
};

// Kiểm tra xem đường B có "vô dụng" hay không khi đã có đường A và đường C
// (B nằm hoàn toàn phía trên giao điểm của A và C, không bao giờ là đường thấp nhất)
bool baDuongThangKhongCanGiua(DuongThang A, DuongThang B, DuongThang C) {
    // So sánh chéo bằng phép nhân để tránh chia (tránh sai số dấu phẩy động)
    return (double)(C.tungDoGoc - A.tungDoGoc) * (A.docDo - B.docDo)
         <= (double)(B.tungDoGoc - A.tungDoGoc) * (A.docDo - C.docDo);
}

deque<DuongThang> vLoiDuongThang;

void themDuongThang(DuongThang duongMoi) {
    while (vLoiDuongThang.size() >= 2) {
        DuongThang cuoi1 = vLoiDuongThang[vLoiDuongThang.size() - 1];
        DuongThang cuoi2 = vLoiDuongThang[vLoiDuongThang.size() - 2];

        if (baDuongThangKhongCanGiua(cuoi2, cuoi1, duongMoi)) {
            vLoiDuongThang.pop_back();
        } else {
            break;
        }
    }
    vLoiDuongThang.push_back(duongMoi);
}

long long truyVanGiaTriNhoNhat(long long x) {
    while (vLoiDuongThang.size() >= 2) {
        long long giaTriDau = vLoiDuongThang[0].tinhGiaTri(x);
        long long giaTriThu2 = vLoiDuongThang[1].tinhGiaTri(x);

        if (giaTriThu2 <= giaTriDau) {
            vLoiDuongThang.pop_front();
        } else {
            break;
        }
    }
    return vLoiDuongThang[0].tinhGiaTri(x);
}

long long giaiBaiToanChiaDoanBinhPhuong(vector<int> a, long long C) {
    int n = (int)a.size();
    vector<long long> prefix(n + 1, 0);
    for (int i = 0; i < n; i++) {
        prefix[i + 1] = prefix[i] + a[i];
    }

    vector<long long> dp(n + 1, 0);
    vLoiDuongThang.clear();

    // Thêm đường thẳng ứng với j = 0 trước khi tính dp[1]
    themDuongThang({-2 * prefix[0], dp[0] + prefix[0] * prefix[0]});

    for (int i = 1; i <= n; i++) {
        long long giaTriNhoNhat = truyVanGiaTriNhoNhat(prefix[i]);
        dp[i] = prefix[i] * prefix[i] + C + giaTriNhoNhat;

        // Sau khi tính dp[i], thêm đường thẳng ứng với j = i để dùng cho các i sau
        themDuongThang({-2 * prefix[i], dp[i] + prefix[i] * prefix[i]});
    }

    return dp[n];
}

int main() {
    vector<int> a = {1, 2, 3, 4, 5};
    long long C = 10;

    cout << giaiBaiToanChiaDoanBinhPhuong(a, C) << endl;

    return 0;
}
```

#### PYTHON
```python
class DuongThang:
    def __init__(self, doc_do, tung_do_goc):
        self.doc_do = doc_do
        self.tung_do_goc = tung_do_goc

    def tinh_gia_tri(self, x):
        return self.doc_do * x + self.tung_do_goc


def ba_duong_thang_khong_can_giua(A, B, C):
    ben_trai = (C.tung_do_goc - A.tung_do_goc) * (A.doc_do - B.doc_do)
    ben_phai = (B.tung_do_goc - A.tung_do_goc) * (A.doc_do - C.doc_do)
    return ben_trai <= ben_phai


v_loi_duong_thang = []


def them_duong_thang(duong_moi):
    while len(v_loi_duong_thang) >= 2:
        cuoi_1 = v_loi_duong_thang[-1]
        cuoi_2 = v_loi_duong_thang[-2]

        if ba_duong_thang_khong_can_giua(cuoi_2, cuoi_1, duong_moi):
            v_loi_duong_thang.pop()
        else:
            break
    v_loi_duong_thang.append(duong_moi)


def truy_van_gia_tri_nho_nhat(x):
    while len(v_loi_duong_thang) >= 2:
        gia_tri_dau = v_loi_duong_thang[0].tinh_gia_tri(x)
        gia_tri_thu_2 = v_loi_duong_thang[1].tinh_gia_tri(x)

        if gia_tri_thu_2 <= gia_tri_dau:
            v_loi_duong_thang.pop(0)
        else:
            break
    return v_loi_duong_thang[0].tinh_gia_tri(x)


def giai_bai_toan_chia_doan_binh_phuong(a, C):
    global v_loi_duong_thang
    n = len(a)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + a[i]

    dp = [0] * (n + 1)
    v_loi_duong_thang = []

    them_duong_thang(DuongThang(-2 * prefix[0], dp[0] + prefix[0] * prefix[0]))

    for i in range(1, n + 1):
        gia_tri_nho_nhat = truy_van_gia_tri_nho_nhat(prefix[i])
        dp[i] = prefix[i] * prefix[i] + C + gia_tri_nho_nhat

        them_duong_thang(DuongThang(-2 * prefix[i], dp[i] + prefix[i] * prefix[i]))

    return dp[n]


a = [1, 2, 3, 4, 5]
C = 10

print(giai_bai_toan_chia_doan_binh_phuong(a, C))
```

> **Lưu ý về việc dùng `pop_front` trên list Python:** đoạn code trên dùng `v_loi_duong_thang.pop(0)` (tương đương xoá đầu list) — trong Python, thao tác này là O(n), không phải O(1) như `deque`. Trong code thi đấu thực tế, nên thay `v_loi_duong_thang` bằng `collections.deque` để đạt đúng độ phức tạp O(n) tổng thể.

### Ví dụ 1 (Dễ) — Nhận diện bài toán có thể dùng CHT

**Phân tích (không code, chỉ phân tích):** Dấu hiệu nhận biết một bài toán DP có thể áp dụng Convex Hull Trick:
1. Công thức DP có dạng `dp[i] = min_j (dp[j] + f(j) × g(i) + h(j))` hoặc tương tự — tách được thành "phần phụ thuộc j" nhân với "phần phụ thuộc i".
2. Sau khi tách, phần "phụ thuộc j" đóng vai trò độ dốc và tung độ gốc của 1 đường thẳng, còn "phần phụ thuộc i" đóng vai trò biến `x` để truy vấn.
3. Bài toán cái túi phân số có công thức chi phí toàn phương (bậc 2) theo tổng đoạn là ví dụ kinh điển nhất — như bài toán chính của mục này.

### Ví dụ 2 (Trung bình) — Bài toán "in ấn tối ưu" (Word Wrap / Print Neatly dạng đơn giản hoá)

Bài toán: có n từ cần in thành các dòng, mỗi dòng có độ rộng tối đa W. Đặt `L[i]` là độ dài từ thứ i (đã tính cả khoảng trắng). Nếu 1 dòng chứa các từ từ vị trí `j+1` đến `i`, "khoảng trống dư" của dòng đó là `W - (tổng độ dài từ j+1 đến i) - (số khoảng trắng)`. Chi phí của dòng là **bình phương** khoảng trống dư (dòng có nhiều khoảng trắng thừa bị phạt nặng). Hãy tìm cách xuống dòng để tổng chi phí là nhỏ nhất.

**Phân tích:** Đây thực chất là biến thể của bài toán chính (chỉ khác công thức chi phí cụ thể của mỗi đoạn, vẫn ở dạng bình phương của 1 hiệu số cộng dồn) — cấu trúc CHT áp dụng gần như giống hệt, chỉ cần thay công thức khai triển tương ứng.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

struct DuongThangInAn {
    long long docDo, tungDoGoc;
    long long tinhGiaTri(long long x) { return docDo * x + tungDoGoc; }
};

bool baDuongKhongCanGiuaInAn(DuongThangInAn A, DuongThangInAn B, DuongThangInAn C) {
    return (double)(C.tungDoGoc - A.tungDoGoc) * (A.docDo - B.docDo)
         <= (double)(B.tungDoGoc - A.tungDoGoc) * (A.docDo - C.docDo);
}

long long giaiBaiToanInAn(vector<int> doDaiTu, int W) {
    int n = (int)doDaiTu.size();
    vector<long long> prefix(n + 1, 0);
    for (int i = 0; i < n; i++) {
        prefix[i + 1] = prefix[i] + doDaiTu[i] + 1; // +1 cho khoảng trắng giữa các từ
    }

    vector<long long> dp(n + 1, 0);
    deque<DuongThangInAn> vLoi;

    auto themDuong = [&](DuongThangInAn duongMoi) {
        while (vLoi.size() >= 2) {
            DuongThangInAn cuoi1 = vLoi[vLoi.size()-1];
            DuongThangInAn cuoi2 = vLoi[vLoi.size()-2];
            if (baDuongKhongCanGiuaInAn(cuoi2, cuoi1, duongMoi)) {
                vLoi.pop_back();
            } else break;
        }
        vLoi.push_back(duongMoi);
    };

    auto truyVan = [&](long long x) -> long long {
        while (vLoi.size() >= 2) {
            if (vLoi[1].tinhGiaTri(x) <= vLoi[0].tinhGiaTri(x)) {
                vLoi.pop_front();
            } else break;
        }
        return vLoi[0].tinhGiaTri(x);
    };

    themDuong({-2 * prefix[0], dp[0] + prefix[0]*prefix[0]});

    for (int i = 1; i <= n; i++) {
        long long x = prefix[i] - W;
        long long giaTriNhoNhat = truyVan(x); // đơn giản hoá: giả sử luôn xếp được (không kiểm tra vượt W)
        dp[i] = x * x + giaTriNhoNhat;
        themDuong({-2 * prefix[i], dp[i] + prefix[i]*prefix[i]});
    }

    return dp[n];
}

int main() {
    vector<int> doDaiTu = {3, 2, 2, 5};
    int W = 6;
    cout << giaiBaiToanInAn(doDaiTu, W) << endl;
    return 0;
}
```

#### PYTHON
```python
class DuongThangInAn:
    def __init__(self, doc_do, tung_do_goc):
        self.doc_do = doc_do
        self.tung_do_goc = tung_do_goc

    def tinh_gia_tri(self, x):
        return self.doc_do * x + self.tung_do_goc


def ba_duong_khong_can_giua_in_an(A, B, C):
    return (C.tung_do_goc - A.tung_do_goc) * (A.doc_do - B.doc_do) <= (B.tung_do_goc - A.tung_do_goc) * (A.doc_do - C.doc_do)


def giai_bai_toan_in_an(do_dai_tu, W):
    n = len(do_dai_tu)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + do_dai_tu[i] + 1

    dp = [0] * (n + 1)
    v_loi = []

    def them_duong(duong_moi):
        while len(v_loi) >= 2:
            cuoi_1 = v_loi[-1]
            cuoi_2 = v_loi[-2]
            if ba_duong_khong_can_giua_in_an(cuoi_2, cuoi_1, duong_moi):
                v_loi.pop()
            else:
                break
        v_loi.append(duong_moi)

    def truy_van(x):
        while len(v_loi) >= 2:
            if v_loi[1].tinh_gia_tri(x) <= v_loi[0].tinh_gia_tri(x):
                v_loi.pop(0)
            else:
                break
        return v_loi[0].tinh_gia_tri(x)

    them_duong(DuongThangInAn(-2 * prefix[0], dp[0] + prefix[0]**2))

    for i in range(1, n + 1):
        x = prefix[i] - W
        gia_tri_nho_nhat = truy_van(x)
        dp[i] = x * x + gia_tri_nho_nhat
        them_duong(DuongThangInAn(-2 * prefix[i], dp[i] + prefix[i]**2))

    return dp[n]


do_dai_tu = [3, 2, 2, 5]
W = 6
print(giai_bai_toan_in_an(do_dai_tu, W))
```

### Ví dụ 3 (Khó) — Convex Hull Trick khi độ dốc/truy vấn KHÔNG đơn điệu (dùng LiChao Tree, giới thiệu)

**Phân tích:** Trong Ví dụ 1 và 2, cả độ dốc các đường thẳng thêm vào và giá trị `x` truy vấn đều đơn điệu — cho phép dùng deque đơn giản. Nếu 1 trong 2 điều kiện này **không** thoả (ví dụ độ dốc thêm vào không theo thứ tự nào, hoặc cần truy vấn tại các điểm x bất kỳ, không đơn điệu), ta cần một cấu trúc dữ liệu mạnh hơn gọi là **Li Chao Tree** (cây phân đoạn đặc biệt lưu trữ đường thẳng "tốt nhất" cho mỗi khoảng con) — mỗi thao tác thêm/truy vấn đều O(log(phạm vi giá trị x)).

> **Ghi chú:** Li Chao Tree là kỹ thuật nâng cao vượt phạm vi minh hoạ chi tiết của chương này (đòi hỏi kiến thức Segment Tree ở Chương 20 trước). Ở đây chỉ giới thiệu để học sinh biết hướng giải quyết khi CHT dạng deque không áp dụng được — khuyến khích tìm hiểu thêm sau khi đã vững Chương 20.

**Bài tập minh hoạ:** Cho mảng n số nguyên đã sắp xếp `prefix` (mảng cộng dồn tăng dần vì các phần tử gốc là số dương), hãy viết hàm `themDuongThang` và `truyVanGiaTriLonNhat` (đối xứng với bài toán chính — đổi từ tìm min sang tìm **max**).

**Lời giải:** Khi chuyển từ tìm min sang tìm max, đảo ngược điều kiện so sánh trong cả hàm kiểm tra "đường vô dụng" và hàm truy vấn.

#### C++
```cpp
struct DuongThangMax {
    long long docDo, tungDoGoc;
    long long tinhGiaTri(long long x) { return docDo * x + tungDoGoc; }
};

bool baDuongKhongCanGiuaMax(DuongThangMax A, DuongThangMax B, DuongThangMax C) {
    // Đảo dấu bất đẳng thức so với bản tìm min
    return (double)(C.tungDoGoc - A.tungDoGoc) * (A.docDo - B.docDo)
         >= (double)(B.tungDoGoc - A.tungDoGoc) * (A.docDo - C.docDo);
}

deque<DuongThangMax> vLoiMax;

void themDuongThangMax(DuongThangMax duongMoi) {
    while (vLoiMax.size() >= 2) {
        DuongThangMax cuoi1 = vLoiMax[vLoiMax.size()-1];
        DuongThangMax cuoi2 = vLoiMax[vLoiMax.size()-2];
        if (baDuongKhongCanGiuaMax(cuoi2, cuoi1, duongMoi)) {
            vLoiMax.pop_back();
        } else break;
    }
    vLoiMax.push_back(duongMoi);
}

long long truyVanGiaTriLonNhat(long long x) {
    while (vLoiMax.size() >= 2) {
        if (vLoiMax[1].tinhGiaTri(x) >= vLoiMax[0].tinhGiaTri(x)) {
            vLoiMax.pop_front();
        } else break;
    }
    return vLoiMax[0].tinhGiaTri(x);
}
```

#### PYTHON
```python
class DuongThangMax:
    def __init__(self, doc_do, tung_do_goc):
        self.doc_do = doc_do
        self.tung_do_goc = tung_do_goc

    def tinh_gia_tri(self, x):
        return self.doc_do * x + self.tung_do_goc


def ba_duong_khong_can_giua_max(A, B, C):
    return (C.tung_do_goc - A.tung_do_goc) * (A.doc_do - B.doc_do) >= (B.tung_do_goc - A.tung_do_goc) * (A.doc_do - C.doc_do)


v_loi_max = []


def them_duong_thang_max(duong_moi):
    while len(v_loi_max) >= 2:
        cuoi_1 = v_loi_max[-1]
        cuoi_2 = v_loi_max[-2]
        if ba_duong_khong_can_giua_max(cuoi_2, cuoi_1, duong_moi):
            v_loi_max.pop()
        else:
            break
    v_loi_max.append(duong_moi)


def truy_van_gia_tri_lon_nhat(x):
    while len(v_loi_max) >= 2:
        if v_loi_max[1].tinh_gia_tri(x) >= v_loi_max[0].tinh_gia_tri(x):
            v_loi_max.pop(0)
        else:
            break
    return v_loi_max[0].tinh_gia_tri(x)
```

---

## 10.3. Tối ưu hoá Chia để trị (Divide and Conquer Optimization)

**Nêu bài toán:** Cho mảng n số nguyên, chia thành đúng **m nhóm liên tiếp** (m cho trước), chi phí mỗi nhóm là bất kỳ hàm `chiPhi(trai, phai)` nào thoả một tính chất đặc biệt gọi là "bất đẳng thức tứ giác" (sẽ giải thích bên dưới). Hãy tìm cách chia để tổng chi phí là nhỏ nhất.

**Phân tích vấn đề:** Đặt `dp[soNhom][i]` = chi phí nhỏ nhất khi chia `i` phần tử đầu thành `soNhom` nhóm. Công thức: `dp[soNhom][i] = min(dp[soNhom-1][j] + chiPhi(j, i))` với `j` từ 0 đến i-1. Với m nhóm và n phần tử, độ phức tạp trực tiếp là O(m×n²) — có thể quá chậm nếu m×n² vượt quá khả năng tính toán (ví dụ n=10^5 đã khiến n² không khả thi).

**Giải pháp đơn thuần:** Tính trực tiếp theo công thức trên bằng 3 vòng lặp lồng nhau (soNhom, i, j). Độ phức tạp O(m×n²).

**Khó khăn với giải pháp đơn thuần:** Với n = 10^4 và m = 10^4, độ phức tạp có thể lên tới 10^12 — không khả thi.

**Cách tiếp cận mới — D&C Optimization:** Nếu hàm chi phí thoả **bất đẳng thức tứ giác** (quadrangle inequality — với mọi `a ≤ b ≤ c ≤ d`, ta có `chiPhi(a,c) + chiPhi(b,d) ≤ chiPhi(a,d) + chiPhi(b,c)`), thì có thể chứng minh: vị trí `j` tối ưu cho `dp[soNhom][i]` (gọi là `opt[i]`) là hàm **đơn điệu không giảm** theo `i`. Tính chất này cho phép áp dụng chia để trị: để tính `dp[soNhom][*]` cho toàn bộ khoảng `i`, ta tính trước cho `i = giữa` (thử toàn bộ `j` có thể, tìm `opt[giữa]`), rồi đệ quy giải nửa trái của `i` (chỉ cần xét `j` từ 0 đến `opt[giữa]`) và nửa phải (chỉ cần xét `j` từ `opt[giữa]` đến i-1) — giảm độ phức tạp từ O(n²) xuống O(n log n) cho mỗi lớp `soNhom`, tổng cộng O(m×n log n).

### Minh hoạ lời giải chi tiết

Giả sử ta cần tính `dp[soNhom][i]` cho `i` từ 1 đến 8, và biết trước `opt[i]` (vị trí j tối ưu) là hàm không giảm. Thay vì thử mọi `j` cho mọi `i`, ta:

1. Tính `dp[soNhom][4]` (điểm giữa của [1,8]), thử toàn bộ `j` từ 0 đến 3, tìm ra `opt[4]`, ví dụ `opt[4] = 2`.
2. Đệ quy tính nửa trái `i ∈ [1,3]`: vì `opt[i] ≤ opt[4] = 2` với mọi `i ≤ 4` (do tính đơn điệu), ta chỉ cần thử `j` từ 0 đến 2, thu hẹp phạm vi tìm kiếm.
3. Đệ quy tính nửa phải `i ∈ [5,8]`: vì `opt[i] ≥ opt[4] = 2`, ta chỉ cần thử `j` từ 2 trở đi.

Nhờ việc thu hẹp phạm vi `j` dựa vào tính đơn điệu, tổng số lần thử `j` qua toàn bộ quá trình đệ quy chỉ còn O(n log n) thay vì O(n²).

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<long long> dpLopTruoc, dpLopHienTai;
vector<int> a;

long long chiPhiNhom(int j, int i) {
    // Ví dụ minh hoạ: chi phí nhóm [j+1, i] là bình phương độ dài nhóm
    // (một hàm thoả bất đẳng thức tứ giác - dùng cho mục đích minh hoạ)
    long long doDaiNhom = i - j;
    return doDaiNhom * doDaiNhom;
}

void chiaDeTriTinhDP(int traiI, int phaiI, int traiJ, int phaiJ) {
    if (traiI > phaiI) {
        return;
    }

    int giuaI = traiI + (phaiI - traiI) / 2;
    long long giaTriTotNhat = LLONG_MAX;
    int viTriToiUu = traiJ;

    int gioiHanJ = min(giuaI - 1, phaiJ);
    for (int j = traiJ; j <= gioiHanJ; j++) {
        if (dpLopTruoc[j] == LLONG_MAX) {
            continue;
        }
        long long ungCu = dpLopTruoc[j] + chiPhiNhom(j, giuaI);
        if (ungCu < giaTriTotNhat) {
            giaTriTotNhat = ungCu;
            viTriToiUu = j;
        }
    }

    dpLopHienTai[giuaI] = giaTriTotNhat;

    chiaDeTriTinhDP(traiI, giuaI - 1, traiJ, viTriToiUu);
    chiaDeTriTinhDP(giuaI + 1, phaiI, viTriToiUu, phaiJ);
}

long long giaiBangDCOptimization(vector<int> mangGoc, int m) {
    int n = (int)mangGoc.size();
    a = mangGoc;

    dpLopTruoc.assign(n + 1, LLONG_MAX);
    dpLopTruoc[0] = 0;

    for (int soNhom = 1; soNhom <= m; soNhom++) {
        dpLopHienTai.assign(n + 1, LLONG_MAX);
        chiaDeTriTinhDP(1, n, 0, n - 1);
        dpLopTruoc = dpLopHienTai;
    }

    return dpLopTruoc[n];
}

int main() {
    vector<int> a = {1, 2, 3, 4, 5, 6, 7, 8};
    int m = 3;

    cout << giaiBangDCOptimization(a, m) << endl;

    return 0;
}
```

#### PYTHON
```python
import sys
sys.setrecursionlimit(100000)

dp_lop_truoc = []
dp_lop_hien_tai = []


def chi_phi_nhom(j, i):
    do_dai_nhom = i - j
    return do_dai_nhom * do_dai_nhom


def chia_de_tri_tinh_dp(trai_i, phai_i, trai_j, phai_j):
    if trai_i > phai_i:
        return

    giua_i = trai_i + (phai_i - trai_i) // 2
    gia_tri_tot_nhat = float('inf')
    vi_tri_toi_uu = trai_j

    gioi_han_j = min(giua_i - 1, phai_j)
    for j in range(trai_j, gioi_han_j + 1):
        if dp_lop_truoc[j] == float('inf'):
            continue
        ung_cu = dp_lop_truoc[j] + chi_phi_nhom(j, giua_i)
        if ung_cu < gia_tri_tot_nhat:
            gia_tri_tot_nhat = ung_cu
            vi_tri_toi_uu = j

    dp_lop_hien_tai[giua_i] = gia_tri_tot_nhat

    chia_de_tri_tinh_dp(trai_i, giua_i - 1, trai_j, vi_tri_toi_uu)
    chia_de_tri_tinh_dp(giua_i + 1, phai_i, vi_tri_toi_uu, phai_j)


def giai_bang_dc_optimization(mang_goc, m):
    global dp_lop_truoc, dp_lop_hien_tai
    n = len(mang_goc)

    dp_lop_truoc = [float('inf')] * (n + 1)
    dp_lop_truoc[0] = 0

    for so_nhom in range(1, m + 1):
        dp_lop_hien_tai = [float('inf')] * (n + 1)
        chia_de_tri_tinh_dp(1, n, 0, n - 1)
        dp_lop_truoc = dp_lop_hien_tai

    return dp_lop_truoc[n]


a = [1, 2, 3, 4, 5, 6, 7, 8]
m = 3

print(giai_bang_dc_optimization(a, m))
```

### Ví dụ 1 (Dễ) — Kiểm tra bất đẳng thức tứ giác bằng thực nghiệm

**Phân tích (bài tập tư duy, không code phức tạp):** Trước khi áp dụng D&C Optimization, cần **xác nhận** hàm chi phí thoả bất đẳng thức tứ giác. Với hàm `chiPhi(j,i) = (i-j)²` (bình phương độ dài đoạn), ta có thể viết một đoạn code nhỏ để kiểm tra thực nghiệm trên nhiều bộ `(a,b,c,d)` ngẫu nhiên.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long chiPhiKiemTra(int j, int i) {
    long long doDaiNhom = i - j;
    return doDaiNhom * doDaiNhom;
}

bool kiemTraBatDangThucTuGiac(int a, int b, int c, int d) {
    // a <= b <= c <= d
    long long veTrai = chiPhiKiemTra(a, c) + chiPhiKiemTra(b, d);
    long long vePhai = chiPhiKiemTra(a, d) + chiPhiKiemTra(b, c);
    return veTrai <= vePhai;
}

int main() {
    srand(42);
    bool luonDung = true;

    for (int lan = 0; lan < 10000; lan++) {
        int a = rand() % 100;
        int b = a + rand() % 100;
        int c = b + rand() % 100;
        int d = c + rand() % 100;

        if (kiemTraBatDangThucTuGiac(a, b, c, d) == false) {
            luonDung = false;
            break;
        }
    }

    cout << (luonDung ? "Thoa bat dang thuc tu giac" : "KHONG thoa") << endl;

    return 0;
}
```

#### PYTHON
```python
import random


def chi_phi_kiem_tra(j, i):
    do_dai_nhom = i - j
    return do_dai_nhom * do_dai_nhom


def kiem_tra_bat_dang_thuc_tu_giac(a, b, c, d):
    ve_trai = chi_phi_kiem_tra(a, c) + chi_phi_kiem_tra(b, d)
    ve_phai = chi_phi_kiem_tra(a, d) + chi_phi_kiem_tra(b, c)
    return ve_trai <= ve_phai


random.seed(42)
luon_dung = True

for lan in range(10000):
    a = random.randint(0, 100)
    b = a + random.randint(0, 100)
    c = b + random.randint(0, 100)
    d = c + random.randint(0, 100)

    if kiem_tra_bat_dang_thuc_tu_giac(a, b, c, d) == False:
        luon_dung = False
        break

print("Thoa bat dang thuc tu giac" if luon_dung else "KHONG thoa")
```

### Ví dụ 2 (Trung bình) — Chia mảng thành k nhóm với chi phí "khoảng cách tới trung vị"

Bài toán: chia mảng n số nguyên đã sắp xếp thành k nhóm liên tiếp, chi phí mỗi nhóm là tổng khoảng cách từ mỗi phần tử trong nhóm tới **trung vị** của nhóm đó. Hãy tìm cách chia để tổng chi phí nhỏ nhất.

**Phân tích:** Hàm chi phí "tổng khoảng cách tới trung vị" của một đoạn là một hàm quen thuộc thoả bất đẳng thức tứ giác (có thể chứng minh bằng lý luận hình học trên trục số) — áp dụng đúng khung D&C Optimization đã học, chỉ cần viết lại hàm `chiPhiNhom` cho phù hợp.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> aDaSapXep;
vector<long long> prefixCuaA;

long long chiPhiKhoangCachToiTrungVi(int j, int i) {
    // Nhóm gồm các phần tử từ chỉ số j đến i-1 (0-indexed) trong mảng đã sắp xếp
    int viTriTrungVi = j + (i - j) / 2;
    long long giaTriTrungVi = aDaSapXep[viTriTrungVi];

    long long tongKhoangCach = 0;
    for (int t = j; t < i; t++) {
        tongKhoangCach += abs((long long)aDaSapXep[t] - giaTriTrungVi);
    }
    return tongKhoangCach;
}
// (Hàm này viết đơn giản O(doDaiNhom) mỗi lần gọi để dễ hiểu;
//  trong thực chiến có thể tối ưu bằng prefix sum để tính O(log n) hoặc O(1))

int main() {
    aDaSapXep = {1, 2, 4, 7, 10, 15};
    int n = (int)aDaSapXep.size();
    int k = 2;

    // Sử dụng lại khung chiaDeTriTinhDP đã viết ở "Code khung mẫu",
    // chỉ thay hàm chiPhiNhom bằng chiPhiKhoangCachToiTrungVi

    return 0;
}
```

#### PYTHON
```python
a_da_sap_xep = []


def chi_phi_khoang_cach_toi_trung_vi(j, i):
    vi_tri_trung_vi = j + (i - j) // 2
    gia_tri_trung_vi = a_da_sap_xep[vi_tri_trung_vi]

    tong_khoang_cach = 0
    for t in range(j, i):
        tong_khoang_cach += abs(a_da_sap_xep[t] - gia_tri_trung_vi)
    return tong_khoang_cach


a_da_sap_xep = [1, 2, 4, 7, 10, 15]
# Sử dụng lại khung chia_de_tri_tinh_dp đã viết ở "Code khung mẫu",
# chỉ thay hàm chi_phi_nhom bằng chi_phi_khoang_cach_toi_trung_vi
```

### Ví dụ 3 (Khó) — So sánh D&C Optimization với Convex Hull Trick: khi nào dùng cái nào?

**Phân tích (bảng so sánh, không code):**

| Tiêu chí | Convex Hull Trick (mục 10.2) | D&C Optimization (mục 10.3) |
|---|---|---|
| Điều kiện áp dụng | Chi phí khai triển được thành dạng đường thẳng (tuyến tính theo biến truy vấn) | Chi phí thoả bất đẳng thức tứ giác (không cần dạng tuyến tính) |
| Độ phức tạp | O(n) hoặc O(n log n) | O(n log n) cho mỗi "lớp" (VD: mỗi giá trị k trong bài toán m nhóm) |
| Độ khó cài đặt | Trung bình (cần khai triển công thức đúng) | Trung bình-khó (cần chứng minh/thử nghiệm bất đẳng thức tứ giác) |
| Phạm vi áp dụng | Hẹp hơn — chỉ áp dụng khi chi phí có cấu trúc đại số cụ thể | Rộng hơn — áp dụng được cho nhiều hàm chi phí "trực giác lồi" khác nhau, kể cả không có công thức đóng |

**Bài tập minh hoạ:** Với bài toán "chia mảng thành k nhóm, chi phí mỗi nhóm là bình phương tổng đoạn" (giống Ví dụ 2, mục 8.4 dạng interval DP, nhưng giờ với k nhóm thay vì tự do), hãy xác định: nên dùng CHT (mục 10.2) hay D&C Optimization (mục 10.3)?

**Lời giải:** Vì chi phí `(prefix[i]-prefix[j])²` khai triển trực tiếp được thành dạng đường thẳng theo biến `prefix[i]` (đã làm ở mục 10.2), đây là bài toán **phù hợp cả 2 kỹ thuật**. Tuy nhiên CHT thường được ưu tiên hơn khi độ phức tạp mong muốn là O(n) (không có hệ số log), trong khi D&C Optimization có thêm hệ số log nhưng dễ áp dụng hơn khi không chắc chắn công thức khai triển tuyến tính có đúng hay không (chỉ cần kiểm tra thực nghiệm bất đẳng thức tứ giác như Ví dụ 1).

---

## 10.4. Quy hoạch động theo chữ số (Digit DP)

**Nêu bài toán:** Cho 2 số nguyên L và R, hãy đếm số lượng số nguyên trong đoạn `[L, R]` mà **tổng các chữ số** của nó chia hết cho một số k cho trước.

**Phân tích vấn đề:** Nếu R lớn (ví dụ tới 10^18), duyệt trực tiếp từng số từ L đến R để kiểm tra là hoàn toàn không khả thi. Nhận xét quan trọng: bài toán "đếm số thoả điều kiện trong `[L, R]`" có thể quy về "đếm số thoả điều kiện trong `[0, R]`" trừ đi "đếm số thoả điều kiện trong `[0, L-1]`" — nên chỉ cần giải bài toán con "đếm số thoả điều kiện từ 0 đến N" cho 1 giá trị N bất kỳ.

**Giải pháp đơn thuần:** Duyệt từng số từ 0 đến N, tính tổng chữ số, kiểm tra chia hết. Độ phức tạp O(N) — không khả thi khi N tới 10^18.

**Khó khăn với giải pháp đơn thuần:** N có thể có tới 18-19 chữ số, số lượng giá trị cần duyệt là quá lớn để duyệt trực tiếp.

**Cách tiếp cận mới — Digit DP:** Thay vì duyệt từng số, ta xây dựng số cần đếm **từng chữ số một**, từ chữ số cao nhất tới chữ số thấp nhất, dùng đệ quy có nhớ (memoization). Trạng thái DP gồm: vị trí chữ số đang xét, thông tin cần theo dõi cho điều kiện bài toán (ở đây là tổng chữ số hiện tại, lấy dư theo k), và một cờ đặc biệt gọi là **"tight"** (bị chặn trên) — cho biết các chữ số đã chọn từ đầu tới giờ có **trùng khớp hoàn toàn** với N hay không (nếu có, chữ số tiếp theo bị giới hạn không được vượt quá chữ số tương ứng của N; nếu không, chữ số tiếp theo được tự do chọn từ 0-9).

### Minh hoạ lời giải chi tiết

Với N = 23, ta xây dựng số cần đếm từ chữ số hàng chục xuống hàng đơn vị (biểu diễn N = "23"):

- Vị trí 0 (hàng chục): nếu chọn chữ số **nhỏ hơn** 2 (tức 0 hoặc 1), số đang xây dựng chắc chắn nhỏ hơn N ở vị trí đầu, nên các chữ số sau được **tự do chọn** 0-9 (không bị ràng buộc — cờ "tight" tắt). Nếu chọn đúng chữ số 2 (bằng với N), cờ "tight" vẫn bật, vị trí tiếp theo vẫn bị giới hạn.
- Vị trí 1 (hàng đơn vị), nếu cờ "tight" đang bật (đã chọn đúng 2 ở vị trí trước): chỉ được chọn từ 0 đến 3 (chữ số tương ứng của N). Nếu cờ "tight" đã tắt: được chọn tự do 0-9.

Nhờ việc "ghi nhớ" theo cặp `(vị trí, tổng chữ số mod k, cờ tight)`, số lượng trạng thái cần tính chỉ là O(số_chữ_số × k × 2) — rất nhỏ so với việc duyệt N số trực tiếp.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> cacChuSo;
vector<vector<vector<long long>>> ghiNho; // ghiNho[viTri][tongModK][tight]
int kGlobal;

long long dfsDigitDP(int viTri, int tongModK, bool dangBiChan) {
    if (viTri == (int)cacChuSo.size()) {
        return (tongModK == 0) ? 1 : 0;
    }

    if (dangBiChan == false && ghiNho[viTri][tongModK][0] != -1) {
        return ghiNho[viTri][tongModK][0];
    }

    int chuSoLonNhatDuocChon;
    if (dangBiChan == true) {
        chuSoLonNhatDuocChon = cacChuSo[viTri];
    } else {
        chuSoLonNhatDuocChon = 9;
    }

    long long tongKetQua = 0;
    for (int chuSo = 0; chuSo <= chuSoLonNhatDuocChon; chuSo++) {
        bool tightMoi = (dangBiChan == true) && (chuSo == chuSoLonNhatDuocChon);
        int tongModKMoi = (tongModK + chuSo) % kGlobal;

        tongKetQua = tongKetQua + dfsDigitDP(viTri + 1, tongModKMoi, tightMoi);
    }

    if (dangBiChan == false) {
        ghiNho[viTri][tongModK][0] = tongKetQua;
    }

    return tongKetQua;
}

long long demSoThoaDieuKienTu0DenN(long long N, int k) {
    if (N < 0) {
        return 0;
    }

    string chuoiN = to_string(N);
    cacChuSo.clear();
    for (char c : chuoiN) {
        cacChuSo.push_back(c - '0');
    }

    kGlobal = k;
    ghiNho.assign(cacChuSo.size(), vector<vector<long long>>(k, vector<long long>(2, -1)));

    return dfsDigitDP(0, 0, true);
}

int main() {
    long long L = 1, R = 23;
    int k = 3;

    long long ketQua = demSoThoaDieuKienTu0DenN(R, k) - demSoThoaDieuKienTu0DenN(L - 1, k);
    cout << ketQua << endl;

    return 0;
}
```

#### PYTHON
```python
import sys
sys.setrecursionlimit(100000)

cac_chu_so = []
ghi_nho = {}
k_global = 0


def dfs_digit_dp(vi_tri, tong_mod_k, dang_bi_chan):
    if vi_tri == len(cac_chu_so):
        return 1 if tong_mod_k == 0 else 0

    trang_thai = (vi_tri, tong_mod_k, dang_bi_chan)
    if dang_bi_chan == False and trang_thai in ghi_nho:
        return ghi_nho[trang_thai]

    if dang_bi_chan == True:
        chu_so_lon_nhat_duoc_chon = cac_chu_so[vi_tri]
    else:
        chu_so_lon_nhat_duoc_chon = 9

    tong_ket_qua = 0
    for chu_so in range(0, chu_so_lon_nhat_duoc_chon + 1):
        tight_moi = (dang_bi_chan == True) and (chu_so == chu_so_lon_nhat_duoc_chon)
        tong_mod_k_moi = (tong_mod_k + chu_so) % k_global

        tong_ket_qua = tong_ket_qua + dfs_digit_dp(vi_tri + 1, tong_mod_k_moi, tight_moi)

    if dang_bi_chan == False:
        ghi_nho[trang_thai] = tong_ket_qua

    return tong_ket_qua


def dem_so_thoa_dieu_kien_tu_0_den_n(N, k):
    global cac_chu_so, ghi_nho, k_global

    if N < 0:
        return 0

    cac_chu_so = [int(c) for c in str(N)]
    k_global = k
    ghi_nho = {}

    return dfs_digit_dp(0, 0, True)


L, R = 1, 23
k = 3

ket_qua = dem_so_thoa_dieu_kien_tu_0_den_n(R, k) - dem_so_thoa_dieu_kien_tu_0_den_n(L - 1, k)
print(ket_qua)
```

### Ví dụ 1 (Dễ) — Đếm số lượng chữ số "7" xuất hiện từ 1 đến N

Bài toán: đếm tổng số lần chữ số 7 xuất hiện trong biểu diễn thập phân của tất cả các số từ 1 đến N.

**Phân tích:** Tương tự bài toán chính, nhưng thay vì đếm "số lượng số thoả điều kiện", ta đếm "tổng số lần xuất hiện" — trạng thái DP cần thêm một chiều để tích luỹ số lượng, không chỉ đếm 0/1.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> cacChuSoDem7;
vector<vector<long long>> ghiNhoDem7; // ghiNhoDem7[viTri][tight]

long long dfsDemChuSo7(int viTri, bool dangBiChan) {
    if (viTri == (int)cacChuSoDem7.size()) {
        return 0;
    }

    if (dangBiChan == false && ghiNhoDem7[viTri][0] != -1) {
        return ghiNhoDem7[viTri][0];
    }

    int chuSoLonNhat = dangBiChan ? cacChuSoDem7[viTri] : 9;
    long long tongKetQua = 0;

    for (int chuSo = 0; chuSo <= chuSoLonNhat; chuSo++) {
        bool tightMoi = dangBiChan && (chuSo == chuSoLonNhat);
        long long soLuongTu7CuaChuSoNay = (chuSo == 7) ? 1 : 0;

        tongKetQua = tongKetQua + soLuongTu7CuaChuSoNay + dfsDemChuSo7(viTri + 1, tightMoi);
        // Lưu ý: soLuongTu7CuaChuSoNay được cộng cho MỌI cách chọn các chữ số còn lại phía sau,
        // nhưng vì ta cộng nó ngay khi chọn chữ số này (trước khi đệ quy), công thức trên
        // thực chất cần nhân với số lượng cách chọn phần đuôi - sửa lại cho đúng bên dưới.
    }

    if (dangBiChan == false) {
        ghiNhoDem7[viTri][0] = tongKetQua;
    }

    return tongKetQua;
}

int main() {
    // Cách tính đúng cần đếm riêng "số lượng số" và "tổng số lần xuất hiện" - xem lời giải chi tiết
    // trong phần bài tập cuối chương để có bản đầy đủ chính xác.
    cout << "Xem loi giai chi tiet o bai tap cuoi chuong" << endl;
    return 0;
}
```

#### PYTHON
```python
# Ghi chú: cách đơn giản hoá ở ví dụ này có lỗi tinh vi (chưa nhân đúng số lượng cách
# chọn phần đuôi khi cộng dồn số lần xuất hiện chữ số 7).
# Bản đầy đủ, chính xác được trình bày lại trong Lời giải Bài 2, phần bài tập cuối chương.
print("Xem loi giai chi tiet o bai tap cuoi chuong")
```

> **Ghi chú sư phạm quan trọng:** ví dụ này cố ý để lộ ra một **lỗi thường gặp** khi mới học Digit DP — khi bài toán yêu cầu "đếm tổng số lần xuất hiện" (thay vì chỉ đếm "có/không thoả điều kiện"), công thức DP cần tách riêng thành 2 giá trị: số lượng cách hoàn thành phần đuôi, và tổng đóng góp từ phần đuôi — chứ không thể cộng dồn đơn giản như hàm `dfsDemChuSo7` phía trên. Lời giải đúng và đầy đủ được trình bày ở phần bài tập cuối chương (Bài 2).

### Ví dụ 2 (Trung bình) — Đếm số không có 2 chữ số liền kề bằng nhau

Bài toán: đếm số lượng số nguyên trong đoạn `[L, R]` mà biểu diễn thập phân của nó **không có 2 chữ số liền kề nào bằng nhau** (ví dụ 121 hợp lệ, nhưng 122 không hợp lệ vì có 2 chữ số "2" liền kề).

**Phân tích:** Cần thêm một chiều trạng thái: "chữ số vừa chọn ở bước trước là gì" — để kiểm tra chữ số tiếp theo có trùng không. Đồng thời cần xử lý riêng trường hợp **số 0 ở đầu** (không tính là "chữ số" thực sự khi số chưa "bắt đầu").

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> cacChuSoKhongTrung;
vector<vector<vector<vector<long long>>>> ghiNhoKhongTrung; // [viTri][chuSoTruoc+1][daBatDau][tight]

long long dfsKhongTrungLienKe(int viTri, int chuSoTruoc, bool daBatDau, bool dangBiChan) {
    if (viTri == (int)cacChuSoKhongTrung.size()) {
        return 1;
    }

    if (dangBiChan == false && ghiNhoKhongTrung[viTri][chuSoTruoc + 1][daBatDau][0] != -1) {
        return ghiNhoKhongTrung[viTri][chuSoTruoc + 1][daBatDau][0];
    }

    int chuSoLonNhat = dangBiChan ? cacChuSoKhongTrung[viTri] : 9;
    long long tongKetQua = 0;

    for (int chuSo = 0; chuSo <= chuSoLonNhat; chuSo++) {
        bool laSoKhongDauChuaBatDau = (daBatDau == false) && (chuSo == 0);

        if (laSoKhongDauChuaBatDau == false && chuSo == chuSoTruoc) {
            continue; // vi phạm điều kiện "không trùng chữ số liền kề"
        }

        bool tightMoi = dangBiChan && (chuSo == chuSoLonNhat);
        bool daBatDauMoi = daBatDau || (chuSo != 0);
        int chuSoTruocMoi = laSoKhongDauChuaBatDau ? -1 : chuSo;

        tongKetQua = tongKetQua + dfsKhongTrungLienKe(viTri + 1, chuSoTruocMoi, daBatDauMoi, tightMoi);
    }

    if (dangBiChan == false) {
        ghiNhoKhongTrung[viTri][chuSoTruoc + 1][daBatDau][0] = tongKetQua;
    }

    return tongKetQua;
}

long long demSoKhongTrungTu0DenN(long long N) {
    if (N < 0) return 0;

    string chuoiN = to_string(N);
    cacChuSoKhongTrung.clear();
    for (char c : chuoiN) cacChuSoKhongTrung.push_back(c - '0');

    int soLuongChuSo = (int)cacChuSoKhongTrung.size();
    ghiNhoKhongTrung.assign(soLuongChuSo,
        vector<vector<vector<long long>>>(11,
            vector<vector<long long>>(2, vector<long long>(2, -1))));

    return dfsKhongTrungLienKe(0, -1, false, true);
}

int main() {
    long long L = 1, R = 130;
    cout << demSoKhongTrungTu0DenN(R) - demSoKhongTrungTu0DenN(L - 1) << endl;
    return 0;
}
```

#### PYTHON
```python
import sys
sys.setrecursionlimit(100000)

cac_chu_so_khong_trung = []
ghi_nho_khong_trung = {}


def dfs_khong_trung_lien_ke(vi_tri, chu_so_truoc, da_bat_dau, dang_bi_chan):
    if vi_tri == len(cac_chu_so_khong_trung):
        return 1

    trang_thai = (vi_tri, chu_so_truoc, da_bat_dau, dang_bi_chan)
    if dang_bi_chan == False and trang_thai in ghi_nho_khong_trung:
        return ghi_nho_khong_trung[trang_thai]

    chu_so_lon_nhat = cac_chu_so_khong_trung[vi_tri] if dang_bi_chan else 9
    tong_ket_qua = 0

    for chu_so in range(0, chu_so_lon_nhat + 1):
        la_so_khong_dau_chua_bat_dau = (da_bat_dau == False) and (chu_so == 0)

        if la_so_khong_dau_chua_bat_dau == False and chu_so == chu_so_truoc:
            continue

        tight_moi = dang_bi_chan and (chu_so == chu_so_lon_nhat)
        da_bat_dau_moi = da_bat_dau or (chu_so != 0)
        chu_so_truoc_moi = -1 if la_so_khong_dau_chua_bat_dau else chu_so

        tong_ket_qua = tong_ket_qua + dfs_khong_trung_lien_ke(vi_tri + 1, chu_so_truoc_moi, da_bat_dau_moi, tight_moi)

    if dang_bi_chan == False:
        ghi_nho_khong_trung[trang_thai] = tong_ket_qua

    return tong_ket_qua


def dem_so_khong_trung_tu_0_den_n(N):
    global cac_chu_so_khong_trung, ghi_nho_khong_trung

    if N < 0:
        return 0

    cac_chu_so_khong_trung = [int(c) for c in str(N)]
    ghi_nho_khong_trung = {}

    return dfs_khong_trung_lien_ke(0, -1, False, True)


L, R = 1, 130
print(dem_so_khong_trung_tu_0_den_n(R) - dem_so_khong_trung_tu_0_den_n(L - 1))
```

### Ví dụ 3 (Khó) — Đếm số có tổng chữ số bằng đúng S và chia hết cho k đồng thời

Bài toán: đếm số lượng số nguyên trong `[L, R]` sao cho **đồng thời** tổng các chữ số bằng đúng S **và** bản thân số đó chia hết cho k.

**Phân tích:** Cần kết hợp **2 điều kiện đồng thời** trong trạng thái DP: tổng chữ số tích luỹ (so với S), và giá trị số hiện tại modulo k (để kiểm tra chia hết) — giá trị "số hiện tại mod k" được cập nhật bằng công thức `(giaTriModKCu × 10 + chuSoMoi) mod k` khi thêm 1 chữ số mới vào cuối.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> cacChuSoKetHop;
map<tuple<int,int,int,int>, long long> ghiNhoKetHop;
int kKetHop, sKetHop;

long long dfsKetHop(int viTri, int tongChuSo, int giaTriModK, bool dangBiChan) {
    if (tongChuSo > sKetHop) {
        return 0; // cắt tỉa: đã vượt quá tổng mong muốn
    }

    if (viTri == (int)cacChuSoKetHop.size()) {
        return (tongChuSo == sKetHop && giaTriModK == 0) ? 1 : 0;
    }

    auto trangThai = make_tuple(viTri, tongChuSo, giaTriModK, 0);
    if (dangBiChan == false && ghiNhoKetHop.count(trangThai) > 0) {
        return ghiNhoKetHop[trangThai];
    }

    int chuSoLonNhat = dangBiChan ? cacChuSoKetHop[viTri] : 9;
    long long tongKetQua = 0;

    for (int chuSo = 0; chuSo <= chuSoLonNhat; chuSo++) {
        bool tightMoi = dangBiChan && (chuSo == chuSoLonNhat);
        int giaTriModKMoi = (giaTriModK * 10 + chuSo) % kKetHop;

        tongKetQua = tongKetQua + dfsKetHop(viTri + 1, tongChuSo + chuSo, giaTriModKMoi, tightMoi);
    }

    if (dangBiChan == false) {
        ghiNhoKetHop[trangThai] = tongKetQua;
    }

    return tongKetQua;
}

long long demSoKetHopTu0DenN(long long N, int S, int k) {
    if (N < 0) return 0;

    string chuoiN = to_string(N);
    cacChuSoKetHop.clear();
    for (char c : chuoiN) cacChuSoKetHop.push_back(c - '0');

    kKetHop = k;
    sKetHop = S;
    ghiNhoKetHop.clear();

    return dfsKetHop(0, 0, 0, true);
}

int main() {
    long long L = 1, R = 1000;
    int S = 6, k = 3;

    cout << demSoKetHopTu0DenN(R, S, k) - demSoKetHopTu0DenN(L - 1, S, k) << endl;

    return 0;
}
```

#### PYTHON
```python
import sys
sys.setrecursionlimit(100000)

cac_chu_so_ket_hop = []
ghi_nho_ket_hop = {}
k_ket_hop = 0
s_ket_hop = 0


def dfs_ket_hop(vi_tri, tong_chu_so, gia_tri_mod_k, dang_bi_chan):
    if tong_chu_so > s_ket_hop:
        return 0

    if vi_tri == len(cac_chu_so_ket_hop):
        return 1 if (tong_chu_so == s_ket_hop and gia_tri_mod_k == 0) else 0

    trang_thai = (vi_tri, tong_chu_so, gia_tri_mod_k)
    if dang_bi_chan == False and trang_thai in ghi_nho_ket_hop:
        return ghi_nho_ket_hop[trang_thai]

    chu_so_lon_nhat = cac_chu_so_ket_hop[vi_tri] if dang_bi_chan else 9
    tong_ket_qua = 0

    for chu_so in range(0, chu_so_lon_nhat + 1):
        tight_moi = dang_bi_chan and (chu_so == chu_so_lon_nhat)
        gia_tri_mod_k_moi = (gia_tri_mod_k * 10 + chu_so) % k_ket_hop

        tong_ket_qua = tong_ket_qua + dfs_ket_hop(vi_tri + 1, tong_chu_so + chu_so, gia_tri_mod_k_moi, tight_moi)

    if dang_bi_chan == False:
        ghi_nho_ket_hop[trang_thai] = tong_ket_qua

    return tong_ket_qua


def dem_so_ket_hop_tu_0_den_n(N, S, k):
    global cac_chu_so_ket_hop, ghi_nho_ket_hop, k_ket_hop, s_ket_hop

    if N < 0:
        return 0

    cac_chu_so_ket_hop = [int(c) for c in str(N)]
    k_ket_hop = k
    s_ket_hop = S
    ghi_nho_ket_hop = {}

    return dfs_ket_hop(0, 0, 0, True)


L, R = 1, 1000
S, k = 6, 3

print(dem_so_ket_hop_tu_0_den_n(R, S, k) - dem_so_ket_hop_tu_0_den_n(L - 1, S, k))
```

**Bài tập minh hoạ:** Đếm số lượng số trong `[L, R]` có chứa **ít nhất 1 chữ số 0** trong biểu diễn thập phân.

**Lời giải:** Cách dễ nhất: đếm phần bù — "tổng số lượng số trong `[L,R]`" trừ đi "số lượng số **không chứa** chữ số 0 nào".

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> cacChuSoKhong0;
vector<vector<long long>> ghiNhoKhong0;

long long dfsKhongCoChuSo0(int viTri, bool daBatDau, bool dangBiChan) {
    if (viTri == (int)cacChuSoKhong0.size()) {
        return 1;
    }

    if (dangBiChan == false && ghiNhoKhong0[viTri][daBatDau ? 1 : 0] != -1) {
        return ghiNhoKhong0[viTri][daBatDau ? 1 : 0];
    }

    int chuSoLonNhat = dangBiChan ? cacChuSoKhong0[viTri] : 9;
    long long tongKetQua = 0;

    for (int chuSo = 0; chuSo <= chuSoLonNhat; chuSo++) {
        bool laSoKhongDauChuaBatDau = (daBatDau == false) && (chuSo == 0);
        if (laSoKhongDauChuaBatDau == false && chuSo == 0) {
            continue; // đã bắt đầu số thực sự nhưng gặp chữ số 0 -> loại (đây là "không chứa số 0")
        }

        bool tightMoi = dangBiChan && (chuSo == chuSoLonNhat);
        bool daBatDauMoi = daBatDau || (chuSo != 0);

        tongKetQua = tongKetQua + dfsKhongCoChuSo0(viTri + 1, daBatDauMoi, tightMoi);
    }

    if (dangBiChan == false) {
        ghiNhoKhong0[viTri][daBatDau ? 1 : 0] = tongKetQua;
    }

    return tongKetQua;
}

long long demKhongCoChuSo0Tu0DenN(long long N) {
    if (N < 0) return 0;
    string chuoiN = to_string(N);
    cacChuSoKhong0.clear();
    for (char c : chuoiN) cacChuSoKhong0.push_back(c - '0');

    ghiNhoKhong0.assign(cacChuSoKhong0.size(), vector<long long>(2, -1));
    return dfsKhongCoChuSo0(0, false, true);
}

int main() {
    long long L = 1, R = 50;

    long long tongSoLuong = R - L + 1;
    long long soLuongKhongCo0 = demKhongCoChuSo0Tu0DenN(R) - demKhongCoChuSo0Tu0DenN(L - 1);
    long long soLuongCoIt1So0 = tongSoLuong - soLuongKhongCo0;

    cout << soLuongCoIt1So0 << endl;

    return 0;
}
```

#### PYTHON
```python
cac_chu_so_khong_0 = []
ghi_nho_khong_0 = {}


def dfs_khong_co_chu_so_0(vi_tri, da_bat_dau, dang_bi_chan):
    if vi_tri == len(cac_chu_so_khong_0):
        return 1

    trang_thai = (vi_tri, da_bat_dau)
    if dang_bi_chan == False and trang_thai in ghi_nho_khong_0:
        return ghi_nho_khong_0[trang_thai]

    chu_so_lon_nhat = cac_chu_so_khong_0[vi_tri] if dang_bi_chan else 9
    tong_ket_qua = 0

    for chu_so in range(0, chu_so_lon_nhat + 1):
        la_so_khong_dau_chua_bat_dau = (da_bat_dau == False) and (chu_so == 0)
        if la_so_khong_dau_chua_bat_dau == False and chu_so == 0:
            continue

        tight_moi = dang_bi_chan and (chu_so == chu_so_lon_nhat)
        da_bat_dau_moi = da_bat_dau or (chu_so != 0)

        tong_ket_qua = tong_ket_qua + dfs_khong_co_chu_so_0(vi_tri + 1, da_bat_dau_moi, tight_moi)

    if dang_bi_chan == False:
        ghi_nho_khong_0[trang_thai] = tong_ket_qua

    return tong_ket_qua


def dem_khong_co_chu_so_0_tu_0_den_n(N):
    global cac_chu_so_khong_0, ghi_nho_khong_0

    if N < 0:
        return 0

    cac_chu_so_khong_0 = [int(c) for c in str(N)]
    ghi_nho_khong_0 = {}

    return dfs_khong_co_chu_so_0(0, False, True)


L, R = 1, 50

tong_so_luong = R - L + 1
so_luong_khong_co_0 = dem_khong_co_chu_so_0_tu_0_den_n(R) - dem_khong_co_chu_so_0_tu_0_den_n(L - 1)
so_luong_co_it_1_so_0 = tong_so_luong - so_luong_khong_co_0

print(so_luong_co_it_1_so_0)
```

---

## Tổng kết Chương 10

- **Tối ưu hoá DP bằng Monotonic Queue:** áp dụng khi công thức DP có dạng `dp[i] = a[i] + tối_ưu(dp[j])` với `j` giới hạn trong một **cửa sổ kích thước cố định**, giảm độ phức tạp từ O(n×k) xuống O(n).
- **Convex Hull Trick:** áp dụng khi công thức DP khai triển được thành dạng **đường thẳng** theo biến truy vấn (`dp[j] + m(j)×x(i) + b(j)`), với `j` không giới hạn trong cửa sổ — giảm độ phức tạp từ O(n²) xuống O(n) hoặc O(n log n), nhờ duy trì "vỏ lồi" các đường thẳng.
- **Divide and Conquer Optimization:** áp dụng khi hàm chi phí thoả **bất đẳng thức tứ giác**, dựa vào tính đơn điệu của vị trí tối ưu `opt[i]` để giảm độ phức tạp mỗi "lớp" DP từ O(n²) xuống O(n log n).
- **Digit DP:** áp dụng cho bài toán đếm số lượng (hoặc tổng hợp thông tin) các số nguyên trong một khoảng `[L, R]` rất lớn, thoả điều kiện liên quan tới **các chữ số** — xây dựng số cần đếm từng chữ số một, với trạng thái luôn bao gồm "cờ tight" (bị chặn trên) và các thông tin cần theo dõi riêng cho từng bài toán cụ thể.
- **Kỹ năng cốt lõi cần luyện:** cả 4 kỹ thuật trong chương này đều là "tầng tối ưu hoá" xây dựng **trên nền** một công thức DP cơ bản (Chương 8, 9) đã đúng nhưng chưa đủ nhanh — luôn viết đúng công thức DP O(n²) hoặc chậm hơn trước, xác nhận nó cho kết quả đúng, rồi mới tìm cách tối ưu hoá bằng 1 trong 4 kỹ thuật này, thay vì cố gắng viết trực tiếp bản tối ưu ngay từ đầu (rất dễ sai sót).

---

## Bài tập cuối chương — Chương 10

### PHẦN A: ĐỀ BÀI (14 bài, không kèm lời giải)

**Bài 1 — Constrained Subsequence Sum (LeetCode 1425, xem lại) — Khó**
Giống hệt bài đã giải ở Chương 6 (Bài 17) và liên hệ lại với mục 10.1 — hãy viết lại dưới dạng tổng quát của "tối ưu hoá DP bằng hàng đợi đơn điệu".

**Bài 2 — Đếm tổng số lần xuất hiện chữ số 7 (tự thiết kế, sửa lỗi Ví dụ 1 mục 10.4) — Trung bình**
Viết lại đúng và đầy đủ lời giải cho Ví dụ 1, mục 10.4 — đếm tổng số lần chữ số 7 xuất hiện trong biểu diễn thập phân của tất cả các số từ 1 đến N. (Gợi ý: trạng thái DP cần trả về một **cặp** giá trị: số lượng cách hoàn thành, và tổng số lần xuất hiện chữ số 7 tích luỹ từ các cách đó.)

**Bài 3 — Cutting Sticks / Optimal Merge Cost (dạng phổ biến) — Trung bình**
Cho n khúc gỗ có độ dài cho trước cần cắt tại các điểm cho sẵn, chi phí mỗi lần cắt bằng độ dài đoạn gỗ hiện tại đang cắt. Tìm chi phí nhỏ nhất để cắt hết theo đúng thứ tự (đây là Interval DP đã học ở Chương 8, nhưng hãy thử áp dụng D&C Optimization nếu số điểm cắt lớn).

**Bài 4 — Batch Scheduling (bài toán kinh điển, dạng IOI) — Khó**
Có n công việc phải xử lý theo lô (mỗi lô là 1 đoạn công việc liên tiếp), chi phí xử lý 1 lô bao gồm 1 hằng số S (setup) cộng thêm tổng thời gian hoàn thành của các công việc trong lô (thời gian hoàn thành của công việc phụ thuộc vào toàn bộ thời gian các lô trước đó). Tìm cách chia lô để tổng chi phí là nhỏ nhất. (Đây chính là bài toán CHT kinh điật — thử tự khai triển công thức trước khi xem gợi ý.)

**Bài 5 — Chia mảng thành K nhóm tối thiểu hoá tổng bình phương (tự thiết kế) — Trung bình-Khó**
Giống hệt Code khung mẫu, mục 10.2 (Convex Hull Trick) nhưng giới hạn số nhóm phải đúng bằng k (không phải tự do như bài gốc).

**Bài 6 — Chia dãy số thành K đoạn tối ưu bằng D&C Optimization (CSES dạng mở rộng của 1080/1085) — Khó**
Giống hệt Code khung mẫu, mục 10.3 đã trình bày (dùng để luyện tập lại với hàm chi phí khác).

**Bài 7 — Đếm số Palindrome trong khoảng [L, R] (Digit DP dạng đối xứng) — Khó**
Đếm số lượng số nguyên trong đoạn `[L, R]` mà biểu diễn thập phân của nó là một chuỗi đối xứng (palindrome). (Gợi ý: có thể không cần Digit DP đầy đủ — palindrome có cấu trúc đặc biệt cho phép sinh trực tiếp; nhưng hãy thử giải bằng Digit DP để luyện kỹ thuật.)

**Bài 8 — Numbers With Repeated Digits (LeetCode 1012) — Khó**
Đếm số lượng số nguyên trong đoạn `[1, N]` có **ít nhất 1 chữ số lặp lại**. (Gợi ý: đếm phần bù — số lượng không có chữ số lặp lại, tương tự cách làm ở Bài tập minh hoạ, mục 10.4, nhưng trạng thái DP cần theo dõi tập chữ số đã dùng bằng bitmask, kết hợp Chương 9.)

**Bài 9 — Non-decreasing Digits Count (tự thiết kế) — Trung bình**
Đếm số lượng số nguyên trong đoạn `[L, R]` mà các chữ số của nó không giảm dần từ trái qua phải (ví dụ 1234, 1122 hợp lệ; 1213 không hợp lệ).

**Bài 10 — Rounding trong bài toán chia lịch trực (tự thiết kế theo dạng đề thi HSG) — Khó**
n nhân viên trực ca theo thứ tự, mỗi lần chia ca (đoạn nhân viên liên tiếp) có chi phí là **giá trị tuyệt đối của hiệu** giữa số nhân viên trong ca này và số nhân viên trung bình lý tưởng mỗi ca. Tìm cách chia thành đúng k ca để tổng chi phí nhỏ nhất. (Gợi ý: kiểm tra hàm chi phí có thoả bất đẳng thức tứ giác không, dùng phương pháp thực nghiệm như Ví dụ 1, mục 10.3.)

**Bài 11 — CSES 1163 Houses (biến thể, mở rộng độ khó) — Trung bình**
Có n ngôi nhà trên 1 đường thẳng và m máy phát wifi, mỗi máy có bán kính phủ sóng nhất định phải được đặt tại 1 vị trí nào đó trên đường thẳng (không nhất thiết trùng nhà), sao cho khoảng cách từ mỗi nhà tới máy gần nhất là nhỏ nhất có thể — trong số cách đặt tối ưu, tìm giá trị lớn nhất của khoảng cách này (minimize the maximum). Đây là dạng bài kết hợp Binary Search on Answer (Chương 4) với D&C Optimization để kiểm tra tính khả thi nhanh hơn.

**Bài 12 — Đếm số có tổng chữ số là số nguyên tố (tự thiết kế) — Trung bình-Khó**
Đếm số lượng số nguyên trong đoạn `[L, R]` mà tổng các chữ số của nó là một số nguyên tố. (Gợi ý: kết hợp Digit DP với sàng nguyên tố — Chương 17 — để kiểm tra nhanh 1 tổng có phải số nguyên tố hay không.)

**Bài 13 — Frog Jump với chi phí bậc 2 (tự thiết kế, kết hợp CHT) — Khó**
Một con ếch đứng ở vị trí 0 trên trục số, muốn nhảy tới vị trí n. Từ vị trí `i`, ếch có thể nhảy tới bất kỳ vị trí `j > i` nào, chi phí nhảy là `(j - i - k)²` (k là "khoảng nhảy lý tưởng" cho trước, phạt nếu nhảy quá xa hoặc quá gần so với lý tưởng). Tìm chi phí nhỏ nhất để tới đích. (Gợi ý: khai triển bình phương, áp dụng CHT.)

**Bài 14 — Tổng hợp: đếm số trong khoảng thoả nhiều điều kiện cùng lúc (tự thiết kế, dạng đề thi ICPC/HSG) — Khó**
Đếm số lượng số nguyên trong đoạn `[L, R]` (R có thể tới 10^18) sao cho: tổng các chữ số chia hết cho 3, **và** số đó không chứa 2 chữ số "4" liên tiếp, **và** chữ số đầu tiên khác chữ số cuối cùng. (Bài rèn kỹ năng kết hợp nhiều điều kiện trong 1 trạng thái Digit DP.)

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Constrained Subsequence Sum (liên hệ tổng quát)</summary>

Xem lại lời giải đầy đủ ở Chương 6 (Bài 17). Về bản chất, công thức `dp[i] = a[i] + max(0, max(dp[j]))` với `j` trong `[i-k, i-1]` chính là dạng chuẩn của mục 10.1 — khung `hangDoi`/`hang_doi` dùng đúng kỹ thuật monotonic deque đã hệ thống hoá trong chương này.
</details>

<details>
<summary>Lời giải Bài 2 — Đếm tổng số lần xuất hiện chữ số 7 (bản đúng)</summary>

**Ý tưởng đúng:** hàm đệ quy trả về **cặp giá trị** `(soLuongCachHoanThanh, tongSoLanXuatHien7)` thay vì chỉ 1 giá trị.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> cacChuSo;
map<pair<int,int>, pair<long long,long long>> ghiNho; // (viTri, tight) -> (soLuongCach, tongSoLan7)

pair<long long,long long> dfsDemChuSo7Dung(int viTri, bool dangBiChan) {
    if (viTri == (int)cacChuSo.size()) {
        return {1, 0}; // 1 cách hoàn thành (số rỗng còn lại), 0 lần xuất hiện thêm
    }

    auto trangThai = make_pair(viTri, dangBiChan ? 1 : 0);
    if (dangBiChan == false && ghiNho.count(trangThai) > 0) {
        return ghiNho[trangThai];
    }

    int chuSoLonNhat = dangBiChan ? cacChuSo[viTri] : 9;
    long long tongSoLuongCach = 0;
    long long tongSoLan7 = 0;

    for (int chuSo = 0; chuSo <= chuSoLonNhat; chuSo++) {
        bool tightMoi = dangBiChan && (chuSo == chuSoLonNhat);
        auto ketQuaDuoi = dfsDemChuSo7Dung(viTri + 1, tightMoi);

        long long soLuongCachDuoi = ketQuaDuoi.first;
        long long soLan7Duoi = ketQuaDuoi.second;

        tongSoLuongCach += soLuongCachDuoi;
        // Đóng góp của chữ số hiện tại: nếu chữ số này là 7, nó xuất hiện thêm 1 lần
        // cho MỖI cách hoàn thành phần đuôi -> nhân với soLuongCachDuoi
        long long dongGopChuSoNay = (chuSo == 7) ? soLuongCachDuoi : 0;
        tongSoLan7 += soLan7Duoi + dongGopChuSoNay;
    }

    pair<long long,long long> ketQua = {tongSoLuongCach, tongSoLan7};
    if (dangBiChan == false) {
        ghiNho[trangThai] = ketQua;
    }
    return ketQua;
}

long long demChuSo7Tu0DenN(long long N) {
    if (N < 0) return 0;
    string chuoiN = to_string(N);
    cacChuSo.clear();
    for (char c : chuoiN) cacChuSo.push_back(c - '0');
    ghiNho.clear();

    return dfsDemChuSo7Dung(0, true).second;
}

int main() {
    cout << demChuSo7Tu0DenN(100) << endl; // ví dụ: từ 1-100 có 20 lần chữ số 7 xuất hiện
    return 0;
}
```

#### PYTHON
```python
cac_chu_so = []
ghi_nho = {}


def dfs_dem_chu_so_7_dung(vi_tri, dang_bi_chan):
    if vi_tri == len(cac_chu_so):
        return (1, 0)

    trang_thai = (vi_tri, dang_bi_chan)
    if dang_bi_chan == False and trang_thai in ghi_nho:
        return ghi_nho[trang_thai]

    chu_so_lon_nhat = cac_chu_so[vi_tri] if dang_bi_chan else 9
    tong_so_luong_cach = 0
    tong_so_lan_7 = 0

    for chu_so in range(0, chu_so_lon_nhat + 1):
        tight_moi = dang_bi_chan and (chu_so == chu_so_lon_nhat)
        so_luong_cach_duoi, so_lan_7_duoi = dfs_dem_chu_so_7_dung(vi_tri + 1, tight_moi)

        tong_so_luong_cach += so_luong_cach_duoi
        dong_gop_chu_so_nay = so_luong_cach_duoi if chu_so == 7 else 0
        tong_so_lan_7 += so_lan_7_duoi + dong_gop_chu_so_nay

    ket_qua = (tong_so_luong_cach, tong_so_lan_7)
    if dang_bi_chan == False:
        ghi_nho[trang_thai] = ket_qua
    return ket_qua


def dem_chu_so_7_tu_0_den_n(N):
    global cac_chu_so, ghi_nho

    if N < 0:
        return 0

    cac_chu_so = [int(c) for c in str(N)]
    ghi_nho = {}

    return dfs_dem_chu_so_7_dung(0, True)[1]


print(dem_chu_so_7_tu_0_den_n(100))
```
</details>

<details>
<summary>Lời giải Bài 3 — Cutting Sticks</summary>

Đây là Interval DP chuẩn (giống Ví dụ 2, mục 8.4 — Matrix Chain Multiplication), không bắt buộc D&C Optimization nếu số điểm cắt nhỏ. Với số điểm cắt lớn, có thể thử kiểm tra bất đẳng thức tứ giác cho hàm chi phí `chiPhi(trai, phai) = do_dai_doan(trai, phai)` bằng phương pháp thực nghiệm tương tự Ví dụ 1, mục 10.3, rồi áp dụng khung `chiaDeTriTinhDP`.
</details>

<details>
<summary>Lời giải Bài 4 — Batch Scheduling</summary>

**Ý tưởng:** Đặt `T[i]` là tổng thời gian xử lý i công việc đầu (mảng cộng dồn). Nếu lô cuối cùng là từ `j+1` đến `i`, thời gian hoàn thành của lô này là `T[i]` (tính từ đầu), và nó phải cộng thêm setup S. Chi phí đóng góp vào "tổng thời gian hoàn thành các công việc" của lô `[j+1,i]` là `(i-j) × (T[j] + S)` cộng thêm phần nội tại của lô đó. Khai triển đúng công thức sẽ cho dạng đường thẳng theo biến "thời gian bắt đầu lô" — đây là bài toán CHT kinh điển, cấu trúc lời giải tương tự hệt Code khung mẫu, mục 10.2, chỉ khác công thức khai triển cụ thể (khuyến khích học sinh tự làm bài tập khai triển công thức trước khi xem đáp án đầy đủ trên các nguồn tài liệu chuyên sâu về CHT).
</details>

<details>
<summary>Lời giải Bài 5 — Chia K nhóm tối thiểu hoá tổng bình phương</summary>

Kết hợp khung "vòng lặp ngoài theo số nhóm" (giống D&C Optimization, mục 10.3) với CHT bên trong mỗi lớp (thay vì D&C bên trong):

#### C++
```cpp
// Với mỗi lớp "soNhom", dùng lại cấu trúc CHT y hệt Code khung mẫu mục 10.2,
// nhưng dp[soNhom-1] đóng vai trò "dpLopTruoc" và dp[soNhom] là "dpLopHienTai".
// Vì cấu trúc lặp lại y hệt mục 10.2, không lặp lại toàn bộ code ở đây -
// điểm khác biệt duy nhất là vòng lặp ngoài chạy đúng k lần (số nhóm cố định).
```

#### PYTHON
```python
# Tương tự như trên - áp dụng lại cấu trúc CHT (mục 10.2) bên trong vòng lặp k lớp.
```
</details>

<details>
<summary>Lời giải Bài 6 — Chia K đoạn bằng D&C Optimization</summary>

Giống hệt Code khung mẫu, mục 10.3, hàm `chiaDeTriTinhDP`/`chia_de_tri_tinh_dp` — chỉ cần thay hàm `chiPhiNhom`/`chi_phi_nhom` theo đúng yêu cầu đề bài cụ thể.
</details>

<details>
<summary>Lời giải Bài 7 — Đếm số Palindrome trong khoảng</summary>

**Ý tưởng đơn giản hơn Digit DP đầy đủ:** với mỗi độ dài `d`, số palindrome độ dài `d` được xác định hoàn toàn bởi **nửa đầu** của nó (nửa sau là ảnh gương của nửa đầu) — nên có thể **sinh trực tiếp** mọi palindrome có độ dài ≤ số chữ số của R, rồi đếm những cái nằm trong `[L,R]`, thay vì Digit DP đầy đủ.

#### C++
```cpp
long long taoPalindromeTuNuaDau(long long nuaDau, int doDai) {
    string s = to_string(nuaDau);
    string ketQua = s;
    string phanDaoNguoc = s;
    reverse(phanDaoNguoc.begin(), phanDaoNguoc.end());

    if (doDai % 2 == 0) {
        ketQua += phanDaoNguoc;
    } else {
        ketQua += phanDaoNguoc.substr(1);
    }
    return stoll(ketQua);
}

long long demPalindromeTrongKhoang(long long L, long long R) {
    long long dem = 0;

    for (int doDai = 1; doDai <= 18; doDai++) {
        int doDaiNuaDau = (doDai + 1) / 2;
        long long batDauNuaDau = (doDaiNuaDau == 1) ? 0 : (long long)pow(10, doDaiNuaDau - 1);
        long long ketThucNuaDau = (long long)pow(10, doDaiNuaDau) - 1;

        for (long long nuaDau = max(batDauNuaDau, 1LL); nuaDau <= ketThucNuaDau; nuaDau++) {
            long long palindrome = taoPalindromeTuNuaDau(nuaDau, doDai);
            if (palindrome >= L && palindrome <= R) {
                dem++;
            }
        }
    }

    return dem;
}
```

#### PYTHON
```python
def tao_palindrome_tu_nua_dau(nua_dau, do_dai):
    s = str(nua_dau)
    phan_dao_nguoc = s[::-1]

    if do_dai % 2 == 0:
        ket_qua = s + phan_dao_nguoc
    else:
        ket_qua = s + phan_dao_nguoc[1:]
    return int(ket_qua)


def dem_palindrome_trong_khoang(L, R):
    dem = 0

    for do_dai in range(1, 19):
        do_dai_nua_dau = (do_dai + 1) // 2
        bat_dau_nua_dau = 0 if do_dai_nua_dau == 1 else 10 ** (do_dai_nua_dau - 1)
        ket_thuc_nua_dau = 10 ** do_dai_nua_dau - 1

        for nua_dau in range(max(bat_dau_nua_dau, 1), ket_thuc_nua_dau + 1):
            palindrome = tao_palindrome_tu_nua_dau(nua_dau, do_dai)
            if L <= palindrome <= R:
                dem += 1

    return dem
```
> **Ghi chú:** cách "sinh trực tiếp" này hiệu quả hơn Digit DP cho riêng dạng palindrome, nhưng việc thử cả 2 hướng tiếp cận (Digit DP tổng quát vs. sinh trực tiếp tận dụng cấu trúc đặc thù) là một bài học quan trọng: đôi khi cấu trúc đặc biệt của bài toán cho phép một lời giải đơn giản hơn kỹ thuật tổng quát.
</details>

<details>
<summary>Lời giải Bài 8 — Numbers With Repeated Digits</summary>

**Ý tưởng:** đếm phần bù — số lượng có **tất cả chữ số phân biệt**, dùng bitmask (Chương 9) lưu tập chữ số đã dùng trong trạng thái Digit DP.

#### C++
```cpp
vector<int> cacChuSoKhongLap;
map<tuple<int,int,int>, long long> ghiNhoKhongLap; // (viTri, mask, daBatDau)

long long dfsKhongLapChuSo(int viTri, int mask, bool daBatDau, bool dangBiChan) {
    if (viTri == (int)cacChuSoKhongLap.size()) {
        return 1;
    }

    auto trangThai = make_tuple(viTri, mask, daBatDau ? 1 : 0);
    if (dangBiChan == false && ghiNhoKhongLap.count(trangThai) > 0) {
        return ghiNhoKhongLap[trangThai];
    }

    int chuSoLonNhat = dangBiChan ? cacChuSoKhongLap[viTri] : 9;
    long long tongKetQua = 0;

    for (int chuSo = 0; chuSo <= chuSoLonNhat; chuSo++) {
        bool laSoKhongDauChuaBatDau = (daBatDau == false) && (chuSo == 0);

        if (laSoKhongDauChuaBatDau == false && ((mask >> chuSo) & 1)) {
            continue; // chữ số này đã dùng rồi -> vi phạm "không lặp"
        }

        bool tightMoi = dangBiChan && (chuSo == chuSoLonNhat);
        bool daBatDauMoi = daBatDau || (chuSo != 0);
        int maskMoi = laSoKhongDauChuaBatDau ? mask : (mask | (1 << chuSo));

        tongKetQua += dfsKhongLapChuSo(viTri + 1, maskMoi, daBatDauMoi, tightMoi);
    }

    if (dangBiChan == false) {
        ghiNhoKhongLap[trangThai] = tongKetQua;
    }
    return tongKetQua;
}

long long demSoKhongLapTu1DenN(long long N) {
    string chuoiN = to_string(N);
    cacChuSoKhongLap.clear();
    for (char c : chuoiN) cacChuSoKhongLap.push_back(c - '0');
    ghiNhoKhongLap.clear();

    return dfsKhongLapChuSo(0, 0, false, true) - 1; // trừ 1 vì tính cả số 0
}

long long demSoLapTu1DenN(long long N) {
    return N - demSoKhongLapTu1DenN(N);
}
```

#### PYTHON
```python
cac_chu_so_khong_lap = []
ghi_nho_khong_lap = {}


def dfs_khong_lap_chu_so(vi_tri, mask, da_bat_dau, dang_bi_chan):
    if vi_tri == len(cac_chu_so_khong_lap):
        return 1

    trang_thai = (vi_tri, mask, da_bat_dau)
    if dang_bi_chan == False and trang_thai in ghi_nho_khong_lap:
        return ghi_nho_khong_lap[trang_thai]

    chu_so_lon_nhat = cac_chu_so_khong_lap[vi_tri] if dang_bi_chan else 9
    tong_ket_qua = 0

    for chu_so in range(0, chu_so_lon_nhat + 1):
        la_so_khong_dau_chua_bat_dau = (da_bat_dau == False) and (chu_so == 0)

        if la_so_khong_dau_chua_bat_dau == False and ((mask >> chu_so) & 1):
            continue

        tight_moi = dang_bi_chan and (chu_so == chu_so_lon_nhat)
        da_bat_dau_moi = da_bat_dau or (chu_so != 0)
        mask_moi = mask if la_so_khong_dau_chua_bat_dau else (mask | (1 << chu_so))

        tong_ket_qua += dfs_khong_lap_chu_so(vi_tri + 1, mask_moi, da_bat_dau_moi, tight_moi)

    if dang_bi_chan == False:
        ghi_nho_khong_lap[trang_thai] = tong_ket_qua
    return tong_ket_qua


def dem_so_khong_lap_tu_1_den_n(N):
    global cac_chu_so_khong_lap, ghi_nho_khong_lap

    cac_chu_so_khong_lap = [int(c) for c in str(N)]
    ghi_nho_khong_lap = {}

    return dfs_khong_lap_chu_so(0, 0, False, True) - 1


def dem_so_lap_tu_1_den_n(N):
    return N - dem_so_khong_lap_tu_1_den_n(N)
```
</details>

<details>
<summary>Lời giải Bài 9 — Non-decreasing Digits Count</summary>

**Ý tưởng:** tương tự Ví dụ 2, mục 10.4 nhưng đổi điều kiện từ "không trùng liền kề" thành "chữ số sau ≥ chữ số trước".

#### C++
```cpp
vector<int> cacChuSoTangDan;
map<tuple<int,int,int>, long long> ghiNhoTangDan;

long long dfsKhongGiamDan(int viTri, int chuSoTruoc, bool dangBiChan) {
    if (viTri == (int)cacChuSoTangDan.size()) {
        return 1;
    }

    auto trangThai = make_tuple(viTri, chuSoTruoc, 0);
    if (dangBiChan == false && ghiNhoTangDan.count(trangThai) > 0) {
        return ghiNhoTangDan[trangThai];
    }

    int chuSoLonNhat = dangBiChan ? cacChuSoTangDan[viTri] : 9;
    long long tongKetQua = 0;

    for (int chuSo = chuSoTruoc; chuSo <= chuSoLonNhat; chuSo++) {
        bool tightMoi = dangBiChan && (chuSo == chuSoLonNhat);
        tongKetQua += dfsKhongGiamDan(viTri + 1, chuSo, tightMoi);
    }

    if (dangBiChan == false) {
        ghiNhoTangDan[trangThai] = tongKetQua;
    }
    return tongKetQua;
}
```

#### PYTHON
```python
cac_chu_so_tang_dan = []
ghi_nho_tang_dan = {}


def dfs_khong_giam_dan(vi_tri, chu_so_truoc, dang_bi_chan):
    if vi_tri == len(cac_chu_so_tang_dan):
        return 1

    trang_thai = (vi_tri, chu_so_truoc)
    if dang_bi_chan == False and trang_thai in ghi_nho_tang_dan:
        return ghi_nho_tang_dan[trang_thai]

    chu_so_lon_nhat = cac_chu_so_tang_dan[vi_tri] if dang_bi_chan else 9
    tong_ket_qua = 0

    for chu_so in range(chu_so_truoc, chu_so_lon_nhat + 1):
        tight_moi = dang_bi_chan and (chu_so == chu_so_lon_nhat)
        tong_ket_qua += dfs_khong_giam_dan(vi_tri + 1, chu_so, tight_moi)

    if dang_bi_chan == False:
        ghi_nho_tang_dan[trang_thai] = tong_ket_qua
    return tong_ket_qua
```
> **Lưu ý:** chữ số bắt đầu nên là 0 để cho phép chữ số đầu tiên là bất kỳ giá trị nào (không bắt buộc ≥ 1 chữ số trước không tồn tại).
</details>

<details>
<summary>Lời giải Bài 10 — Chia lịch trực tối thiểu hoá chênh lệch</summary>

**Bước 1 (kiểm tra bất đẳng thức tứ giác):** dùng đúng phương pháp thực nghiệm ở Ví dụ 1, mục 10.3, với hàm `chiPhi(j,i) = abs((i-j) - n/k)`.
**Bước 2:** nếu thoả (thường đúng với hàm giá trị tuyệt đối tuyến tính theo độ dài đoạn), áp dụng trực tiếp khung `chiaDeTriTinhDP` của Code khung mẫu, mục 10.3, chỉ thay hàm chi phí.
</details>

<details>
<summary>Lời giải Bài 11 — CSES Houses biến thể</summary>

Đây là bài kết hợp 2 chương: dùng Binary Search on Answer (Chương 4) trên giá trị "khoảng cách tối đa cho phép", với hàm `kiemTraKhaThi(khoangCach)` chạy tham lam O(n+m) (không cần D&C Optimization vì bản chất bài toán tham lam đã đủ nhanh) — độ khó chính nằm ở việc nhận diện đúng kỹ thuật cần dùng là Binary Search chứ không phải DP thuần.
</details>

<details>
<summary>Lời giải Bài 12 — Đếm số có tổng chữ số là số nguyên tố</summary>

**Ý tưởng:** kết hợp khung Digit DP đếm theo tổng chữ số (giống bài toán chính đầu mục 10.4) với 1 bước tiền xử lý sàng nguyên tố (Chương 17) để nhanh chóng kiểm tra `tongChuSo` có phải số nguyên tố hay không ở bước cuối cùng (`viTri == độ dài`), thay vì kiểm tra `tongModK == 0`.
</details>

<details>
<summary>Lời giải Bài 13 — Frog Jump với chi phí bậc 2</summary>

**Khai triển:** `chiPhi(i,j) = (j-i-k)² = (j-i)² - 2k(j-i) + k²`. Đặt `dp[j] = min_i(dp[i] + (j-i-k)²)`, khai triển theo biến `j`: `dp[j] = j² - 2kj + k² + min_i(dp[i] + i² + 2ki - 2ij)`. Đây là dạng đường thẳng theo `j` với độ dốc `-2i` và tung độ gốc `dp[i]+i²+2ki` — áp dụng CHT y hệt Code khung mẫu, mục 10.2, chỉ khác công thức khai triển.
</details>

<details>
<summary>Lời giải Bài 14 — Tổng hợp nhiều điều kiện Digit DP</summary>

**Ý tưởng:** kết hợp cả 3 điều kiện trong 1 trạng thái duy nhất: `(viTri, tongModK=3, chuSoTruoc (để kiểm tra không có "44" liên tiếp), chuSoDauTien (lưu lại từ bước đầu), tight)`. Đây là bài rèn kỹ năng tổng hợp — cấu trúc code giống hệt việc "chồng" các điều kiện riêng lẻ đã học ở Ví dụ 2 và Ví dụ 3, mục 10.4 vào cùng 1 hàm `dfs`, mở rộng tuple trạng thái để bao gồm đủ mọi thông tin cần theo dõi.

#### C++
```cpp
vector<int> cacChuSoTongHop;
map<tuple<int,int,int,int,int>, long long> ghiNhoTongHop; // (viTri, tongMod3, chuSoTruoc+1, chuSoDau+1, daBatDau)

long long dfsTongHop(int viTri, int tongMod3, int chuSoTruoc, int chuSoDau, bool daBatDau, bool dangBiChan) {
    if (viTri == (int)cacChuSoTongHop.size()) {
        if (daBatDau == false) return 0; // số 0, không tính
        if (tongMod3 != 0) return 0;
        int chuSoCuoi = chuSoTruoc; // chữ số cuối cùng chính là chuSoTruoc ở bước kết thúc
        if (chuSoCuoi == chuSoDau) return 0; // vi phạm "đầu khác cuối"
        return 1;
    }

    auto trangThai = make_tuple(viTri, tongMod3, chuSoTruoc + 1, chuSoDau + 1);
    if (dangBiChan == false && daBatDau == true && ghiNhoTongHop.count(trangThai) > 0) {
        return ghiNhoTongHop[trangThai];
    }

    int chuSoLonNhat = dangBiChan ? cacChuSoTongHop[viTri] : 9;
    long long tongKetQua = 0;

    for (int chuSo = 0; chuSo <= chuSoLonNhat; chuSo++) {
        bool laSoKhongDauChuaBatDau = (daBatDau == false) && (chuSo == 0);

        if (laSoKhongDauChuaBatDau == false && chuSoTruoc == 4 && chuSo == 4) {
            continue; // vi phạm "không có 2 chữ số 4 liên tiếp"
        }

        bool tightMoi = dangBiChan && (chuSo == chuSoLonNhat);
        bool daBatDauMoi = daBatDau || (chuSo != 0);
        int tongMod3Moi = laSoKhongDauChuaBatDau ? tongMod3 : (tongMod3 + chuSo) % 3;
        int chuSoTruocMoi = laSoKhongDauChuaBatDau ? -1 : chuSo;
        int chuSoDauMoi = laSoKhongDauChuaBatDau ? -1 : (daBatDau ? chuSoDau : chuSo);

        tongKetQua += dfsTongHop(viTri + 1, tongMod3Moi, chuSoTruocMoi, chuSoDauMoi, daBatDauMoi, tightMoi);
    }

    if (dangBiChan == false && daBatDau == true) {
        ghiNhoTongHop[trangThai] = tongKetQua;
    }
    return tongKetQua;
}
```

#### PYTHON
```python
cac_chu_so_tong_hop = []
ghi_nho_tong_hop = {}


def dfs_tong_hop(vi_tri, tong_mod_3, chu_so_truoc, chu_so_dau, da_bat_dau, dang_bi_chan):
    if vi_tri == len(cac_chu_so_tong_hop):
        if da_bat_dau == False:
            return 0
        if tong_mod_3 != 0:
            return 0
        if chu_so_truoc == chu_so_dau:
            return 0
        return 1

    trang_thai = (vi_tri, tong_mod_3, chu_so_truoc, chu_so_dau)
    if dang_bi_chan == False and da_bat_dau == True and trang_thai in ghi_nho_tong_hop:
        return ghi_nho_tong_hop[trang_thai]

    chu_so_lon_nhat = cac_chu_so_tong_hop[vi_tri] if dang_bi_chan else 9
    tong_ket_qua = 0

    for chu_so in range(0, chu_so_lon_nhat + 1):
        la_so_khong_dau_chua_bat_dau = (da_bat_dau == False) and (chu_so == 0)

        if la_so_khong_dau_chua_bat_dau == False and chu_so_truoc == 4 and chu_so == 4:
            continue

        tight_moi = dang_bi_chan and (chu_so == chu_so_lon_nhat)
        da_bat_dau_moi = da_bat_dau or (chu_so != 0)
        tong_mod_3_moi = tong_mod_3 if la_so_khong_dau_chua_bat_dau else (tong_mod_3 + chu_so) % 3
        chu_so_truoc_moi = -1 if la_so_khong_dau_chua_bat_dau else chu_so
        chu_so_dau_moi = -1 if la_so_khong_dau_chua_bat_dau else (chu_so_dau if da_bat_dau else chu_so)

        tong_ket_qua += dfs_tong_hop(vi_tri + 1, tong_mod_3_moi, chu_so_truoc_moi, chu_so_dau_moi, da_bat_dau_moi, tight_moi)

    if dang_bi_chan == False and da_bat_dau == True:
        ghi_nho_tong_hop[trang_thai] = tong_ket_qua
    return tong_ket_qua
```
</details>
