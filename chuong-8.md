# Chương 8: Quy hoạch động cơ bản

---

## 8.1. Tư duy 4 bước xây dựng DP

**Nêu bài toán:** Một người đi cầu thang có n bậc, mỗi bước được phép bước lên 1 bậc hoặc 2 bậc. Hỏi có bao nhiêu cách khác nhau để đi hết n bậc cầu thang?

**Phân tích vấn đề:** Gọi `soCach(n)` là số cách đi hết n bậc. Bước cuối cùng của bất kỳ cách đi nào cũng chỉ có thể là bước 1 bậc (từ bậc n-1 lên bậc n) hoặc bước 2 bậc (từ bậc n-2 lên bậc n). Vậy `soCach(n) = soCach(n-1) + soCach(n-2)` — đây chính là công thức Fibonacci đã gặp ở Chương 7, và ta đã biết đệ quy thuần tuý cho công thức này sẽ tính lại rất nhiều lần cùng một bài toán con.

**Giải pháp đơn thuần:** Viết đệ quy trực tiếp theo công thức trên, không lưu lại kết quả trung gian nào. Độ phức tạp là O(2^n) — quá chậm với n lớn hơn khoảng 40.

**Khó khăn với giải pháp đơn thuần:** Cây đệ quy có rất nhiều nhánh trùng lặp — ví dụ `soCach(5)` gọi `soCach(3)` cả trong nhánh `soCach(4)` lẫn trực tiếp. Ta đang lãng phí thời gian tính đi tính lại cùng một giá trị nhiều lần.

**Cách tiếp cận mới — Quy hoạch động (Dynamic Programming):** Lưu lại kết quả của mỗi bài toán con **ngay sau khi tính lần đầu tiên**, để những lần sau cần tới giá trị đó, ta chỉ cần tra bảng thay vì tính lại. Đây chính là ý tưởng cốt lõi của Quy hoạch động — mỗi bài toán con chỉ cần giải đúng 1 lần.

### Quy trình 4 bước xây dựng lời giải DP

1. **Định nghĩa trạng thái:** xác định rõ `dp[i]` (hoặc `dp[i][j]`) đại diện cho điều gì. Đây là bước quan trọng nhất — nếu định nghĩa sai hoặc mơ hồ, các bước sau sẽ không thể thực hiện đúng.
2. **Tìm công thức chuyển trạng thái (recurrence):** biểu diễn `dp[i]` dựa trên các giá trị `dp` đã biết trước đó.
3. **Xác định giá trị khởi tạo (base case):** những trạng thái nhỏ nhất mà ta có thể biết trực tiếp, không cần tính qua công thức.
4. **Xác định thứ tự tính:** đảm bảo khi tính `dp[i]`, mọi giá trị `dp` mà công thức phụ thuộc vào đã được tính trước đó rồi (tính từ nhỏ đến lớn — gọi là **bottom-up**), hoặc dùng đệ quy có lưu kết quả (gọi là **top-down** / **memoization**).

### Minh hoạ lời giải chi tiết

Với bài toán cầu thang, n = 5:

**Bước 1 — Định nghĩa trạng thái:** `dp[i]` = số cách đi hết i bậc cầu thang.

**Bước 2 — Công thức chuyển trạng thái:** `dp[i] = dp[i-1] + dp[i-2]`.

**Bước 3 — Giá trị khởi tạo:** `dp[0] = 1` (đứng yên tại chân cầu thang, có đúng 1 cách "không đi bước nào"), `dp[1] = 1` (chỉ có 1 cách: bước 1 bậc).

**Bước 4 — Thứ tự tính:** tính từ `dp[2]` tăng dần tới `dp[n]`, vì `dp[i]` chỉ phụ thuộc vào các giá trị nhỏ hơn nó.

| i | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| dp[i] | 1 | 1 | 2 | 3 | 5 | 8 |

`dp[2] = dp[1] + dp[0] = 1 + 1 = 2`. `dp[3] = dp[2] + dp[1] = 2 + 1 = 3`. Và cứ thế tính tiếp. Kết quả cuối: `dp[5] = 8`.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long demSoCachLeoCauThang(int n) {
    vector<long long> dp(n + 1, 0);

    dp[0] = 1; // giá trị khởi tạo
    if (n >= 1) {
        dp[1] = 1; // giá trị khởi tạo
    }

    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2]; // công thức chuyển trạng thái
    }

    return dp[n];
}

int main() {
    int n = 5;
    cout << demSoCachLeoCauThang(n) << endl;
    return 0;
}
```

#### PYTHON
```python
def dem_so_cach_leo_cau_thang(n):
    dp = [0] * (n + 1)

    dp[0] = 1
    if n >= 1:
        dp[1] = 1

    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]


n = 5
print(dem_so_cach_leo_cau_thang(n))
```

### Ví dụ 1 (Dễ) — Cài đặt kiểu Top-down (Memoization) cho cùng bài toán cầu thang

**Phân tích:** Cách tiếp cận Bottom-up ở trên tính từ trạng thái nhỏ lên trạng thái lớn bằng vòng lặp. Cách tiếp cận Top-down giữ nguyên cấu trúc đệ quy tự nhiên (giống Chương 7), nhưng **thêm một mảng ghi nhớ** để tránh tính lại các giá trị đã biết.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<long long> ghiNho;

long long demSoCachTopDown(int n) {
    if (n == 0 || n == 1) {
        return 1; // điều kiện dừng
    }

    if (ghiNho[n] != -1) {
        return ghiNho[n]; // đã tính trước đó -> trả về ngay, không tính lại
    }

    ghiNho[n] = demSoCachTopDown(n - 1) + demSoCachTopDown(n - 2);
    return ghiNho[n];
}

int main() {
    int n = 5;
    ghiNho.assign(n + 1, -1); // -1 nghĩa là "chưa tính"

    cout << demSoCachTopDown(n) << endl;
    return 0;
}
```

#### PYTHON
```python
ghi_nho = {}


def dem_so_cach_top_down(n):
    if n == 0 or n == 1:
        return 1

    if n in ghi_nho:
        return ghi_nho[n]

    ghi_nho[n] = dem_so_cach_top_down(n - 1) + dem_so_cach_top_down(n - 2)
    return ghi_nho[n]


n = 5
print(dem_so_cach_top_down(n))
```

> **So sánh Bottom-up và Top-down:** Bottom-up thường nhanh hơn một chút (không tốn chi phí gọi hàm đệ quy) và dễ tối ưu bộ nhớ hơn, nhưng Top-down giữ được cấu trúc tư duy tự nhiên, dễ viết hơn khi trạng thái phức tạp hoặc không phải mọi trạng thái đều cần tính tới.

### Ví dụ 2 (Trung bình) — Số cách đổi tiền (Coin Change - đếm số cách)

Bài toán: cho các loại tiền xu với mệnh giá cho trước (mỗi loại có thể dùng lại nhiều lần), và một số tiền S, hãy đếm số cách chọn xu (không phân biệt thứ tự chọn) để tổng đúng bằng S.

**Phân tích theo 4 bước:**
1. **Trạng thái:** `dp[i]` = số cách chọn xu (từ các loại đã xét tới thời điểm hiện tại) để tổng đúng bằng `i`.
2. **Công thức:** với mỗi loại xu `c`, ta cập nhật `dp[i] = dp[i] + dp[i - c]` với mọi `i >= c` (duyệt xu ở vòng ngoài, số tiền ở vòng trong, để đảm bảo không đếm trùng hoán vị của cùng 1 tổ hợp xu).
3. **Khởi tạo:** `dp[0] = 1` (có đúng 1 cách để tạo tổng 0: không chọn xu nào).
4. **Thứ tự:** duyệt từng loại xu, với mỗi loại xu duyệt `i` từ `c` tăng dần tới `S`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long demSoCachDoiTien(vector<int> menhGia, int S) {
    vector<long long> dp(S + 1, 0);
    dp[0] = 1;

    for (int i = 0; i < (int)menhGia.size(); i++) {
        int xuHienTai = menhGia[i];
        for (int soTien = xuHienTai; soTien <= S; soTien++) {
            dp[soTien] = dp[soTien] + dp[soTien - xuHienTai];
        }
    }

    return dp[S];
}

int main() {
    vector<int> menhGia = {1, 2, 5};
    int S = 5;

    cout << demSoCachDoiTien(menhGia, S) << endl;

    return 0;
}
```

#### PYTHON
```python
def dem_so_cach_doi_tien(menh_gia, S):
    dp = [0] * (S + 1)
    dp[0] = 1

    for xu_hien_tai in menh_gia:
        for so_tien in range(xu_hien_tai, S + 1):
            dp[so_tien] = dp[so_tien] + dp[so_tien - xu_hien_tai]

    return dp[S]


menh_gia = [1, 2, 5]
S = 5

print(dem_so_cach_doi_tien(menh_gia, S))
```

> **Lưu ý quan trọng về thứ tự vòng lặp:** nếu ta đổi thứ tự (duyệt số tiền ở vòng ngoài, loại xu ở vòng trong), bài toán sẽ trở thành **đếm số dãy có thứ tự** (ví dụ chọn xu 1 rồi xu 2 được tính khác với chọn xu 2 rồi xu 1) thay vì đếm tổ hợp không phân biệt thứ tự — đây là một bài toán khác, sẽ được luyện tập ở bài tập cuối chương.

### Ví dụ 3 (Khó) — Số tiền tối thiểu để đổi tiền (Coin Change - giá trị nhỏ nhất)

Bài toán: cho các loại tiền xu với mệnh giá cho trước (số lượng không giới hạn), và số tiền S, hãy tìm **số lượng xu ít nhất** để tổng đúng bằng S (nếu không thể, trả về -1).

**Phân tích theo 4 bước:**
1. **Trạng thái:** `dp[i]` = số lượng xu ít nhất để tạo thành tổng `i`.
2. **Công thức:** `dp[i] = min(dp[i - c] + 1)` với mọi mệnh giá `c` sao cho `c <= i`.
3. **Khởi tạo:** `dp[0] = 0` (không cần xu nào để tạo tổng 0); các giá trị khác khởi tạo bằng "vô cực" (một số rất lớn, đại diện cho "chưa tìm được cách").
4. **Thứ tự:** tính `dp[i]` tăng dần từ 1 đến S, với mỗi `i` thử tất cả các loại xu.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timSoXuItNhat(vector<int> menhGia, int S) {
    const int VO_CUC = INT_MAX / 2;
    vector<int> dp(S + 1, VO_CUC);
    dp[0] = 0;

    for (int soTien = 1; soTien <= S; soTien++) {
        for (int i = 0; i < (int)menhGia.size(); i++) {
            int xuHienTai = menhGia[i];
            if (xuHienTai <= soTien) {
                int giaTriUngCu = dp[soTien - xuHienTai] + 1;
                if (giaTriUngCu < dp[soTien]) {
                    dp[soTien] = giaTriUngCu;
                }
            }
        }
    }

    if (dp[S] >= VO_CUC) {
        return -1;
    }
    return dp[S];
}

int main() {
    vector<int> menhGia = {1, 3, 4};
    int S = 6;

    cout << timSoXuItNhat(menhGia, S) << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_so_xu_it_nhat(menh_gia, S):
    VO_CUC = float('inf')
    dp = [VO_CUC] * (S + 1)
    dp[0] = 0

    for so_tien in range(1, S + 1):
        for xu_hien_tai in menh_gia:
            if xu_hien_tai <= so_tien:
                gia_tri_ung_cu = dp[so_tien - xu_hien_tai] + 1
                if gia_tri_ung_cu < dp[so_tien]:
                    dp[so_tien] = gia_tri_ung_cu

    if dp[S] == VO_CUC:
        return -1
    return dp[S]


menh_gia = [1, 3, 4]
S = 6

print(tim_so_xu_it_nhat(menh_gia, S))
```

**Bài tập minh hoạ:** Cho n, hãy đếm số cách để biểu diễn n thành tổng của các số nguyên dương (không phân biệt thứ tự các số hạng), trong đó mỗi số hạng chỉ được là 1, 3, hoặc 4.

**Lời giải:** Áp dụng đúng cấu trúc Ví dụ 2 (đếm số cách), với mệnh giá là `{1, 3, 4}` và S = n.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long demSoCachBieuDien(int n) {
    vector<int> soHang = {1, 3, 4};
    vector<long long> dp(n + 1, 0);
    dp[0] = 1;

    for (int i = 0; i < (int)soHang.size(); i++) {
        int soHangHienTai = soHang[i];
        for (int tong = soHangHienTai; tong <= n; tong++) {
            dp[tong] = dp[tong] + dp[tong - soHangHienTai];
        }
    }

    return dp[n];
}

int main() {
    cout << demSoCachBieuDien(7) << endl;
    return 0;
}
```

#### PYTHON
```python
def dem_so_cach_bieu_dien(n):
    so_hang = [1, 3, 4]
    dp = [0] * (n + 1)
    dp[0] = 1

    for so_hang_hien_tai in so_hang:
        for tong in range(so_hang_hien_tai, n + 1):
            dp[tong] = dp[tong] + dp[tong - so_hang_hien_tai]

    return dp[n]


print(dem_so_cach_bieu_dien(7))
```

---

## 8.2. Quy hoạch động 1 chiều — LIS và mở rộng

**Nêu bài toán:** Cho một dãy số nguyên, hãy tìm độ dài của **dãy con tăng dài nhất** (Longest Increasing Subsequence — LIS). Lưu ý: dãy con không cần phải liên tiếp trong mảng gốc, chỉ cần giữ đúng thứ tự xuất hiện.

**Phân tích vấn đề:** Nếu thử tất cả các dãy con có thể có, số lượng dãy con là O(2^n) — không khả thi. Nhưng ta để ý: nếu biết độ dài dãy con tăng dài nhất **kết thúc tại từng vị trí**, ta có thể xây dựng lời giải cho vị trí sau dựa trên các vị trí trước.

**Giải pháp đơn thuần (Backtracking từ Chương 7):** Với mỗi phần tử, thử "chọn" hoặc "không chọn" vào dãy con, kiểm tra tính tăng dần. Độ phức tạp O(2^n).

**Khó khăn với giải pháp đơn thuần:** Với n lớn (ví dụ 1000 trở lên), O(2^n) hoàn toàn không khả thi.

**Cách tiếp cận mới:** Định nghĩa `dp[i]` = độ dài dãy con tăng dài nhất mà **bắt buộc kết thúc tại vị trí i**. Với mỗi `i`, ta xét tất cả các vị trí `j < i` mà `a[j] < a[i]`, và `dp[i] = max(dp[j]) + 1`. Độ phức tạp O(n²) — cải thiện đáng kể so với O(2^n).

### Minh hoạ lời giải chi tiết

Cho `a = [10, 9, 2, 5, 3, 7, 101, 18]`:

| i | a[i] | Các j < i với a[j] < a[i] | dp[i] |
|---|---|---|---|
| 0 | 10 | (không có) | 1 |
| 1 | 9 | (không có) | 1 |
| 2 | 2 | (không có) | 1 |
| 3 | 5 | j=2 (a[2]=2) | dp[2]+1 = 2 |
| 4 | 3 | j=2 (a[2]=2) | dp[2]+1 = 2 |
| 5 | 7 | j=2,3,4 (2,5,3) | max(dp[2],dp[3],dp[4])+1 = 3 |
| 6 | 101 | j=0,1,2,3,4,5 (tất cả nhỏ hơn 101) | max(...)+1 = 4 |
| 7 | 18 | j=2,3,4,5 (2,5,3,7 đều < 18) | max(dp[2],dp[3],dp[4],dp[5])+1 = 4 |

Kết quả: `max(dp) = 4` (ứng với dãy con `[2, 5, 7, 101]` hoặc `[2, 3, 7, 18]`, đều có độ dài 4).

### Code khung mẫu (O(n²))

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timDoDaiLIS(vector<int> a) {
    int n = (int)a.size();
    vector<int> dp(n, 1); // mỗi phần tử tự nó tạo thành dãy con độ dài 1

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (a[j] < a[i]) {
                if (dp[j] + 1 > dp[i]) {
                    dp[i] = dp[j] + 1;
                }
            }
        }
    }

    int ketQua = 0;
    for (int i = 0; i < n; i++) {
        if (dp[i] > ketQua) {
            ketQua = dp[i];
        }
    }

    return ketQua;
}

int main() {
    vector<int> a = {10, 9, 2, 5, 3, 7, 101, 18};
    cout << timDoDaiLIS(a) << endl;
    return 0;
}
```

#### PYTHON
```python
def tim_do_dai_lis(a):
    n = len(a)
    dp = [1] * n

    for i in range(n):
        for j in range(i):
            if a[j] < a[i]:
                if dp[j] + 1 > dp[i]:
                    dp[i] = dp[j] + 1

    ket_qua = 0
    for i in range(n):
        if dp[i] > ket_qua:
            ket_qua = dp[i]

    return ket_qua


a = [10, 9, 2, 5, 3, 7, 101, 18]
print(tim_do_dai_lis(a))
```

### Ví dụ 1 (Dễ) — Số tiền lớn nhất khi trộm nhà (House Robber)

Bài toán: có n ngôi nhà xếp thành hàng, nhà thứ i có số tiền `a[i]`. Kẻ trộm không được trộm 2 nhà liền kề nhau (vì sẽ bị phát hiện). Hãy tìm tổng số tiền lớn nhất có thể trộm được.

**Phân tích theo 4 bước:**
1. **Trạng thái:** `dp[i]` = số tiền lớn nhất trộm được khi chỉ xét i nhà đầu tiên.
2. **Công thức:** tại nhà thứ i, có 2 lựa chọn — không trộm nhà này (`dp[i] = dp[i-1]`), hoặc trộm nhà này (`dp[i] = dp[i-2] + a[i]`, vì không được trộm nhà liền trước). Lấy giá trị lớn hơn.
3. **Khởi tạo:** `dp[0] = a[0]`, `dp[1] = max(a[0], a[1])`.
4. **Thứ tự:** tính tăng dần từ `dp[2]` tới `dp[n-1]`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timSoTienTromLonNhat(vector<int> a) {
    int n = (int)a.size();
    if (n == 0) {
        return 0;
    }
    if (n == 1) {
        return a[0];
    }

    vector<long long> dp(n, 0);
    dp[0] = a[0];
    dp[1] = max(a[0], a[1]);

    for (int i = 2; i < n; i++) {
        long long khongTromNhaNay = dp[i - 1];
        long long tromNhaNay = dp[i - 2] + a[i];
        dp[i] = max(khongTromNhaNay, tromNhaNay);
    }

    return dp[n - 1];
}

int main() {
    vector<int> a = {2, 7, 9, 3, 1};
    cout << timSoTienTromLonNhat(a) << endl;
    return 0;
}
```

#### PYTHON
```python
def tim_so_tien_trom_lon_nhat(a):
    n = len(a)
    if n == 0:
        return 0
    if n == 1:
        return a[0]

    dp = [0] * n
    dp[0] = a[0]
    dp[1] = max(a[0], a[1])

    for i in range(2, n):
        khong_trom_nha_nay = dp[i - 1]
        trom_nha_nay = dp[i - 2] + a[i]
        dp[i] = max(khong_trom_nha_nay, trom_nha_nay)

    return dp[n - 1]


a = [2, 7, 9, 3, 1]
print(tim_so_tien_trom_lon_nhat(a))
```

### Ví dụ 2 (Trung bình) — LIS với độ phức tạp O(n log n)

**Phân tích:** Cách O(n²) ở phần "Code khung mẫu" đủ nhanh cho n tới khoảng 5000-10000, nhưng với n lên tới 10^5 hoặc lớn hơn, ta cần cách tối ưu hơn. Ý tưởng: duy trì một mảng phụ `tail`, trong đó `tail[k]` là giá trị **nhỏ nhất có thể** để kết thúc một dãy con tăng có độ dài `k+1`. Mảng `tail` này luôn được giữ ở trạng thái tăng dần, cho phép dùng tìm kiếm nhị phân (đã học ở Chương 4) để cập nhật trong O(log n) mỗi bước.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timDoDaiLISNhanh(vector<int> a) {
    vector<int> tail; // tail[k] = giá trị nhỏ nhất để kết thúc dãy con tăng độ dài k+1

    for (int i = 0; i < (int)a.size(); i++) {
        // Tìm vị trí đầu tiên trong tail có giá trị >= a[i]
        auto viTri = lower_bound(tail.begin(), tail.end(), a[i]);

        if (viTri == tail.end()) {
            // Không tìm thấy -> a[i] lớn hơn mọi phần tử trong tail -> nối dài thêm dãy
            tail.push_back(a[i]);
        } else {
            // Tìm thấy -> thay thế vị trí đó bằng a[i] (giá trị nhỏ hơn, giúp mở rộng khả năng sau này)
            *viTri = a[i];
        }
    }

    return (int)tail.size();
}

int main() {
    vector<int> a = {10, 9, 2, 5, 3, 7, 101, 18};
    cout << timDoDaiLISNhanh(a) << endl;
    return 0;
}
```

#### PYTHON
```python
import bisect


def tim_do_dai_lis_nhanh(a):
    tail = []

    for x in a:
        vi_tri = bisect.bisect_left(tail, x)

        if vi_tri == len(tail):
            tail.append(x)
        else:
            tail[vi_tri] = x

    return len(tail)


a = [10, 9, 2, 5, 3, 7, 101, 18]
print(tim_do_dai_lis_nhanh(a))
```

> **Lưu ý quan trọng:** mảng `tail` **không phải** là dãy con tăng dài nhất thực sự — nó chỉ giữ đúng **độ dài** của LIS và các giá trị "tốt nhất có thể" tại từng độ dài. Nếu cần khôi phục lại dãy con cụ thể, cần thêm bước lưu vết (truy vết) riêng.

### Ví dụ 3 (Khó) — Số lượng dãy con tăng dài nhất (Number of Longest Increasing Subsequence)

Bài toán: cho một dãy số, hãy đếm xem có **bao nhiêu** dãy con tăng có độ dài bằng đúng độ dài LIS.

**Phân tích:** Ngoài mảng `dp[i]` (độ dài LIS kết thúc tại i) đã có ở phần đầu mục này, ta cần thêm một mảng `demSoCach[i]` = số lượng dãy con tăng có độ dài `dp[i]` và kết thúc tại vị trí i. Khi cập nhật `dp[i]` từ `dp[j]`, nếu tìm được độ dài **tốt hơn** trước đó, ta reset `demSoCach[i] = demSoCach[j]`; nếu tìm được độ dài **bằng** với giá trị tốt nhất hiện tại, ta cộng dồn `demSoCach[i] += demSoCach[j]`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int demSoLuongLIS(vector<int> a) {
    int n = (int)a.size();
    vector<int> dp(n, 1);
    vector<int> demSoCach(n, 1);

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (a[j] < a[i]) {
                if (dp[j] + 1 > dp[i]) {
                    // Tìm được cách tạo dãy con DÀI HƠN kết thúc tại i -> cập nhật lại từ đầu
                    dp[i] = dp[j] + 1;
                    demSoCach[i] = demSoCach[j];
                } else if (dp[j] + 1 == dp[i]) {
                    // Tìm được thêm 1 cách khác cũng đạt độ dài tốt nhất hiện tại -> cộng dồn
                    demSoCach[i] = demSoCach[i] + demSoCach[j];
                }
            }
        }
    }

    int doDaiLISLonNhat = 0;
    for (int i = 0; i < n; i++) {
        if (dp[i] > doDaiLISLonNhat) {
            doDaiLISLonNhat = dp[i];
        }
    }

    int tongSoLuong = 0;
    for (int i = 0; i < n; i++) {
        if (dp[i] == doDaiLISLonNhat) {
            tongSoLuong = tongSoLuong + demSoCach[i];
        }
    }

    return tongSoLuong;
}

int main() {
    vector<int> a = {1, 3, 5, 4, 7};
    cout << demSoLuongLIS(a) << endl;
    return 0;
}
```

#### PYTHON
```python
def dem_so_luong_lis(a):
    n = len(a)
    dp = [1] * n
    dem_so_cach = [1] * n

    for i in range(n):
        for j in range(i):
            if a[j] < a[i]:
                if dp[j] + 1 > dp[i]:
                    dp[i] = dp[j] + 1
                    dem_so_cach[i] = dem_so_cach[j]
                elif dp[j] + 1 == dp[i]:
                    dem_so_cach[i] = dem_so_cach[i] + dem_so_cach[j]

    do_dai_lis_lon_nhat = 0
    for i in range(n):
        if dp[i] > do_dai_lis_lon_nhat:
            do_dai_lis_lon_nhat = dp[i]

    tong_so_luong = 0
    for i in range(n):
        if dp[i] == do_dai_lis_lon_nhat:
            tong_so_luong = tong_so_luong + dem_so_cach[i]

    return tong_so_luong


a = [1, 3, 5, 4, 7]
print(dem_so_luong_lis(a))
```

**Bài tập minh hoạ:** Cho dãy số, tìm độ dài của "dãy con giảm dài nhất" (Longest Decreasing Subsequence).

**Lời giải:** Chỉ cần đảo ngược điều kiện so sánh trong công thức LIS gốc — từ `a[j] < a[i]` thành `a[j] > a[i]`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timDoDaiDayGiamDaiNhat(vector<int> a) {
    int n = (int)a.size();
    vector<int> dp(n, 1);

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (a[j] > a[i]) {
                if (dp[j] + 1 > dp[i]) {
                    dp[i] = dp[j] + 1;
                }
            }
        }
    }

    int ketQua = 0;
    for (int i = 0; i < n; i++) {
        if (dp[i] > ketQua) {
            ketQua = dp[i];
        }
    }

    return ketQua;
}

int main() {
    vector<int> a = {9, 4, 7, 2, 10, 1};
    cout << timDoDaiDayGiamDaiNhat(a) << endl;
    return 0;
}
```

#### PYTHON
```python
def tim_do_dai_day_giam_dai_nhat(a):
    n = len(a)
    dp = [1] * n

    for i in range(n):
        for j in range(i):
            if a[j] > a[i]:
                if dp[j] + 1 > dp[i]:
                    dp[i] = dp[j] + 1

    ket_qua = 0
    for i in range(n):
        if dp[i] > ket_qua:
            ket_qua = dp[i]

    return ket_qua


a = [9, 4, 7, 2, 10, 1]
print(tim_do_dai_day_giam_dai_nhat(a))
```

---

## 8.3. Quy hoạch động 2 chiều — Knapsack, LCS, Edit Distance

**Nêu bài toán (Knapsack 0/1):** Cho n vật, vật thứ i có trọng lượng `w[i]` và giá trị `v[i]`, và một cái túi có sức chứa W. Mỗi vật chỉ được chọn **tối đa 1 lần** (0 hoặc 1, không được chọn phần lẻ). Hãy tìm tổng giá trị lớn nhất có thể đạt được mà không vượt quá sức chứa W.

**Phân tích vấn đề:** Bài toán này có 2 "chiều" thông tin cần theo dõi đồng thời: đã xét tới vật thứ mấy, và trọng lượng đã dùng là bao nhiêu — đây là dấu hiệu cho thấy cần một bảng DP **2 chiều**.

**Giải pháp đơn thuần (Backtracking):** Với mỗi vật, thử "chọn" hoặc "không chọn", kiểm tra tổng trọng lượng ở cuối. Độ phức tạp O(2^n).

**Khó khăn với giải pháp đơn thuần:** Với n lớn, O(2^n) không khả thi, dù W có thể chỉ vài nghìn.

**Cách tiếp cận mới:** Định nghĩa `dp[i][w]` = giá trị lớn nhất đạt được khi chỉ xét i vật đầu tiên, với sức chứa còn lại tối đa là `w`. Với mỗi vật, ta có 2 lựa chọn: không lấy (`dp[i][w] = dp[i-1][w]`), hoặc lấy nếu đủ chỗ (`dp[i][w] = dp[i-1][w - w[i]] + v[i]`), rồi lấy giá trị lớn hơn.

### Minh hoạ lời giải chi tiết

Cho 3 vật: (trọng lượng, giá trị) = (1,6), (2,10), (3,12), sức chứa W = 5.

Bảng `dp[i][w]` (hàng là số vật đã xét, cột là sức chứa còn lại):

| i \ w | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| 0 (chưa xét vật nào) | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 (đã xét vật 1: w=1,v=6) | 0 | 6 | 6 | 6 | 6 | 6 |
| 2 (đã xét vật 2: w=2,v=10) | 0 | 6 | 10 | 16 | 16 | 16 |
| 3 (đã xét vật 3: w=3,v=12) | 0 | 6 | 10 | 16 | 18 | 22 |

Ví dụ tính `dp[3][5]`: không lấy vật 3 -> `dp[2][5] = 16`. Lấy vật 3 (trọng lượng 3, giá trị 12) -> `dp[2][5-3] + 12 = dp[2][2] + 12 = 10 + 12 = 22`. Lấy giá trị lớn hơn: `dp[3][5] = 22`.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long giaiCaiTui01(vector<int> trongLuong, vector<int> giaTri, int W) {
    int n = (int)trongLuong.size();
    vector<vector<long long>> dp(n + 1, vector<long long>(W + 1, 0));

    for (int i = 1; i <= n; i++) {
        for (int w = 0; w <= W; w++) {
            // Lựa chọn 1: không lấy vật thứ i (chỉ số mảng trongLuong/giaTri là i-1)
            dp[i][w] = dp[i - 1][w];

            // Lựa chọn 2: lấy vật thứ i, nếu đủ chỗ
            if (trongLuong[i - 1] <= w) {
                long long giaTriNeuLay = dp[i - 1][w - trongLuong[i - 1]] + giaTri[i - 1];
                if (giaTriNeuLay > dp[i][w]) {
                    dp[i][w] = giaTriNeuLay;
                }
            }
        }
    }

    return dp[n][W];
}

int main() {
    vector<int> trongLuong = {1, 2, 3};
    vector<int> giaTri = {6, 10, 12};
    int W = 5;

    cout << giaiCaiTui01(trongLuong, giaTri, W) << endl;

    return 0;
}
```

#### PYTHON
```python
def giai_cai_tui_01(trong_luong, gia_tri, W):
    n = len(trong_luong)
    dp = [[0] * (W + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        for w in range(W + 1):
            dp[i][w] = dp[i - 1][w]

            if trong_luong[i - 1] <= w:
                gia_tri_neu_lay = dp[i - 1][w - trong_luong[i - 1]] + gia_tri[i - 1]
                if gia_tri_neu_lay > dp[i][w]:
                    dp[i][w] = gia_tri_neu_lay

    return dp[n][W]


trong_luong = [1, 2, 3]
gia_tri = [6, 10, 12]
W = 5

print(giai_cai_tui_01(trong_luong, gia_tri, W))
```

### Ví dụ 1 (Dễ) — Dãy con chung dài nhất (Longest Common Subsequence — LCS)

Bài toán: cho 2 chuỗi ký tự `s1` và `s2`, tìm độ dài của dãy con chung dài nhất (dãy con — không cần liên tiếp — xuất hiện trong cả 2 chuỗi, giữ đúng thứ tự).

**Phân tích theo 4 bước:**
1. **Trạng thái:** `dp[i][j]` = độ dài LCS của `i` ký tự đầu của `s1` và `j` ký tự đầu của `s2`.
2. **Công thức:** nếu `s1[i-1] == s2[j-1]` (ký tự đang xét giống nhau), `dp[i][j] = dp[i-1][j-1] + 1`. Ngược lại, `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`.
3. **Khởi tạo:** `dp[0][j] = 0` và `dp[i][0] = 0` với mọi i, j (chuỗi rỗng thì LCS = 0).
4. **Thứ tự:** tính theo hàng hoặc cột tăng dần, vì mỗi ô chỉ phụ thuộc vào các ô có chỉ số nhỏ hơn hoặc bằng.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timDoDaiLCS(string s1, string s2) {
    int n = (int)s1.size();
    int m = (int)s2.size();
    vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (s1[i - 1] == s2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }

    return dp[n][m];
}

int main() {
    string s1 = "ABCBDAB";
    string s2 = "BDCABA";

    cout << timDoDaiLCS(s1, s2) << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_do_dai_lcs(s1, s2):
    n = len(s1)
    m = len(s2)
    dp = [[0] * (m + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i - 1] == s2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

    return dp[n][m]


s1 = "ABCBDAB"
s2 = "BDCABA"

print(tim_do_dai_lcs(s1, s2))
```

### Ví dụ 2 (Trung bình) — Khoảng cách chỉnh sửa (Edit Distance)

Bài toán: cho 2 chuỗi `s1` và `s2`, tìm số thao tác chỉnh sửa tối thiểu (thêm 1 ký tự, xoá 1 ký tự, hoặc thay 1 ký tự) để biến `s1` thành `s2`.

**Phân tích theo 4 bước:**
1. **Trạng thái:** `dp[i][j]` = số thao tác tối thiểu để biến `i` ký tự đầu của `s1` thành `j` ký tự đầu của `s2`.
2. **Công thức:** nếu `s1[i-1] == s2[j-1]`, không cần thao tác gì thêm cho ký tự này: `dp[i][j] = dp[i-1][j-1]`. Nếu khác nhau, thử cả 3 loại thao tác và lấy giá trị nhỏ nhất: xoá (`dp[i-1][j] + 1`), thêm (`dp[i][j-1] + 1`), thay (`dp[i-1][j-1] + 1`).
3. **Khởi tạo:** `dp[i][0] = i` (xoá hết i ký tự để thành chuỗi rỗng), `dp[0][j] = j` (thêm đủ j ký tự từ chuỗi rỗng).
4. **Thứ tự:** tính theo hàng hoặc cột tăng dần.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timKhoangCachChinhSua(string s1, string s2) {
    int n = (int)s1.size();
    int m = (int)s2.size();
    vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));

    for (int i = 0; i <= n; i++) {
        dp[i][0] = i;
    }
    for (int j = 0; j <= m; j++) {
        dp[0][j] = j;
    }

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (s1[i - 1] == s2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                int xoa = dp[i - 1][j] + 1;
                int them = dp[i][j - 1] + 1;
                int thay = dp[i - 1][j - 1] + 1;

                dp[i][j] = min({xoa, them, thay});
            }
        }
    }

    return dp[n][m];
}

int main() {
    string s1 = "kitten";
    string s2 = "sitting";

    cout << timKhoangCachChinhSua(s1, s2) << endl; // 3

    return 0;
}
```

#### PYTHON
```python
def tim_khoang_cach_chinh_sua(s1, s2):
    n = len(s1)
    m = len(s2)
    dp = [[0] * (m + 1) for _ in range(n + 1)]

    for i in range(n + 1):
        dp[i][0] = i
    for j in range(m + 1):
        dp[0][j] = j

    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i - 1] == s2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                xoa = dp[i - 1][j] + 1
                them = dp[i][j - 1] + 1
                thay = dp[i - 1][j - 1] + 1

                dp[i][j] = min(xoa, them, thay)

    return dp[n][m]


s1 = "kitten"
s2 = "sitting"

print(tim_khoang_cach_chinh_sua(s1, s2))  # 3
```

### Ví dụ 3 (Khó) — Cái túi không giới hạn số lần lấy (Unbounded Knapsack)

Bài toán: giống Knapsack 0/1, nhưng mỗi loại vật được phép lấy **không giới hạn số lần** (miễn còn đủ chỗ trong túi). Hãy tìm tổng giá trị lớn nhất có thể đạt được.

**Phân tích:** Điểm khác biệt duy nhất so với Knapsack 0/1 nằm ở công thức chuyển trạng thái: khi "lấy vật i", ta **vẫn ở trạng thái được phép lấy tiếp vật i** (vì không giới hạn số lần), nên công thức là `dp[i][w] = max(dp[i-1][w], dp[i][w - w[i]] + v[i])` — chú ý vế thứ 2 dùng `dp[i]` (chưa chuyển sang vật tiếp theo) thay vì `dp[i-1]` như Knapsack 0/1.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long giaiCaiTuiKhongGioiHan(vector<int> trongLuong, vector<int> giaTri, int W) {
    int n = (int)trongLuong.size();
    vector<vector<long long>> dp(n + 1, vector<long long>(W + 1, 0));

    for (int i = 1; i <= n; i++) {
        for (int w = 0; w <= W; w++) {
            dp[i][w] = dp[i - 1][w]; // không lấy vật loại i

            if (trongLuong[i - 1] <= w) {
                // Lấy vật loại i, VẪN dùng dp[i] (không phải dp[i-1]) vì được lấy lại
                long long giaTriNeuLay = dp[i][w - trongLuong[i - 1]] + giaTri[i - 1];
                if (giaTriNeuLay > dp[i][w]) {
                    dp[i][w] = giaTriNeuLay;
                }
            }
        }
    }

    return dp[n][W];
}

int main() {
    vector<int> trongLuong = {1, 3, 4};
    vector<int> giaTri = {15, 50, 60};
    int W = 8;

    cout << giaiCaiTuiKhongGioiHan(trongLuong, giaTri, W) << endl;

    return 0;
}
```

#### PYTHON
```python
def giai_cai_tui_khong_gioi_han(trong_luong, gia_tri, W):
    n = len(trong_luong)
    dp = [[0] * (W + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        for w in range(W + 1):
            dp[i][w] = dp[i - 1][w]

            if trong_luong[i - 1] <= w:
                gia_tri_neu_lay = dp[i][w - trong_luong[i - 1]] + gia_tri[i - 1]
                if gia_tri_neu_lay > dp[i][w]:
                    dp[i][w] = gia_tri_neu_lay

    return dp[n][W]


trong_luong = [1, 3, 4]
gia_tri = [15, 50, 60]
W = 8

print(giai_cai_tui_khong_gioi_han(trong_luong, gia_tri, W))
```

**Bài tập minh hoạ:** Cho 2 chuỗi, tìm độ dài **chuỗi con chung ngắn nhất** cần thêm vào (Shortest Common Supersequence — chỉ cần tính độ dài) để chứa cả 2 chuỗi ban đầu như là dãy con của nó.

**Lời giải:** Độ dài chuỗi bổ sung ngắn nhất chứa cả 2 chuỗi làm dãy con bằng `n + m - LCS(s1, s2)` — mỗi ký tự chung (thuộc LCS) chỉ cần xuất hiện đúng 1 lần, các ký tự riêng phải giữ nguyên.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timDoDaiChuoiBoSungNganNhat(string s1, string s2) {
    int doDaiLCS = timDoDaiLCS(s1, s2); // dùng lại hàm đã viết ở Ví dụ 1
    return (int)s1.size() + (int)s2.size() - doDaiLCS;
}

int main() {
    string s1 = "abac";
    string s2 = "cab";

    cout << timDoDaiChuoiBoSungNganNhat(s1, s2) << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_do_dai_chuoi_bo_sung_ngan_nhat(s1, s2):
    do_dai_lcs = tim_do_dai_lcs(s1, s2)  # dùng lại hàm đã viết ở Ví dụ 1
    return len(s1) + len(s2) - do_dai_lcs


s1 = "abac"
s2 = "cab"

print(tim_do_dai_chuoi_bo_sung_ngan_nhat(s1, s2))
```

---

## 8.4. Quy hoạch động trên khoảng (Interval DP)

**Nêu bài toán:** Cho n viên đá xếp thành hàng, viên thứ i có giá trị `a[i]`. Mỗi lượt, ta được phép **loại bỏ** một viên đá ở giữa 2 viên đá còn lại (không phải đầu hoặc cuối dãy hiện tại), và thu về điểm bằng **tích** giá trị của viên bị loại với 2 viên liền kề nó (2 viên liền kề, không phải viên bị loại). Sau khi loại bỏ, 2 viên liền kề sẽ trở thành liền kề nhau. Trò chơi kết thúc khi chỉ còn 2 viên đá (đầu và cuối cùng của dãy gốc, không thể loại bỏ). Hãy tìm tổng điểm lớn nhất có thể đạt được. (Đây chính là bài toán "Zuma"/"Burst Balloons" dạng cổ điển.)

**Phân tích vấn đề:** Đây là dạng bài toán mà quyết định (chọn viên nào để loại **cuối cùng** trong một đoạn) chia bài toán lớn thành **2 bài toán con trên 2 đoạn nhỏ hơn nằm 2 bên**. Đặc điểm quan trọng: trạng thái DP cần biểu diễn bằng một **khoảng [trái, phải]** thay vì một chỉ số đơn lẻ như các mục trước — đây chính là lý do gọi kỹ thuật này là "DP trên khoảng" (Interval DP).

**Giải pháp đơn thuần:** Backtracking thử tất cả các thứ tự loại bỏ có thể. Với n viên đá, số lượng thứ tự loại bỏ có thể lên tới O(n!) — hoàn toàn không khả thi.

**Khó khăn với giải pháp đơn thuần:** Không gian trạng thái quá lớn, và có rất nhiều bài toán con trùng lặp giữa các thứ tự loại bỏ khác nhau.

**Cách tiếp cận mới:** Định nghĩa `dp[trai][phai]` = giá trị tối ưu có thể đạt được cho đoạn từ vị trí `trai` đến `phai` (2 đầu **không bị loại**, chỉ các viên ở giữa mới bị loại dần). Ta thử tất cả các vị trí `k` (nằm giữa trái và phải) là viên **bị loại cuối cùng** trong đoạn này — khi đó điểm thu được là `dp[trai][k] + dp[k][phai] + a[trai]*a[k]*a[phai]` (2 đoạn con được giải độc lập, cộng thêm điểm khi loại viên k cuối cùng, lúc đó 2 viên liền kề nó chính là trái và phải).

### Minh hoạ lời giải chi tiết

Với DP trên khoảng, ta luôn tính theo **thứ tự độ dài khoảng tăng dần** — khoảng ngắn nhất trước, khoảng dài hơn tính sau (vì `dp[trai][phai]` phụ thuộc vào các khoảng con nằm hoàn toàn bên trong nó).

Với `a = [1, 5, 3, 2]` (đánh số từ 0):

- Độ dài khoảng 2 (chỉ có 2 phần tử liền kề, không có viên nào ở giữa để loại): `dp[0][1] = 0`, `dp[1][2] = 0`, `dp[2][3] = 0`.
- Độ dài khoảng 3: xét `dp[0][2]` (đoạn từ viên 0 đến viên 2, viên giữa duy nhất có thể loại là viên 1): `dp[0][2] = dp[0][1] + dp[1][2] + a[0]*a[1]*a[2] = 0 + 0 + 1*5*3 = 15`.
- Tương tự `dp[1][3] = dp[1][2] + dp[2][3] + a[1]*a[2]*a[3] = 0 + 0 + 5*3*2 = 30`.
- Độ dài khoảng 4: `dp[0][3]` — thử từng viên giữa (viên 1 hoặc viên 2) là viên bị loại cuối cùng:
  - Nếu chọn viên 1 loại cuối: `dp[0][1] + dp[1][3] + a[0]*a[1]*a[3] = 0 + 30 + 1*5*2 = 40`.
  - Nếu chọn viên 2 loại cuối: `dp[0][2] + dp[2][3] + a[0]*a[2]*a[3] = 15 + 0 + 1*3*2 = 21`.
  - Lấy giá trị lớn hơn: `dp[0][3] = 40`.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long giaiBaiToanLoaiDa(vector<int> a) {
    int n = (int)a.size();
    vector<vector<long long>> dp(n, vector<long long>(n, 0));

    // Duyệt theo ĐỘ DÀI KHOẢNG tăng dần
    for (int doDaiKhoang = 3; doDaiKhoang <= n; doDaiKhoang++) {
        for (int trai = 0; trai + doDaiKhoang - 1 < n; trai++) {
            int phai = trai + doDaiKhoang - 1;
            dp[trai][phai] = 0; // giá trị nhỏ nhất có thể (sẽ được cập nhật lớn dần)

            for (int k = trai + 1; k < phai; k++) {
                long long giaTriUngCu = dp[trai][k] + dp[k][phai]
                                       + (long long)a[trai] * a[k] * a[phai];
                if (giaTriUngCu > dp[trai][phai]) {
                    dp[trai][phai] = giaTriUngCu;
                }
            }
        }
    }

    return dp[0][n - 1];
}

int main() {
    vector<int> a = {1, 5, 3, 2};
    cout << giaiBaiToanLoaiDa(a) << endl;
    return 0;
}
```

#### PYTHON
```python
def giai_bai_toan_loai_da(a):
    n = len(a)
    dp = [[0] * n for _ in range(n)]

    for do_dai_khoang in range(3, n + 1):
        for trai in range(0, n - do_dai_khoang + 1):
            phai = trai + do_dai_khoang - 1
            dp[trai][phai] = 0

            for k in range(trai + 1, phai):
                gia_tri_ung_cu = dp[trai][k] + dp[k][phai] + a[trai] * a[k] * a[phai]
                if gia_tri_ung_cu > dp[trai][phai]:
                    dp[trai][phai] = gia_tri_ung_cu

    return dp[0][n - 1]


a = [1, 5, 3, 2]
print(giai_bai_toan_loai_da(a))
```

### Ví dụ 1 (Dễ) — Kiểm tra chuỗi đối xứng bằng DP trên khoảng

Bài toán: cho một chuỗi, xây dựng bảng để kiểm tra **mọi đoạn con** của chuỗi có phải là chuỗi đối xứng (palindrome) hay không.

**Phân tích theo 4 bước:**
1. **Trạng thái:** `dp[trai][phai]` = true nếu đoạn từ `trai` đến `phai` là chuỗi đối xứng.
2. **Công thức:** `dp[trai][phai] = true` nếu `s[trai] == s[phai]` **và** (đoạn ở giữa `dp[trai+1][phai-1]` cũng đối xứng, hoặc đoạn giữa quá ngắn để cần kiểm tra thêm).
3. **Khởi tạo:** mọi đoạn có độ dài 1 đều đối xứng (`dp[i][i] = true`); đoạn độ dài 2 đối xứng khi 2 ký tự bằng nhau.
4. **Thứ tự:** tính theo độ dài đoạn tăng dần, giống bài toán chính của mục này.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<bool>> xayDungBangDoiXung(string s) {
    int n = (int)s.size();
    vector<vector<bool>> dp(n, vector<bool>(n, false));

    for (int i = 0; i < n; i++) {
        dp[i][i] = true; // đoạn độ dài 1 luôn đối xứng
    }

    for (int doDaiKhoang = 2; doDaiKhoang <= n; doDaiKhoang++) {
        for (int trai = 0; trai + doDaiKhoang - 1 < n; trai++) {
            int phai = trai + doDaiKhoang - 1;

            if (s[trai] != s[phai]) {
                dp[trai][phai] = false;
            } else if (doDaiKhoang == 2) {
                dp[trai][phai] = true; // chỉ có 2 ký tự, và chúng bằng nhau
            } else {
                dp[trai][phai] = dp[trai + 1][phai - 1];
            }
        }
    }

    return dp;
}

int main() {
    string s = "abcba";
    vector<vector<bool>> dp = xayDungBangDoiXung(s);

    cout << (dp[0][4] ? "Doan [0,4] doi xung" : "Doan [0,4] khong doi xung") << endl;
    cout << (dp[1][3] ? "Doan [1,3] doi xung" : "Doan [1,3] khong doi xung") << endl;

    return 0;
}
```

#### PYTHON
```python
def xay_dung_bang_doi_xung(s):
    n = len(s)
    dp = [[False] * n for _ in range(n)]

    for i in range(n):
        dp[i][i] = True

    for do_dai_khoang in range(2, n + 1):
        for trai in range(0, n - do_dai_khoang + 1):
            phai = trai + do_dai_khoang - 1

            if s[trai] != s[phai]:
                dp[trai][phai] = False
            elif do_dai_khoang == 2:
                dp[trai][phai] = True
            else:
                dp[trai][phai] = dp[trai + 1][phai - 1]

    return dp


s = "abcba"
dp = xay_dung_bang_doi_xung(s)

print("Doan [0,4] doi xung" if dp[0][4] else "Doan [0,4] khong doi xung")
print("Doan [1,3] doi xung" if dp[1][3] else "Doan [1,3] khong doi xung")
```

### Ví dụ 2 (Trung bình) — Số cách nhân ma trận tối ưu (Matrix Chain Multiplication)

Bài toán: cho n ma trận cần nhân với nhau theo đúng thứ tự cho trước (không được đổi thứ tự các ma trận, chỉ được đổi cách đặt ngoặc), mỗi ma trận thứ i có kích thước `p[i-1] x p[i]`. Hãy tìm số phép nhân số học tối thiểu cần thực hiện để tính được tích của tất cả các ma trận.

**Phân tích:** Đây là bài toán DP trên khoảng kinh điển nhất. `dp[trai][phai]` = số phép nhân tối thiểu để tính tích các ma trận từ vị trí `trai` đến `phai`. Với mỗi cách chia đoạn thành 2 phần tại vị trí `k` (nhân riêng 2 nhóm ma trận rồi nhân kết quả với nhau), chi phí là `dp[trai][k] + dp[k+1][phai] + p[trai-1]*p[k]*p[phai]`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timSoPhepNhanToiThieu(vector<int> p) {
    int n = (int)p.size() - 1; // số lượng ma trận
    vector<vector<long long>> dp(n + 1, vector<long long>(n + 1, 0));

    for (int doDaiKhoang = 2; doDaiKhoang <= n; doDaiKhoang++) {
        for (int trai = 1; trai + doDaiKhoang - 1 <= n; trai++) {
            int phai = trai + doDaiKhoang - 1;
            dp[trai][phai] = LLONG_MAX;

            for (int k = trai; k < phai; k++) {
                long long chiPhi = dp[trai][k] + dp[k + 1][phai]
                                  + (long long)p[trai - 1] * p[k] * p[phai];
                if (chiPhi < dp[trai][phai]) {
                    dp[trai][phai] = chiPhi;
                }
            }
        }
    }

    return dp[1][n];
}

int main() {
    // 3 ma trận: (10x30), (30x5), (5x60) -> p = {10, 30, 5, 60}
    vector<int> p = {10, 30, 5, 60};
    cout << timSoPhepNhanToiThieu(p) << endl;
    return 0;
}
```

#### PYTHON
```python
def tim_so_phep_nhan_toi_thieu(p):
    n = len(p) - 1
    dp = [[0] * (n + 1) for _ in range(n + 1)]

    for do_dai_khoang in range(2, n + 1):
        for trai in range(1, n - do_dai_khoang + 2):
            phai = trai + do_dai_khoang - 1
            dp[trai][phai] = float('inf')

            for k in range(trai, phai):
                chi_phi = dp[trai][k] + dp[k + 1][phai] + p[trai - 1] * p[k] * p[phai]
                if chi_phi < dp[trai][phai]:
                    dp[trai][phai] = chi_phi

    return dp[1][n]


p = [10, 30, 5, 60]
print(tim_so_phep_nhan_toi_thieu(p))
```

### Ví dụ 3 (Khó) — Bài toán chia kẹo tối ưu (Optimal Game Strategy / Predict the Winner dạng đơn giản)

Bài toán: có n viên kẹo xếp thành hàng, giá trị `a[i]`. Hai người chơi lần lượt lấy kẹo, mỗi lượt chỉ được lấy kẹo ở **đầu hoặc cuối** dãy hiện tại. Cả 2 người đều chơi tối ưu (luôn cố gắng lấy được nhiều giá trị nhất cho mình). Hãy tính giá trị lớn nhất mà người đi trước có thể đảm bảo lấy được, trừ đi giá trị người đi sau lấy được (hiệu số tối ưu).

**Phân tích:** Định nghĩa `dp[trai][phai]` = hiệu số tối ưu (giá trị người đang đi lấy được trừ giá trị đối thủ) mà người **đang đến lượt** có thể đạt được, khi chỉ còn đoạn kẹo từ `trai` đến `phai`. Người chơi có 2 lựa chọn: lấy kẹo trái (`a[trai] - dp[trai+1][phai]`, vì sau khi lấy, đối thủ trở thành người "đang đi" trên đoạn còn lại, nên hiệu số của đối thủ bị trừ đi) hoặc lấy kẹo phải (`a[phai] - dp[trai][phai-1]`). Chọn giá trị lớn hơn.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timHieuSoToiUu(vector<int> a) {
    int n = (int)a.size();
    vector<vector<long long>> dp(n, vector<long long>(n, 0));

    for (int i = 0; i < n; i++) {
        dp[i][i] = a[i]; // chỉ còn 1 viên kẹo, buộc phải lấy nó
    }

    for (int doDaiKhoang = 2; doDaiKhoang <= n; doDaiKhoang++) {
        for (int trai = 0; trai + doDaiKhoang - 1 < n; trai++) {
            int phai = trai + doDaiKhoang - 1;

            long long layTrai = a[trai] - dp[trai + 1][phai];
            long long layPhai = a[phai] - dp[trai][phai - 1];

            dp[trai][phai] = max(layTrai, layPhai);
        }
    }

    return dp[0][n - 1];
}

int main() {
    vector<int> a = {1, 5, 2, 4, 6};
    long long ketQua = timHieuSoToiUu(a);

    cout << "Hieu so toi uu: " << ketQua << endl;
    if (ketQua > 0) {
        cout << "Nguoi di truoc thang" << endl;
    } else if (ketQua < 0) {
        cout << "Nguoi di sau thang" << endl;
    } else {
        cout << "Hoa" << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def tim_hieu_so_toi_uu(a):
    n = len(a)
    dp = [[0] * n for _ in range(n)]

    for i in range(n):
        dp[i][i] = a[i]

    for do_dai_khoang in range(2, n + 1):
        for trai in range(0, n - do_dai_khoang + 1):
            phai = trai + do_dai_khoang - 1

            lay_trai = a[trai] - dp[trai + 1][phai]
            lay_phai = a[phai] - dp[trai][phai - 1]

            dp[trai][phai] = max(lay_trai, lay_phai)

    return dp[0][n - 1]


a = [1, 5, 2, 4, 6]
ket_qua = tim_hieu_so_toi_uu(a)

print("Hieu so toi uu:", ket_qua)
if ket_qua > 0:
    print("Nguoi di truoc thang")
elif ket_qua < 0:
    print("Nguoi di sau thang")
else:
    print("Hoa")
```

**Bài tập minh hoạ:** Cho một chuỗi ngoặc chưa hoàn chỉnh (chỉ có ký tự `(` và `)`, một số vị trí không xác định là đóng hay mở), hãy đếm số cách điền dấu ngoặc để tạo thành chuỗi ngoặc hợp lệ. (Bài minh hoạ đơn giản hoá — giả sử tất cả vị trí là `?`, có thể là `(` hoặc `)`.)

**Lời giải:** Đây là bài toán liên quan tới cấu trúc khoảng cân bằng ngoặc — sử dụng `dp[trai][phai]` biểu diễn số cách điền để đoạn từ `trai` đến `phai` là 1 chuỗi ngoặc cân bằng hoàn chỉnh, dựa trên việc chia đoạn tại vị trí ngoặc "khớp" với vị trí đầu tiên.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long demSoCachDienNgoac(int n) { // n là độ dài chuỗi, giả sử tất cả đều là "?"
    // dp[i] = số cách điền để i cặp ngoặc cân bằng hợp lệ (đây là số Catalan)
    vector<long long> dp(n / 2 + 1, 0);
    dp[0] = 1;

    for (int soCap = 1; soCap <= n / 2; soCap++) {
        for (int k = 0; k < soCap; k++) {
            dp[soCap] = dp[soCap] + dp[k] * dp[soCap - 1 - k];
        }
    }

    return dp[n / 2];
}

int main() {
    int n = 6; // 3 cặp ngoặc
    cout << demSoCachDienNgoac(n) << endl; // số Catalan thứ 3 = 5
    return 0;
}
```

#### PYTHON
```python
def dem_so_cach_dien_ngoac(n):
    dp = [0] * (n // 2 + 1)
    dp[0] = 1

    for so_cap in range(1, n // 2 + 1):
        for k in range(so_cap):
            dp[so_cap] = dp[so_cap] + dp[k] * dp[so_cap - 1 - k]

    return dp[n // 2]


n = 6
print(dem_so_cach_dien_ngoac(n))  # số Catalan thứ 3 = 5
```

> **Ghi chú:** đây chính là công thức số Catalan — một cấu trúc DP trên khoảng đặc biệt xuất hiện trong rất nhiều bài toán đếm (số cách chia đa giác thành tam giác, số cây nhị phân tìm kiếm khác nhau...), rất đáng để ghi nhớ như một "mẫu hình" quen thuộc.

---

## Tổng kết Chương 8

- **Quy trình 4 bước** (định nghĩa trạng thái, tìm công thức, xác định khởi tạo, xác định thứ tự tính) là khung tư duy áp dụng được cho **mọi** bài toán DP, dù đơn giản hay phức tạp — luôn viết ra rõ ràng cả 4 bước trước khi bắt tay code.
- **DP 1 chiều** (LIS, Coin Change, House Robber): trạng thái chỉ cần 1 chỉ số, thường đại diện cho "đã xét đến đâu" hoặc "giá trị mục tiêu còn lại bao nhiêu".
- **DP 2 chiều** (Knapsack, LCS, Edit Distance): trạng thái cần 2 chỉ số, thường xuất hiện khi có 2 "chiều lựa chọn" độc lập cần theo dõi đồng thời (ví dụ: đã xét vật nào + đã dùng bao nhiêu sức chứa; hoặc vị trí trong chuỗi 1 + vị trí trong chuỗi 2).
- **DP trên khoảng (Interval DP):** trạng thái biểu diễn bằng một cặp `[trái, phải]`, luôn tính theo **thứ tự độ dài khoảng tăng dần** — xuất hiện khi quyết định tại một điểm chia đôi bài toán thành 2 bài toán con độc lập trên 2 nửa của một đoạn.
- **Kỹ năng cốt lõi cần luyện:** trước khi viết code, luôn viết ra bằng lời **định nghĩa trạng thái** một cách rõ ràng, chính xác — đây là bước quyết định 90% khả năng giải đúng bài toán DP, quan trọng hơn nhiều so với việc nhớ code mẫu.

---

## Bài tập cuối chương — Chương 8

### PHẦN A: ĐỀ BÀI (16 bài, không kèm lời giải)

**Bài 1 — Climbing Stairs (LeetCode 70) — Dễ**
Giống hệt bài toán chính, mục 8.1 đã trình bày (dùng để luyện tập lại).

**Bài 2 — Fibonacci Number (LeetCode 509) — Dễ**
Tính số Fibonacci thứ n bằng quy hoạch động (không dùng đệ quy thuần tuý).

**Bài 3 — Coin Change (LeetCode 322) — Trung bình**
Giống hệt Ví dụ 3, mục 8.1 đã trình bày (dùng để luyện tập lại).

**Bài 4 — Coin Change II (LeetCode 518) — Trung bình**
Giống hệt Ví dụ 2, mục 8.1 đã trình bày (dùng để luyện tập lại).

**Bài 5 — House Robber II (LeetCode 213) — Trung bình**
Biến thể của Ví dụ 1, mục 8.2: các ngôi nhà được xếp thành **vòng tròn** (nhà đầu tiên và nhà cuối cùng cũng được coi là liền kề nhau). Hãy tìm số tiền lớn nhất có thể trộm được.

**Bài 6 — Longest Increasing Subsequence (LeetCode 300) — Trung bình**
Giống hệt bài toán chính, mục 8.2 đã trình bày (dùng để luyện tập lại cả 2 cách O(n²) và O(n log n)).

**Bài 7 — Maximum Product Subarray (LeetCode 152) — Trung bình**
Cho mảng số nguyên (có thể có số âm), tìm đoạn con liên tiếp có **tích** lớn nhất. (Gợi ý: khác với tổng, tích của 2 số âm có thể tạo ra số dương lớn — cần theo dõi đồng thời cả giá trị lớn nhất và nhỏ nhất tại mỗi vị trí.)

**Bài 8 — Longest Common Subsequence (LeetCode 1143) — Trung bình**
Giống hệt Ví dụ 1, mục 8.3 đã trình bày (dùng để luyện tập lại).

**Bài 9 — Edit Distance (LeetCode 72) — Trung bình-Khó**
Giống hệt Ví dụ 2, mục 8.3 đã trình bày (dùng để luyện tập lại).

**Bài 10 — Partition Equal Subset Sum (LeetCode 416) — Trung bình**
Cho mảng số nguyên dương, kiểm tra xem có thể chia mảng thành 2 tập con có tổng bằng nhau hay không. (Gợi ý: đây là biến thể của bài toán cái túi 0/1 — mục tiêu là kiểm tra tồn tại tập con có tổng bằng đúng một nửa tổng toàn mảng.)

**Bài 11 — Target Sum (LeetCode 494) — Trung bình**
Cho mảng số nguyên và số target, mỗi số được gán dấu + hoặc -, đếm số cách gán dấu để tổng đúng bằng target.

**Bài 12 — Interleaving String (LeetCode 97) — Trung bình-Khó**
Cho 3 chuỗi `s1`, `s2`, `s3`, kiểm tra xem `s3` có phải được tạo thành bằng cách trộn xen kẽ (giữ nguyên thứ tự nội bộ) các ký tự của `s1` và `s2` hay không.

**Bài 13 — Palindrome Partitioning II (LeetCode 132) — Khó**
Cho một chuỗi, tìm số lần cắt tối thiểu để chia chuỗi đó thành các đoạn con mà mỗi đoạn đều là chuỗi đối xứng. (Gợi ý: kết hợp bảng đối xứng ở Ví dụ 1, mục 8.4 với một DP 1 chiều bổ sung.)

**Bài 14 — Matrix Chain Multiplication (bài toán kinh điển, nhiều nguồn) — Khó**
Giống hệt Ví dụ 2, mục 8.4 đã trình bày (dùng để luyện tập lại).

**Bài 15 — Predict the Winner (LeetCode 486) — Trung bình-Khó**
Giống hệt Ví dụ 3, mục 8.4, nhưng chỉ cần trả lời true/false liệu người đi trước có thắng hoặc hoà hay không.

**Bài 16 — Burst Balloons (LeetCode 312) — Khó**
Giống hệt bài toán chính, mục 8.4 đã trình bày (dùng để luyện tập lại — đây chính là phiên bản gốc nổi tiếng của bài toán "loại đá").

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Climbing Stairs</summary>

Giống hệt code khung mẫu, mục 8.1, hàm `demSoCachLeoCauThang`/`dem_so_cach_leo_cau_thang`.
</details>

<details>
<summary>Lời giải Bài 2 — Fibonacci Number</summary>

Cấu trúc hoàn toàn giống bài toán cầu thang (mục 8.1), chỉ khác giá trị khởi tạo: `dp[0] = 0`, `dp[1] = 1`.

#### C++
```cpp
long long fib(int n) {
    if (n == 0) return 0;
    vector<long long> dp(n + 1, 0);
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n];
}
```

#### PYTHON
```python
def fib(n):
    if n == 0:
        return 0
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]
    return dp[n]
```
</details>

<details>
<summary>Lời giải Bài 3 — Coin Change</summary>

Giống hệt Ví dụ 3, mục 8.1, hàm `timSoXuItNhat`/`tim_so_xu_it_nhat`.
</details>

<details>
<summary>Lời giải Bài 4 — Coin Change II</summary>

Giống hệt Ví dụ 2, mục 8.1, hàm `demSoCachDoiTien`/`dem_so_cach_doi_tien`.
</details>

<details>
<summary>Lời giải Bài 5 — House Robber II</summary>

**Ý tưởng:** Vì nhà đầu và nhà cuối liền kề nhau (vòng tròn), ta không thể chọn cả 2. Giải quyết bằng cách chạy thuật toán House Robber gốc (mục 8.2) **2 lần**: một lần trên đoạn "loại bỏ nhà cuối" (từ nhà 0 đến nhà n-2), một lần trên đoạn "loại bỏ nhà đầu" (từ nhà 1 đến nhà n-1), rồi lấy kết quả lớn hơn.

#### C++
```cpp
long long timSoTienTromLonNhatDoanThang(vector<int> a, int batDau, int ketThuc) {
    if (batDau > ketThuc) return 0;
    if (batDau == ketThuc) return a[batDau];

    long long truoc2 = a[batDau];
    long long truoc1 = max(a[batDau], a[batDau + 1]);

    for (int i = batDau + 2; i <= ketThuc; i++) {
        long long hienTai = max(truoc1, truoc2 + a[i]);
        truoc2 = truoc1;
        truoc1 = hienTai;
    }

    return truoc1;
}

long long robCircular(vector<int> a) {
    int n = (int)a.size();
    if (n == 1) return a[0];

    long long ketQua1 = timSoTienTromLonNhatDoanThang(a, 0, n - 2);
    long long ketQua2 = timSoTienTromLonNhatDoanThang(a, 1, n - 1);

    return max(ketQua1, ketQua2);
}
```

#### PYTHON
```python
def tim_so_tien_trom_lon_nhat_doan_thang(a, bat_dau, ket_thuc):
    if bat_dau > ket_thuc:
        return 0
    if bat_dau == ket_thuc:
        return a[bat_dau]

    truoc2 = a[bat_dau]
    truoc1 = max(a[bat_dau], a[bat_dau + 1])

    for i in range(bat_dau + 2, ket_thuc + 1):
        hien_tai = max(truoc1, truoc2 + a[i])
        truoc2 = truoc1
        truoc1 = hien_tai

    return truoc1


def rob_circular(a):
    n = len(a)
    if n == 1:
        return a[0]

    ket_qua_1 = tim_so_tien_trom_lon_nhat_doan_thang(a, 0, n - 2)
    ket_qua_2 = tim_so_tien_trom_lon_nhat_doan_thang(a, 1, n - 1)

    return max(ket_qua_1, ket_qua_2)
```
</details>

<details>
<summary>Lời giải Bài 6 — Longest Increasing Subsequence</summary>

Giống hệt code khung mẫu (O(n²)) hoặc Ví dụ 2 (O(n log n)), mục 8.2.
</details>

<details>
<summary>Lời giải Bài 7 — Maximum Product Subarray</summary>

**Ý tưởng:** Duy trì đồng thời `dpMax[i]` (tích lớn nhất kết thúc tại i) và `dpMin[i]` (tích nhỏ nhất kết thúc tại i) — vì khi gặp số âm, `dpMin` (thường âm) nhân với số âm mới có thể trở thành `dpMax` mới.

#### C++
```cpp
long long maxProduct(vector<int> a) {
    long long dpMaxHienTai = a[0];
    long long dpMinHienTai = a[0];
    long long ketQuaTotNhat = a[0];

    for (int i = 1; i < (int)a.size(); i++) {
        long long ungCu1 = a[i];
        long long ungCu2 = dpMaxHienTai * a[i];
        long long ungCu3 = dpMinHienTai * a[i];

        long long dpMaxMoi = max({ungCu1, ungCu2, ungCu3});
        long long dpMinMoi = min({ungCu1, ungCu2, ungCu3});

        dpMaxHienTai = dpMaxMoi;
        dpMinHienTai = dpMinMoi;

        if (dpMaxHienTai > ketQuaTotNhat) {
            ketQuaTotNhat = dpMaxHienTai;
        }
    }

    return ketQuaTotNhat;
}
```

#### PYTHON
```python
def max_product(a):
    dp_max_hien_tai = a[0]
    dp_min_hien_tai = a[0]
    ket_qua_tot_nhat = a[0]

    for i in range(1, len(a)):
        ung_cu_1 = a[i]
        ung_cu_2 = dp_max_hien_tai * a[i]
        ung_cu_3 = dp_min_hien_tai * a[i]

        dp_max_moi = max(ung_cu_1, ung_cu_2, ung_cu_3)
        dp_min_moi = min(ung_cu_1, ung_cu_2, ung_cu_3)

        dp_max_hien_tai = dp_max_moi
        dp_min_hien_tai = dp_min_moi

        if dp_max_hien_tai > ket_qua_tot_nhat:
            ket_qua_tot_nhat = dp_max_hien_tai

    return ket_qua_tot_nhat
```
</details>

<details>
<summary>Lời giải Bài 8 — Longest Common Subsequence</summary>

Giống hệt Ví dụ 1, mục 8.3, hàm `timDoDaiLCS`/`tim_do_dai_lcs`.
</details>

<details>
<summary>Lời giải Bài 9 — Edit Distance</summary>

Giống hệt Ví dụ 2, mục 8.3, hàm `timKhoangCachChinhSua`/`tim_khoang_cach_chinh_sua`.
</details>

<details>
<summary>Lời giải Bài 10 — Partition Equal Subset Sum</summary>

**Ý tưởng:** Tính tổng toàn mảng; nếu tổng lẻ, chắc chắn không chia được. Nếu tổng chẵn, kiểm tra xem có tồn tại tập con có tổng đúng bằng `tổng / 2` không (dùng DP kiểu Knapsack 0/1, nhưng chỉ cần biết "có đạt được hay không", không cần giá trị lớn nhất).

#### C++
```cpp
bool canPartition(vector<int> a) {
    long long tongTatCa = 0;
    for (int i = 0; i < (int)a.size(); i++) {
        tongTatCa = tongTatCa + a[i];
    }

    if (tongTatCa % 2 != 0) {
        return false;
    }

    long long target = tongTatCa / 2;
    vector<bool> dp(target + 1, false);
    dp[0] = true;

    for (int i = 0; i < (int)a.size(); i++) {
        for (long long w = target; w >= a[i]; w--) {
            if (dp[w - a[i]] == true) {
                dp[w] = true;
            }
        }
    }

    return dp[target];
}
```

#### PYTHON
```python
def can_partition(a):
    tong_tat_ca = sum(a)

    if tong_tat_ca % 2 != 0:
        return False

    target = tong_tat_ca // 2
    dp = [False] * (target + 1)
    dp[0] = True

    for x in a:
        for w in range(target, x - 1, -1):
            if dp[w - x] == True:
                dp[w] = True

    return dp[target]
```
</details>

<details>
<summary>Lời giải Bài 11 — Target Sum</summary>

**Ý tưởng:** Gọi P là tổng các số được gán dấu +, N là tổng các số được gán dấu -. Ta có `P + N = tongTatCa` và `P - N = target`, suy ra `P = (tongTatCa + target) / 2`. Bài toán trở thành: đếm số tập con có tổng đúng bằng P (dùng DP đếm số cách, giống Ví dụ 2 mục 8.1).

#### C++
```cpp
int findTargetSumWays(vector<int> a, int target) {
    long long tongTatCa = 0;
    for (int i = 0; i < (int)a.size(); i++) {
        tongTatCa = tongTatCa + a[i];
    }

    if ((tongTatCa + target) % 2 != 0 || tongTatCa < abs(target)) {
        return 0;
    }

    long long P = (tongTatCa + target) / 2;
    vector<long long> dp(P + 1, 0);
    dp[0] = 1;

    for (int i = 0; i < (int)a.size(); i++) {
        for (long long w = P; w >= a[i]; w--) {
            dp[w] = dp[w] + dp[w - a[i]];
        }
    }

    return (int)dp[P];
}
```

#### PYTHON
```python
def find_target_sum_ways(a, target):
    tong_tat_ca = sum(a)

    if (tong_tat_ca + target) % 2 != 0 or tong_tat_ca < abs(target):
        return 0

    P = (tong_tat_ca + target) // 2
    dp = [0] * (P + 1)
    dp[0] = 1

    for x in a:
        for w in range(P, x - 1, -1):
            dp[w] = dp[w] + dp[w - x]

    return dp[P]
```
</details>

<details>
<summary>Lời giải Bài 12 — Interleaving String</summary>

**Ý tưởng:** `dp[i][j]` = true nếu `i` ký tự đầu của `s1` và `j` ký tự đầu của `s2` có thể trộn thành `i+j` ký tự đầu của `s3`. Công thức: `dp[i][j]` đúng nếu (`s1[i-1] == s3[i+j-1]` và `dp[i-1][j]` đúng) hoặc (`s2[j-1] == s3[i+j-1]` và `dp[i][j-1]` đúng).

#### C++
```cpp
bool isInterleave(string s1, string s2, string s3) {
    int n = (int)s1.size();
    int m = (int)s2.size();

    if (n + m != (int)s3.size()) {
        return false;
    }

    vector<vector<bool>> dp(n + 1, vector<bool>(m + 1, false));
    dp[0][0] = true;

    for (int i = 1; i <= n; i++) {
        dp[i][0] = dp[i - 1][0] && (s1[i - 1] == s3[i - 1]);
    }
    for (int j = 1; j <= m; j++) {
        dp[0][j] = dp[0][j - 1] && (s2[j - 1] == s3[j - 1]);
    }

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            bool tuS1 = dp[i - 1][j] && (s1[i - 1] == s3[i + j - 1]);
            bool tuS2 = dp[i][j - 1] && (s2[j - 1] == s3[i + j - 1]);
            dp[i][j] = tuS1 || tuS2;
        }
    }

    return dp[n][m];
}
```

#### PYTHON
```python
def is_interleave(s1, s2, s3):
    n = len(s1)
    m = len(s2)

    if n + m != len(s3):
        return False

    dp = [[False] * (m + 1) for _ in range(n + 1)]
    dp[0][0] = True

    for i in range(1, n + 1):
        dp[i][0] = dp[i - 1][0] and (s1[i - 1] == s3[i - 1])
    for j in range(1, m + 1):
        dp[0][j] = dp[0][j - 1] and (s2[j - 1] == s3[j - 1])

    for i in range(1, n + 1):
        for j in range(1, m + 1):
            tu_s1 = dp[i - 1][j] and (s1[i - 1] == s3[i + j - 1])
            tu_s2 = dp[i][j - 1] and (s2[j - 1] == s3[i + j - 1])
            dp[i][j] = tu_s1 or tu_s2

    return dp[n][m]
```
</details>

<details>
<summary>Lời giải Bài 13 — Palindrome Partitioning II</summary>

**Ý tưởng:** Bước 1, xây dựng bảng đối xứng `laDoiXung[i][j]` giống hệt Ví dụ 1, mục 8.4. Bước 2, dùng DP 1 chiều: `dp[i]` = số lần cắt tối thiểu cho `i` ký tự đầu tiên, với `dp[i] = min(dp[j] + 1)` với mọi `j < i` sao cho đoạn `[j, i-1]` đối xứng.

#### C++
```cpp
int minCut(string s) {
    int n = (int)s.size();
    vector<vector<bool>> laDoiXung(n, vector<bool>(n, false));

    for (int i = 0; i < n; i++) {
        laDoiXung[i][i] = true;
    }

    for (int doDaiKhoang = 2; doDaiKhoang <= n; doDaiKhoang++) {
        for (int trai = 0; trai + doDaiKhoang - 1 < n; trai++) {
            int phai = trai + doDaiKhoang - 1;
            if (s[trai] == s[phai]) {
                if (doDaiKhoang == 2 || laDoiXung[trai + 1][phai - 1] == true) {
                    laDoiXung[trai][phai] = true;
                }
            }
        }
    }

    vector<int> dp(n, 0);
    for (int i = 0; i < n; i++) {
        if (laDoiXung[0][i] == true) {
            dp[i] = 0; // toàn bộ đoạn [0, i] đã đối xứng, không cần cắt
        } else {
            dp[i] = i; // trường hợp xấu nhất: cắt thành từng ký tự riêng lẻ
            for (int j = 1; j <= i; j++) {
                if (laDoiXung[j][i] == true) {
                    if (dp[j - 1] + 1 < dp[i]) {
                        dp[i] = dp[j - 1] + 1;
                    }
                }
            }
        }
    }

    return dp[n - 1];
}
```

#### PYTHON
```python
def min_cut(s):
    n = len(s)
    la_doi_xung = [[False] * n for _ in range(n)]

    for i in range(n):
        la_doi_xung[i][i] = True

    for do_dai_khoang in range(2, n + 1):
        for trai in range(0, n - do_dai_khoang + 1):
            phai = trai + do_dai_khoang - 1
            if s[trai] == s[phai]:
                if do_dai_khoang == 2 or la_doi_xung[trai + 1][phai - 1] == True:
                    la_doi_xung[trai][phai] = True

    dp = [0] * n
    for i in range(n):
        if la_doi_xung[0][i] == True:
            dp[i] = 0
        else:
            dp[i] = i
            for j in range(1, i + 1):
                if la_doi_xung[j][i] == True:
                    if dp[j - 1] + 1 < dp[i]:
                        dp[i] = dp[j - 1] + 1

    return dp[n - 1]
```
</details>

<details>
<summary>Lời giải Bài 14 — Matrix Chain Multiplication</summary>

Giống hệt Ví dụ 2, mục 8.4, hàm `timSoPhepNhanToiThieu`/`tim_so_phep_nhan_toi_thieu`.
</details>

<details>
<summary>Lời giải Bài 15 — Predict the Winner</summary>

Giống hệt Ví dụ 3, mục 8.4 — chỉ cần kiểm tra `dp[0][n-1] >= 0` (người đi trước thắng hoặc hoà).

#### C++
```cpp
bool predictTheWinner(vector<int> a) {
    long long hieuSo = timHieuSoToiUu(a); // dùng lại hàm ở Ví dụ 3
    return hieuSo >= 0;
}
```

#### PYTHON
```python
def predict_the_winner(a):
    hieu_so = tim_hieu_so_toi_uu(a)  # dùng lại hàm ở Ví dụ 3
    return hieu_so >= 0
```
</details>

<details>
<summary>Lời giải Bài 16 — Burst Balloons</summary>

Giống hệt bài toán chính, mục 8.4, hàm `giaiBaiToanLoaiDa`/`giai_bai_toan_loai_da`. Lưu ý đề gốc LeetCode cần thêm 2 "quả bóng ảo" giá trị 1 ở 2 đầu mảng trước khi áp dụng công thức, để xử lý đúng trường hợp bóng ở biên bị nổ.
</details>
