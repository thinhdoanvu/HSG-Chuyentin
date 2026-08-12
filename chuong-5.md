# Chương 5: Mảng cộng dồn & Mảng hiệu

---

## 5.1. Mảng cộng dồn 1 chiều (Prefix Sum)

**Nêu bài toán:** Cho một mảng `a` gồm n số nguyên. Có q truy vấn, mỗi truy vấn hỏi: tổng các phần tử từ vị trí `l` đến vị trí `r` (cả 2 đầu) là bao nhiêu? Yêu cầu trả lời nhanh cho tất cả q truy vấn.

**Phân tích vấn đề:** Nếu chỉ có 1 truy vấn, ta chỉ cần duyệt từ `l` đến `r` rồi cộng dồn, tốn O(n). Nhưng nếu có q truy vấn, và q có thể lên tới hàng trăm nghìn, việc tính lại từ đầu cho mỗi truy vấn sẽ rất lãng phí — đặc biệt khi nhiều truy vấn có đoạn `[l, r]` chồng lấn lên nhau, ta đang tính đi tính lại nhiều lần cùng một dữ liệu.

**Giải pháp đơn thuần:** Với mỗi truy vấn, duyệt từ `l` đến `r`, cộng dồn giá trị lại. Với q truy vấn, mỗi truy vấn tốn tối đa O(n), tổng độ phức tạp là O(q × n).

**Khó khăn với giải pháp đơn thuần:** Nếu n và q đều lên tới 200.000, thì O(q × n) sẽ xấp xỉ 4 × 10^10 phép tính — vượt xa khả năng xử lý trong giới hạn thời gian thông thường (khoảng 10^8 phép tính mỗi giây), dẫn tới lỗi quá thời gian (TLE).

**Cách tiếp cận mới:** Ta xây dựng trước một mảng phụ gọi là **mảng cộng dồn** (prefix sum), trong đó `mangCongDon[i]` là tổng của tất cả các phần tử từ vị trí 0 đến vị trí `i - 1` trong mảng gốc. Sau khi có mảng cộng dồn này, tổng của đoạn `[l, r]` có thể tính được chỉ bằng một phép trừ, không cần duyệt lại.

### Minh hoạ lời giải chi tiết

Cho mảng gốc: `a = [3, 1, 4, 1, 5, 9, 2, 6]`.

Ta xây dựng mảng cộng dồn `mangCongDon` với `mangCongDon[0] = 0`, và `mangCongDon[i] = mangCongDon[i-1] + a[i-1]`:

| i | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|---|
| mangCongDon[i] | 0 | 3 | 4 | 8 | 9 | 14 | 23 | 25 | 31 |

Muốn tính tổng đoạn `[2, 5]` (các phần tử `a[2], a[3], a[4], a[5]` tức là `4, 1, 5, 9`, tổng đúng là 19), ta chỉ cần lấy `mangCongDon[6] - mangCongDon[2] = 23 - 4 = 19`. Kết quả khớp, và ta chỉ tốn đúng 1 phép trừ, không cần duyệt qua từng phần tử.

**Công thức tổng quát:** tổng của đoạn `[l, r]` (0-indexed, cả 2 đầu) bằng `mangCongDon[r + 1] - mangCongDon[l]`.

### Code khung mẫu

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {3, 1, 4, 1, 5, 9, 2, 6};
    int n = (int)a.size();

    vector<long long> mangCongDon(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangCongDon[i + 1] = mangCongDon[i] + a[i];
    }

    int l = 2, r = 5;
    long long tongDoan = mangCongDon[r + 1] - mangCongDon[l];
    cout << "Tong doan [" << l << ", " << r << "]: " << tongDoan << endl;

    return 0;
}
```
```python
a = [3, 1, 4, 1, 5, 9, 2, 6]
n = len(a)

mang_cong_don = [0] * (n + 1)
for i in range(n):
    mang_cong_don[i + 1] = mang_cong_don[i] + a[i]

l = 2
r = 5
tong_doan = mang_cong_don[r + 1] - mang_cong_don[l]
print("Tong doan [", l, ",", r, "]:", tong_doan)
```

### Ví dụ 1 (Dễ) — Trả lời nhiều truy vấn tổng đoạn

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;

    vector<long long> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    vector<long long> mangCongDon(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangCongDon[i + 1] = mangCongDon[i] + a[i];
    }

    int soTruyVan;
    cin >> soTruyVan;

    for (int i = 0; i < soTruyVan; i++) {
        int l, r;
        cin >> l >> r;
        long long ketQua = mangCongDon[r + 1] - mangCongDon[l];
        cout << ketQua << endl;
    }

    return 0;
}
```
```python
n = int(input())
a = list(map(int, input().split()))

mang_cong_don = [0] * (n + 1)
for i in range(n):
    mang_cong_don[i + 1] = mang_cong_don[i] + a[i]

so_truy_van = int(input())

for i in range(so_truy_van):
    l, r = map(int, input().split())
    ket_qua = mang_cong_don[r + 1] - mang_cong_don[l]
    print(ket_qua)
```

### Ví dụ 2 (Trung bình) — Đếm số lượng phần tử thoả điều kiện trong đoạn bằng mảng cộng dồn nhị phân

Bài toán: cho mảng n số nguyên, có q truy vấn, mỗi truy vấn hỏi "trong đoạn `[l, r]`, có bao nhiêu số chẵn?".

**Phân tích:** Ta không cần mảng cộng dồn của chính giá trị `a[i]`, mà xây dựng mảng cộng dồn của một mảng phụ `b[i]`, với `b[i] = 1` nếu `a[i]` là số chẵn, ngược lại `b[i] = 0`. Khi đó, "số lượng số chẵn trong đoạn `[l, r]`" chính là tổng của đoạn `[l, r]` trên mảng `b` — áp dụng lại đúng công thức mảng cộng dồn đã học.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {2, 5, 4, 7, 8, 1, 6};
    int n = (int)a.size();

    vector<int> b(n);
    for (int i = 0; i < n; i++) {
        if (a[i] % 2 == 0) {
            b[i] = 1;
        } else {
            b[i] = 0;
        }
    }

    vector<int> mangCongDon(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangCongDon[i + 1] = mangCongDon[i] + b[i];
    }

    int l = 1, r = 4;
    int soLuongSoChan = mangCongDon[r + 1] - mangCongDon[l];
    cout << "So luong so chan trong doan [" << l << ", " << r << "]: " << soLuongSoChan << endl;

    return 0;
}
```
```python
a = [2, 5, 4, 7, 8, 1, 6]
n = len(a)

b = []
for i in range(n):
    if a[i] % 2 == 0:
        b.append(1)
    else:
        b.append(0)

mang_cong_don = [0] * (n + 1)
for i in range(n):
    mang_cong_don[i + 1] = mang_cong_don[i] + b[i]

l = 1
r = 4
so_luong_so_chan = mang_cong_don[r + 1] - mang_cong_don[l]
print("So luong so chan trong doan [", l, ",", r, "]:", so_luong_so_chan)
```

### Ví dụ 3 (Khó) — Tìm đoạn con dài nhất có tổng không vượt quá S bằng cách kết hợp mảng cộng dồn và tìm kiếm nhị phân

Bài toán: cho mảng n số nguyên **dương**, và số S. Tìm độ dài lớn nhất của một đoạn con liên tiếp sao cho tổng của đoạn đó không vượt quá S.

**Phân tích:** Vì mảng toàn số dương, mảng cộng dồn của nó là một dãy **tăng dần nghiêm ngặt**. Nhờ tính chất tăng dần này, với mỗi vị trí bắt đầu `l`, ta có thể dùng tìm kiếm nhị phân trên mảng cộng dồn để tìm vị trí `r` xa nhất sao cho tổng đoạn `[l, r]` vẫn không vượt quá S — thay vì phải thử từng `r` một cách tuần tự.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<long long> a = {1, 2, 3, 4, 5, 6, 7};
    int n = (int)a.size();
    long long S = 12;

    vector<long long> mangCongDon(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangCongDon[i + 1] = mangCongDon[i] + a[i];
    }

    int doDaiLonNhat = 0;

    for (int l = 0; l < n; l++) {
        // Tìm vị trí r xa nhất sao cho mangCongDon[r+1] - mangCongDon[l] <= S
        // Tương đương: mangCongDon[r+1] <= mangCongDon[l] + S
        long long gioiHan = mangCongDon[l] + S;

        // upper_bound tìm vị trí ĐẦU TIÊN có giá trị > gioiHan
        auto conTro = upper_bound(mangCongDon.begin(), mangCongDon.end(), gioiHan);
        int viTriRCongMot = (int)(conTro - mangCongDon.begin()) - 1;

        int doDaiDoan = viTriRCongMot - l;
        if (doDaiDoan > doDaiLonNhat) {
            doDaiLonNhat = doDaiDoan;
        }
    }

    cout << "Do dai doan con dai nhat: " << doDaiLonNhat << endl;

    return 0;
}
```
```python
import bisect

a = [1, 2, 3, 4, 5, 6, 7]
n = len(a)
S = 12

mang_cong_don = [0] * (n + 1)
for i in range(n):
    mang_cong_don[i + 1] = mang_cong_don[i] + a[i]

do_dai_lon_nhat = 0

for l in range(n):
    gioi_han = mang_cong_don[l] + S
    vi_tri_r_cong_mot = bisect.bisect_right(mang_cong_don, gioi_han) - 1

    do_dai_doan = vi_tri_r_cong_mot - l
    if do_dai_doan > do_dai_lon_nhat:
        do_dai_lon_nhat = do_dai_doan

print("Do dai doan con dai nhat:", do_dai_lon_nhat)
```

**Bài tập minh hoạ:** Cho mảng n số nguyên, có q truy vấn, mỗi truy vấn hỏi "trung bình cộng của đoạn `[l, r]` là bao nhiêu?".

**Lời giải:** Dùng mảng cộng dồn để tính tổng đoạn `[l, r]` trong O(1), sau đó chia cho số lượng phần tử của đoạn đó (`r - l + 1`).

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<long long> a = {10, 20, 30, 40, 50};
    int n = (int)a.size();

    vector<long long> mangCongDon(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangCongDon[i + 1] = mangCongDon[i] + a[i];
    }

    int l = 1, r = 3;
    long long tongDoan = mangCongDon[r + 1] - mangCongDon[l];
    int soLuongPhanTu = r - l + 1;
    double trungBinh = (double)tongDoan / (double)soLuongPhanTu;

    cout << fixed << setprecision(2) << trungBinh << endl;

    return 0;
}
```
```python
a = [10, 20, 30, 40, 50]
n = len(a)

mang_cong_don = [0] * (n + 1)
for i in range(n):
    mang_cong_don[i + 1] = mang_cong_don[i] + a[i]

l = 1
r = 3
tong_doan = mang_cong_don[r + 1] - mang_cong_don[l]
so_luong_phan_tu = r - l + 1
trung_binh = tong_doan / so_luong_phan_tu

print(round(trung_binh, 2))
```

---

## 5.2. Mảng hiệu 1 chiều (Difference Array)

**Nêu bài toán:** Cho một mảng `a` gồm n số nguyên, ban đầu tất cả bằng 0. Có q thao tác, mỗi thao tác yêu cầu: cộng thêm một giá trị `val` vào tất cả các phần tử trong đoạn `[l, r]`. Sau khi thực hiện xong tất cả q thao tác, hãy in ra mảng `a` cuối cùng.

**Phân tích vấn đề:** Đây là bài toán **ngược lại** với mảng cộng dồn — thay vì hỏi tổng của một đoạn, ta cần **cập nhật giá trị** cho cả một đoạn nhiều lần.

**Giải pháp đơn thuần:** Với mỗi thao tác, duyệt từ `l` đến `r`, cộng `val` vào từng phần tử. Với q thao tác, mỗi thao tác tốn tối đa O(n), tổng độ phức tạp là O(q × n).

**Khó khăn với giải pháp đơn thuần:** Tương tự như bài toán mảng cộng dồn, nếu n và q đều lớn (ví dụ 200.000), độ phức tạp O(q × n) sẽ gây lỗi quá thời gian.

**Cách tiếp cận mới:** Ta xây dựng một mảng phụ gọi là **mảng hiệu** (difference array), sao cho mỗi thao tác cộng vào cả đoạn `[l, r]` chỉ cần **2 phép cập nhật** trên mảng hiệu, thay vì duyệt cả đoạn. Sau khi thực hiện xong tất cả thao tác, ta dựng lại mảng gốc bằng cách tính mảng cộng dồn của mảng hiệu.

### Minh hoạ lời giải chi tiết

Ý tưởng: để "cộng `val` vào toàn bộ đoạn `[l, r]`", ta chỉ cần ghi nhận 2 điều: "tại vị trí `l`, có một sự thay đổi bắt đầu là +val" và "tại vị trí `r + 1`, sự thay đổi đó kết thúc, tức là -val". Sau đó, khi ta cộng dồn (giống như xây mảng cộng dồn ở mục 5.1) mảng ghi nhận này, hiệu ứng "+val" sẽ tự động lan toả đúng từ vị trí `l` đến vị trí `r`, và dừng lại ngay sau đó.

Ví dụ: mảng gốc có 6 phần tử, ban đầu toàn số 0. Thực hiện thao tác "cộng 5 vào đoạn `[1, 3]`":

| Vị trí | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| mangHieu trước | 0 | 0 | 0 | 0 | 0 | 0 |
| mangHieu sau khi cộng | 0 | +5 | 0 | 0 | -5 | 0 |

Khi ta cộng dồn mảng hiệu này từ trái sang phải: `0, 5, 5, 5, 0, 0` — đúng bằng hiệu ứng "cộng 5 vào các vị trí 1, 2, 3" như mong muốn.

### Code khung mẫu

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 6;
    vector<long long> mangHieu(n + 1, 0);

    // Thao tác: cộng 5 vào đoạn [1, 3]
    int l = 1, r = 3;
    long long val = 5;
    mangHieu[l] = mangHieu[l] + val;
    mangHieu[r + 1] = mangHieu[r + 1] - val;

    // Dựng lại mảng gốc bằng cách cộng dồn mảng hiệu
    vector<long long> ketQua(n, 0);
    long long giaTriChayDong = 0;
    for (int i = 0; i < n; i++) {
        giaTriChayDong = giaTriChayDong + mangHieu[i];
        ketQua[i] = giaTriChayDong;
    }

    for (int i = 0; i < n; i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```
```python
n = 6
mang_hieu = [0] * (n + 1)

# Thao tác: cộng 5 vào đoạn [1, 3]
l = 1
r = 3
val = 5
mang_hieu[l] = mang_hieu[l] + val
mang_hieu[r + 1] = mang_hieu[r + 1] - val

ket_qua = [0] * n
gia_tri_chay_dong = 0
for i in range(n):
    gia_tri_chay_dong = gia_tri_chay_dong + mang_hieu[i]
    ket_qua[i] = gia_tri_chay_dong

for gia_tri in ket_qua:
    print(gia_tri, end=" ")
print()
```

### Ví dụ 1 (Dễ) — Thực hiện nhiều thao tác cộng đoạn

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, soThaoTac;
    cin >> n >> soThaoTac;

    vector<long long> mangHieu(n + 1, 0);

    for (int i = 0; i < soThaoTac; i++) {
        int l, r;
        long long val;
        cin >> l >> r >> val;

        mangHieu[l] = mangHieu[l] + val;
        mangHieu[r + 1] = mangHieu[r + 1] - val;
    }

    vector<long long> ketQua(n, 0);
    long long giaTriChayDong = 0;
    for (int i = 0; i < n; i++) {
        giaTriChayDong = giaTriChayDong + mangHieu[i];
        ketQua[i] = giaTriChayDong;
    }

    for (int i = 0; i < n; i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```
```python
n, so_thao_tac = map(int, input().split())

mang_hieu = [0] * (n + 1)

for i in range(so_thao_tac):
    l, r, val = map(int, input().split())

    mang_hieu[l] = mang_hieu[l] + val
    mang_hieu[r + 1] = mang_hieu[r + 1] - val

ket_qua = [0] * n
gia_tri_chay_dong = 0
for i in range(n):
    gia_tri_chay_dong = gia_tri_chay_dong + mang_hieu[i]
    ket_qua[i] = gia_tri_chay_dong

print(*ket_qua)
```

### Ví dụ 2 (Trung bình) — Bài toán đặt lịch, tìm tải trọng lớn nhất tại một thời điểm

Bài toán: có n khoảng thời gian đặt chỗ, mỗi khoảng `[l, r]` có một số lượng khách `soLuongKhach`. Hãy tìm số lượng khách lớn nhất có mặt tại cùng một thời điểm bất kỳ.

**Phân tích:** Đây chính là ứng dụng trực tiếp của mảng hiệu: mỗi lượt đặt chỗ là một thao tác "cộng `soLuongKhach` vào đoạn `[l, r]`". Sau khi dựng lại mảng bằng cách cộng dồn mảng hiệu, giá trị lớn nhất trong mảng kết quả chính là câu trả lời.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 10; // độ dài trục thời gian, ví dụ từ 0 đến 9

    vector<pair<int,int>> danhSachDatCho; // (khoảng, số khách) - đơn giản hoá: dùng tuple riêng
    // Để rõ ràng, ta dùng 3 mảng song song thay vì cấu trúc lồng ghép phức tạp
    vector<int> danhSachL = {0, 1, 2};
    vector<int> danhSachR = {2, 3, 5};
    vector<int> danhSachSoKhach = {10, 20, 25};

    vector<long long> mangHieu(n + 2, 0);

    for (int i = 0; i < (int)danhSachL.size(); i++) {
        int l = danhSachL[i];
        int r = danhSachR[i];
        long long soKhach = danhSachSoKhach[i];

        mangHieu[l] = mangHieu[l] + soKhach;
        mangHieu[r + 1] = mangHieu[r + 1] - soKhach;
    }

    long long taiTrongLonNhat = 0;
    long long giaTriChayDong = 0;
    for (int i = 0; i <= n; i++) {
        giaTriChayDong = giaTriChayDong + mangHieu[i];
        if (giaTriChayDong > taiTrongLonNhat) {
            taiTrongLonNhat = giaTriChayDong;
        }
    }

    cout << "Tai trong lon nhat: " << taiTrongLonNhat << endl;

    return 0;
}
```
```python
n = 10

danh_sach_l = [0, 1, 2]
danh_sach_r = [2, 3, 5]
danh_sach_so_khach = [10, 20, 25]

mang_hieu = [0] * (n + 2)

for i in range(len(danh_sach_l)):
    l = danh_sach_l[i]
    r = danh_sach_r[i]
    so_khach = danh_sach_so_khach[i]

    mang_hieu[l] = mang_hieu[l] + so_khach
    mang_hieu[r + 1] = mang_hieu[r + 1] - so_khach

tai_trong_lon_nhat = 0
gia_tri_chay_dong = 0
for i in range(n + 1):
    gia_tri_chay_dong = gia_tri_chay_dong + mang_hieu[i]
    if gia_tri_chay_dong > tai_trong_lon_nhat:
        tai_trong_lon_nhat = gia_tri_chay_dong

print("Tai trong lon nhat:", tai_trong_lon_nhat)
```

### Ví dụ 3 (Khó) — Kết hợp mảng hiệu với thao tác kiểm tra không âm

Bài toán: cho n hồ nước, mỗi hồ ban đầu có một lượng nước cho trước. Có q lần tưới tiêu, mỗi lần lấy đi một lượng nước từ tất cả các hồ trong đoạn `[l, r]`. Sau mỗi lần tưới, hãy kiểm tra xem có hồ nào bị âm nước hay không (nếu có, in ra "CO", ngược lại in ra "KHONG").

**Phân tích:** Bài này khó ở chỗ ta cần biết trạng thái mảng **sau mỗi thao tác**, chứ không chỉ ở cuối cùng — nên không thể chỉ dựng lại mảng 1 lần ở cuối như các ví dụ trước. Với những bài toán dạng này (cần biết trạng thái tại nhiều thời điểm trung gian), mảng hiệu đơn thuần không đủ — thường phải chuyển sang dùng cấu trúc dữ liệu nâng cao hơn như Segment Tree (sẽ học ở Chương 20). Ví dụ này minh hoạ ranh giới của kỹ thuật mảng hiệu: nó rất mạnh khi chỉ cần biết trạng thái **cuối cùng**, nhưng không phù hợp khi cần biết trạng thái tại **nhiều thời điểm khác nhau xen kẽ với truy vấn**.

**Bài tập minh hoạ:** Cho một mảng n số nguyên ban đầu toàn số 0. Có q thao tác "cộng giá trị val vào đoạn `[l, r]`". Sau khi thực hiện xong toàn bộ q thao tác, hãy in ra giá trị lớn nhất trong mảng cuối cùng.

**Lời giải:** Dùng mảng hiệu để thực hiện tất cả các thao tác cộng đoạn trong O(1) mỗi thao tác, sau đó dựng lại mảng và tìm giá trị lớn nhất trong O(n).

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, soThaoTac;
    cin >> n >> soThaoTac;

    vector<long long> mangHieu(n + 1, 0);

    for (int i = 0; i < soThaoTac; i++) {
        int l, r;
        long long val;
        cin >> l >> r >> val;

        mangHieu[l] = mangHieu[l] + val;
        mangHieu[r + 1] = mangHieu[r + 1] - val;
    }

    long long giaTriLonNhat = LLONG_MIN;
    long long giaTriChayDong = 0;
    for (int i = 0; i < n; i++) {
        giaTriChayDong = giaTriChayDong + mangHieu[i];
        if (giaTriChayDong > giaTriLonNhat) {
            giaTriLonNhat = giaTriChayDong;
        }
    }

    cout << giaTriLonNhat << endl;

    return 0;
}
```
```python
n, so_thao_tac = map(int, input().split())

mang_hieu = [0] * (n + 1)

for i in range(so_thao_tac):
    l, r, val = map(int, input().split())

    mang_hieu[l] = mang_hieu[l] + val
    mang_hieu[r + 1] = mang_hieu[r + 1] - val

gia_tri_lon_nhat = float('-inf')
gia_tri_chay_dong = 0
for i in range(n):
    gia_tri_chay_dong = gia_tri_chay_dong + mang_hieu[i]
    if gia_tri_chay_dong > gia_tri_lon_nhat:
        gia_tri_lon_nhat = gia_tri_chay_dong

print(gia_tri_lon_nhat)
```

---

## 5.3. Mảng cộng dồn 2 chiều (Prefix Sum 2D)

**Nêu bài toán:** Cho một ma trận (lưới) `luoi` kích thước n dòng, m cột. Có q truy vấn, mỗi truy vấn hỏi: tổng các phần tử trong một hình chữ nhật con của ma trận (xác định bởi góc trên-trái và góc dưới-phải) là bao nhiêu?

**Phân tích vấn đề:** Đây là mở rộng 2 chiều của bài toán mảng cộng dồn 1 chiều. Nếu duyệt trực tiếp qua từng ô trong hình chữ nhật cho mỗi truy vấn, với hình chữ nhật có thể có kích thước tới n×m, và q truy vấn, tổng độ phức tạp có thể lên tới O(q × n × m) — rất chậm.

**Giải pháp đơn thuần:** Với mỗi truy vấn, duyệt qua tất cả các ô trong hình chữ nhật được hỏi, cộng dồn giá trị.

**Khó khăn với giải pháp đơn thuần:** Nếu n = m = 1000 và q = 100.000, độ phức tạp O(q × n × m) có thể lên tới 10^11 — hoàn toàn không khả thi.

**Cách tiếp cận mới:** Xây dựng một ma trận cộng dồn 2 chiều, trong đó `mangCongDon2D[i][j]` là tổng của toàn bộ hình chữ nhật từ góc (0, 0) đến (i-1, j-1). Sau đó, tổng của bất kỳ hình chữ nhật con nào cũng có thể tính được bằng công thức dựa trên **nguyên lý bù trừ** (cộng - trừ - trừ + cộng).

### Minh hoạ lời giải chi tiết

Công thức xây dựng ma trận cộng dồn 2D:
```
mangCongDon2D[i][j] = luoi[i-1][j-1] + mangCongDon2D[i-1][j] + mangCongDon2D[i][j-1] - mangCongDon2D[i-1][j-1]
```

Ta cộng ô hiện tại, cộng tổng phần phía trên, cộng tổng phần bên trái, nhưng phải trừ đi phần **giao nhau** giữa "phía trên" và "bên trái" (vì phần đó đã bị cộng 2 lần).

Công thức tính tổng hình chữ nhật từ `(r1, c1)` đến `(r2, c2)` (0-indexed, cả 2 đầu):
```
tongHinhChuNhat = mangCongDon2D[r2+1][c2+1] - mangCongDon2D[r1][c2+1] - mangCongDon2D[r2+1][c1] + mangCongDon2D[r1][c1]
```

Đây cũng là nguyên lý bù trừ: lấy hình chữ nhật lớn từ góc (0,0) đến (r2,c2), trừ đi phần phía trên đoạn cần tính, trừ đi phần bên trái đoạn cần tính, rồi cộng lại phần giao nhau đã bị trừ 2 lần.

### Code khung mẫu

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 3, m = 3;
    vector<vector<int>> luoi = {
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9}
    };

    vector<vector<long long>> mangCongDon2D(n + 1, vector<long long>(m + 1, 0));

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            mangCongDon2D[i+1][j+1] = luoi[i][j]
                                      + mangCongDon2D[i][j+1]
                                      + mangCongDon2D[i+1][j]
                                      - mangCongDon2D[i][j];
        }
    }

    int r1 = 1, c1 = 1, r2 = 2, c2 = 2;
    long long tongHinhChuNhat = mangCongDon2D[r2+1][c2+1]
                                - mangCongDon2D[r1][c2+1]
                                - mangCongDon2D[r2+1][c1]
                                + mangCongDon2D[r1][c1];

    cout << "Tong hinh chu nhat: " << tongHinhChuNhat << endl; // 5+6+8+9 = 28

    return 0;
}
```
```python
n, m = 3, 3
luoi = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
]

mang_cong_don_2d = []
for i in range(n + 1):
    dong_moi = [0] * (m + 1)
    mang_cong_don_2d.append(dong_moi)

for i in range(n):
    for j in range(m):
        mang_cong_don_2d[i+1][j+1] = (luoi[i][j]
                                       + mang_cong_don_2d[i][j+1]
                                       + mang_cong_don_2d[i+1][j]
                                       - mang_cong_don_2d[i][j])

r1, c1, r2, c2 = 1, 1, 2, 2
tong_hinh_chu_nhat = (mang_cong_don_2d[r2+1][c2+1]
                       - mang_cong_don_2d[r1][c2+1]
                       - mang_cong_don_2d[r2+1][c1]
                       + mang_cong_don_2d[r1][c1])

print("Tong hinh chu nhat:", tong_hinh_chu_nhat)  # 5+6+8+9 = 28
```

### Ví dụ 1 (Dễ) — Trả lời nhiều truy vấn tổng hình chữ nhật

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, m;
    cin >> n >> m;

    vector<vector<int>> luoi(n, vector<int>(m));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> luoi[i][j];
        }
    }

    vector<vector<long long>> mangCongDon2D(n + 1, vector<long long>(m + 1, 0));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            mangCongDon2D[i+1][j+1] = luoi[i][j]
                                      + mangCongDon2D[i][j+1]
                                      + mangCongDon2D[i+1][j]
                                      - mangCongDon2D[i][j];
        }
    }

    int soTruyVan;
    cin >> soTruyVan;

    for (int q = 0; q < soTruyVan; q++) {
        int r1, c1, r2, c2;
        cin >> r1 >> c1 >> r2 >> c2;

        long long ketQua = mangCongDon2D[r2+1][c2+1]
                          - mangCongDon2D[r1][c2+1]
                          - mangCongDon2D[r2+1][c1]
                          + mangCongDon2D[r1][c1];

        cout << ketQua << endl;
    }

    return 0;
}
```
```python
n, m = map(int, input().split())

luoi = []
for i in range(n):
    dong = list(map(int, input().split()))
    luoi.append(dong)

mang_cong_don_2d = []
for i in range(n + 1):
    dong_moi = [0] * (m + 1)
    mang_cong_don_2d.append(dong_moi)

for i in range(n):
    for j in range(m):
        mang_cong_don_2d[i+1][j+1] = (luoi[i][j]
                                       + mang_cong_don_2d[i][j+1]
                                       + mang_cong_don_2d[i+1][j]
                                       - mang_cong_don_2d[i][j])

so_truy_van = int(input())

for q in range(so_truy_van):
    r1, c1, r2, c2 = map(int, input().split())

    ket_qua = (mang_cong_don_2d[r2+1][c2+1]
               - mang_cong_don_2d[r1][c2+1]
               - mang_cong_don_2d[r2+1][c1]
               + mang_cong_don_2d[r1][c1])

    print(ket_qua)
```

### Ví dụ 2 (Trung bình) — Đếm số ô có giá trị đặc biệt trong hình chữ nhật

Bài toán: cho một lưới ký tự gồm chữ 'X' và chữ '.'. Có q truy vấn, mỗi truy vấn hỏi "trong hình chữ nhật này, có bao nhiêu ô chữ 'X'?".

**Phân tích:** Giống Ví dụ 2 của mục 5.1, ta xây dựng một lưới phụ `b[i][j] = 1` nếu ô đó là 'X', ngược lại `b[i][j] = 0`, rồi áp dụng mảng cộng dồn 2D lên lưới phụ này.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 3, m = 4;
    vector<string> luoi = {
        "X.X.",
        "..XX",
        "X..."
    };

    vector<vector<long long>> mangCongDon2D(n + 1, vector<long long>(m + 1, 0));

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            int giaTri = 0;
            if (luoi[i][j] == 'X') {
                giaTri = 1;
            }

            mangCongDon2D[i+1][j+1] = giaTri
                                      + mangCongDon2D[i][j+1]
                                      + mangCongDon2D[i+1][j]
                                      - mangCongDon2D[i][j];
        }
    }

    int r1 = 0, c1 = 0, r2 = 1, c2 = 3;
    long long soLuongX = mangCongDon2D[r2+1][c2+1]
                        - mangCongDon2D[r1][c2+1]
                        - mangCongDon2D[r2+1][c1]
                        + mangCongDon2D[r1][c1];

    cout << "So luong X: " << soLuongX << endl;

    return 0;
}
```
```python
n, m = 3, 4
luoi = ["X.X.", "..XX", "X..."]

mang_cong_don_2d = []
for i in range(n + 1):
    dong_moi = [0] * (m + 1)
    mang_cong_don_2d.append(dong_moi)

for i in range(n):
    for j in range(m):
        gia_tri = 0
        if luoi[i][j] == 'X':
            gia_tri = 1

        mang_cong_don_2d[i+1][j+1] = (gia_tri
                                       + mang_cong_don_2d[i][j+1]
                                       + mang_cong_don_2d[i+1][j]
                                       - mang_cong_don_2d[i][j])

r1, c1, r2, c2 = 0, 0, 1, 3
so_luong_x = (mang_cong_don_2d[r2+1][c2+1]
              - mang_cong_don_2d[r1][c2+1]
              - mang_cong_don_2d[r2+1][c1]
              + mang_cong_don_2d[r1][c1])

print("So luong X:", so_luong_x)
```

### Ví dụ 3 (Khó) — Mảng hiệu 2 chiều

Bài toán: cho một lưới n dòng, m cột, ban đầu toàn số 0. Có q thao tác, mỗi thao tác "cộng giá trị val vào toàn bộ hình chữ nhật từ `(r1, c1)` đến `(r2, c2)`". Sau khi thực hiện xong tất cả thao tác, hãy in ra lưới cuối cùng.

**Phân tích:** Đây là mở rộng 2 chiều của mảng hiệu 1D (mục 5.2). Với hình chữ nhật, ta cần đánh dấu **4 điểm góc** (thay vì 2 điểm như trường hợp 1D) để khi cộng dồn 2 chiều, hiệu ứng "+val" chỉ lan toả đúng trong phạm vi hình chữ nhật.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 5, m = 5;
    vector<vector<long long>> mangHieu2D(n + 2, vector<long long>(m + 2, 0));

    // Thao tác: cộng 3 vào hình chữ nhật từ (1,1) đến (3,3)
    int r1 = 1, c1 = 1, r2 = 3, c2 = 3;
    long long val = 3;

    mangHieu2D[r1][c1] = mangHieu2D[r1][c1] + val;
    mangHieu2D[r1][c2+1] = mangHieu2D[r1][c2+1] - val;
    mangHieu2D[r2+1][c1] = mangHieu2D[r2+1][c1] - val;
    mangHieu2D[r2+1][c2+1] = mangHieu2D[r2+1][c2+1] + val;

    // Dựng lại lưới gốc bằng cách cộng dồn 2 chiều
    vector<vector<long long>> ketQua(n, vector<long long>(m, 0));

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            long long giaTriTaiO = mangHieu2D[i][j];

            if (i > 0) {
                giaTriTaiO = giaTriTaiO + ketQua[i-1][j];
            }
            if (j > 0) {
                giaTriTaiO = giaTriTaiO + ketQua[i][j-1];
            }
            if (i > 0 && j > 0) {
                giaTriTaiO = giaTriTaiO - ketQua[i-1][j-1];
            }

            ketQua[i][j] = giaTriTaiO;
        }
    }

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cout << ketQua[i][j] << " ";
        }
        cout << endl;
    }

    return 0;
}
```
```python
n, m = 5, 5

mang_hieu_2d = []
for i in range(n + 2):
    dong_moi = [0] * (m + 2)
    mang_hieu_2d.append(dong_moi)

# Thao tác: cộng 3 vào hình chữ nhật từ (1,1) đến (3,3)
r1, c1, r2, c2 = 1, 1, 3, 3
val = 3

mang_hieu_2d[r1][c1] = mang_hieu_2d[r1][c1] + val
mang_hieu_2d[r1][c2+1] = mang_hieu_2d[r1][c2+1] - val
mang_hieu_2d[r2+1][c1] = mang_hieu_2d[r2+1][c1] - val
mang_hieu_2d[r2+1][c2+1] = mang_hieu_2d[r2+1][c2+1] + val

ket_qua = []
for i in range(n):
    dong_moi = [0] * m
    ket_qua.append(dong_moi)

for i in range(n):
    for j in range(m):
        gia_tri_tai_o = mang_hieu_2d[i][j]

        if i > 0:
            gia_tri_tai_o = gia_tri_tai_o + ket_qua[i-1][j]
        if j > 0:
            gia_tri_tai_o = gia_tri_tai_o + ket_qua[i][j-1]
        if i > 0 and j > 0:
            gia_tri_tai_o = gia_tri_tai_o - ket_qua[i-1][j-1]

        ket_qua[i][j] = gia_tri_tai_o

for i in range(n):
    for j in range(m):
        print(ket_qua[i][j], end=" ")
    print()
```

**Bài tập minh hoạ:** Cho một lưới n dòng, m cột chứa số 0 và số 1 (0 nghĩa là ô trống, 1 nghĩa là ô có chướng ngại vật). Có q truy vấn, mỗi truy vấn hỏi "trong hình chữ nhật này, có chướng ngại vật nào không?" (trả lời CO hoặc KHONG).

**Lời giải:** Dùng mảng cộng dồn 2D để tính tổng số lượng chướng ngại vật trong hình chữ nhật; nếu tổng lớn hơn 0, nghĩa là có ít nhất 1 chướng ngại vật.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, m;
    cin >> n >> m;

    vector<vector<int>> luoi(n, vector<int>(m));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> luoi[i][j];
        }
    }

    vector<vector<long long>> mangCongDon2D(n + 1, vector<long long>(m + 1, 0));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            mangCongDon2D[i+1][j+1] = luoi[i][j]
                                      + mangCongDon2D[i][j+1]
                                      + mangCongDon2D[i+1][j]
                                      - mangCongDon2D[i][j];
        }
    }

    int soTruyVan;
    cin >> soTruyVan;

    for (int q = 0; q < soTruyVan; q++) {
        int r1, c1, r2, c2;
        cin >> r1 >> c1 >> r2 >> c2;

        long long tongChuongNgaiVat = mangCongDon2D[r2+1][c2+1]
                                      - mangCongDon2D[r1][c2+1]
                                      - mangCongDon2D[r2+1][c1]
                                      + mangCongDon2D[r1][c1];

        if (tongChuongNgaiVat > 0) {
            cout << "CO" << endl;
        } else {
            cout << "KHONG" << endl;
        }
    }

    return 0;
}
```
```python
n, m = map(int, input().split())

luoi = []
for i in range(n):
    dong = list(map(int, input().split()))
    luoi.append(dong)

mang_cong_don_2d = []
for i in range(n + 1):
    dong_moi = [0] * (m + 1)
    mang_cong_don_2d.append(dong_moi)

for i in range(n):
    for j in range(m):
        mang_cong_don_2d[i+1][j+1] = (luoi[i][j]
                                       + mang_cong_don_2d[i][j+1]
                                       + mang_cong_don_2d[i+1][j]
                                       - mang_cong_don_2d[i][j])

so_truy_van = int(input())

for q in range(so_truy_van):
    r1, c1, r2, c2 = map(int, input().split())

    tong_chuong_ngai_vat = (mang_cong_don_2d[r2+1][c2+1]
                             - mang_cong_don_2d[r1][c2+1]
                             - mang_cong_don_2d[r2+1][c1]
                             + mang_cong_don_2d[r1][c1])

    if tong_chuong_ngai_vat > 0:
        print("CO")
    else:
        print("KHONG")
```

---

## 5.4. Mảng cộng dồn XOR và các biến thể

**Nêu bài toán:** Cho một mảng `a` gồm n số nguyên. Có q truy vấn, mỗi truy vấn hỏi: giá trị XOR của tất cả các phần tử từ vị trí `l` đến vị trí `r` là bao nhiêu?

**Phân tích vấn đề:** Phép toán XOR (ký hiệu `^`) có một tính chất đặc biệt: nếu XOR một giá trị với chính nó 2 lần, kết quả sẽ trở về giá trị ban đầu (vì `x ^ x = 0`, và `x ^ 0 = x`). Tính chất này cho phép ta áp dụng đúng ý tưởng của mảng cộng dồn — nhưng thay vì dùng phép cộng và phép trừ, ta dùng phép XOR.

**Giải pháp đơn thuần:** Với mỗi truy vấn, duyệt từ `l` đến `r`, XOR dồn các giá trị lại. Với q truy vấn, độ phức tạp là O(q × n) — gặp đúng vấn đề tương tự bài toán tổng đoạn ở mục 5.1.

**Cách tiếp cận mới:** Xây dựng mảng cộng dồn XOR, với `mangXOR[i]` là kết quả XOR của tất cả các phần tử từ vị trí 0 đến vị trí `i-1`. Khi đó, XOR của đoạn `[l, r]` bằng `mangXOR[r+1] ^ mangXOR[l]` — vì phần XOR của đoạn `[0, l-1]` sẽ tự triệt tiêu khi XOR 2 lần.

### Minh hoạ lời giải chi tiết

Ví dụ mảng `a = [3, 5, 2, 8, 1]`. Ta xây dựng `mangXOR` với `mangXOR[0] = 0` và `mangXOR[i] = mangXOR[i-1] ^ a[i-1]`:

| i | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| mangXOR[i] | 0 | 3 | 6 | 4 | 12 | 13 |

Muốn tính XOR của đoạn `[1, 3]` (các phần tử `5, 2, 8`), ta tính `mangXOR[4] ^ mangXOR[1] = 12 ^ 3 = 15`. Kiểm tra lại bằng cách XOR trực tiếp: `5 ^ 2 ^ 8 = 15` — kết quả khớp.

**Vì sao công thức này đúng?** `mangXOR[4]` là kết quả XOR của các phần tử `a[0], a[1], a[2], a[3]`. `mangXOR[1]` là kết quả XOR của riêng `a[0]`. Khi ta XOR 2 giá trị này với nhau, phần `a[0]` xuất hiện 2 lần nên tự triệt tiêu (theo tính chất `x ^ x = 0`), chỉ còn lại XOR của `a[1], a[2], a[3]` — đúng là đoạn `[1, 3]` ta cần.

### Code khung mẫu

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {3, 5, 2, 8, 1};
    int n = (int)a.size();

    vector<int> mangXOR(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangXOR[i + 1] = mangXOR[i] ^ a[i];
    }

    int l = 1, r = 3;
    int ketQuaXOR = mangXOR[r + 1] ^ mangXOR[l];

    cout << "XOR cua doan [" << l << ", " << r << "]: " << ketQuaXOR << endl;

    return 0;
}
```
```python
a = [3, 5, 2, 8, 1]
n = len(a)

mang_xor = [0] * (n + 1)
for i in range(n):
    mang_xor[i + 1] = mang_xor[i] ^ a[i]

l = 1
r = 3
ket_qua_xor = mang_xor[r + 1] ^ mang_xor[l]

print("XOR cua doan [", l, ",", r, "]:", ket_qua_xor)
```

### Ví dụ 1 (Dễ) — Trả lời nhiều truy vấn XOR đoạn

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;

    vector<int> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    vector<int> mangXOR(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangXOR[i + 1] = mangXOR[i] ^ a[i];
    }

    int soTruyVan;
    cin >> soTruyVan;

    for (int i = 0; i < soTruyVan; i++) {
        int l, r;
        cin >> l >> r;
        int ketQua = mangXOR[r + 1] ^ mangXOR[l];
        cout << ketQua << endl;
    }

    return 0;
}
```
```python
n = int(input())
a = list(map(int, input().split()))

mang_xor = [0] * (n + 1)
for i in range(n):
    mang_xor[i + 1] = mang_xor[i] ^ a[i]

so_truy_van = int(input())

for i in range(so_truy_van):
    l, r = map(int, input().split())
    ket_qua = mang_xor[r + 1] ^ mang_xor[l]
    print(ket_qua)
```

### Ví dụ 2 (Trung bình) — Đếm số đoạn con có XOR bằng 0

Bài toán: cho mảng n số nguyên, đếm số lượng đoạn con liên tiếp `[l, r]` sao cho XOR của toàn bộ đoạn đó bằng 0.

**Phân tích:** XOR của đoạn `[l, r]` bằng 0 khi và chỉ khi `mangXOR[r+1] = mangXOR[l]` (vì `mangXOR[r+1] ^ mangXOR[l] = 0` tương đương với 2 giá trị đó bằng nhau). Vậy bài toán trở thành: đếm số cặp `(l, r+1)` sao cho `mangXOR` tại 2 vị trí đó bằng nhau. Ta có thể dùng bảng băm để đếm số lần xuất hiện của mỗi giá trị trong mảng `mangXOR`, rồi với mỗi giá trị xuất hiện `k` lần, số cặp tạo được là `k × (k-1) / 2`.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {4, 2, 2, 6, 4};
    int n = (int)a.size();

    vector<int> mangXOR(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangXOR[i + 1] = mangXOR[i] ^ a[i];
    }

    unordered_map<int, long long> demSoLanXuatHien;
    for (int i = 0; i <= n; i++) {
        demSoLanXuatHien[mangXOR[i]] = demSoLanXuatHien[mangXOR[i]] + 1;
    }

    long long tongSoDoan = 0;
    for (auto phanTu : demSoLanXuatHien) {
        long long k = phanTu.second;
        tongSoDoan = tongSoDoan + (k * (k - 1)) / 2;
    }

    cout << "So doan con co XOR bang 0: " << tongSoDoan << endl;

    return 0;
}
```
```python
a = [4, 2, 2, 6, 4]
n = len(a)

mang_xor = [0] * (n + 1)
for i in range(n):
    mang_xor[i + 1] = mang_xor[i] ^ a[i]

dem_so_lan_xuat_hien = {}
for i in range(n + 1):
    gia_tri = mang_xor[i]
    if gia_tri in dem_so_lan_xuat_hien:
        dem_so_lan_xuat_hien[gia_tri] = dem_so_lan_xuat_hien[gia_tri] + 1
    else:
        dem_so_lan_xuat_hien[gia_tri] = 1

tong_so_doan = 0
for gia_tri in dem_so_lan_xuat_hien:
    k = dem_so_lan_xuat_hien[gia_tri]
    tong_so_doan = tong_so_doan + (k * (k - 1)) // 2

print("So doan con co XOR bang 0:", tong_so_doan)
```

### Ví dụ 3 (Khó) — Kết hợp mảng cộng dồn XOR với đếm bit

Bài toán: cho mảng n số nguyên, đếm số lượng đoạn con `[l, r]` sao cho XOR của đoạn đó là một số **lẻ** (bit cuối cùng bằng 1).

**Phân tích:** XOR của đoạn `[l, r]` là số lẻ khi và chỉ khi `mangXOR[r+1]` và `mangXOR[l]` có **bit cuối khác nhau** (một số chẵn, một số lẻ). Ta tách các giá trị `mangXOR[i]` thành 2 nhóm theo bit cuối (chẵn/lẻ), rồi số cặp thoả điều kiện chính là tích số lượng phần tử của 2 nhóm này.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {3, 5, 2, 8, 1};
    int n = (int)a.size();

    vector<int> mangXOR(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangXOR[i + 1] = mangXOR[i] ^ a[i];
    }

    long long soLuongChan = 0;
    long long soLuongLe = 0;

    for (int i = 0; i <= n; i++) {
        if (mangXOR[i] % 2 == 0) {
            soLuongChan = soLuongChan + 1;
        } else {
            soLuongLe = soLuongLe + 1;
        }
    }

    long long soDoanCoXORLe = soLuongChan * soLuongLe;

    cout << "So doan con co XOR le: " << soDoanCoXORLe << endl;

    return 0;
}
```
```python
a = [3, 5, 2, 8, 1]
n = len(a)

mang_xor = [0] * (n + 1)
for i in range(n):
    mang_xor[i + 1] = mang_xor[i] ^ a[i]

so_luong_chan = 0
so_luong_le = 0

for i in range(n + 1):
    if mang_xor[i] % 2 == 0:
        so_luong_chan = so_luong_chan + 1
    else:
        so_luong_le = so_luong_le + 1

so_doan_co_xor_le = so_luong_chan * so_luong_le

print("So doan con co XOR le:", so_doan_co_xor_le)
```

**Bài tập minh hoạ:** Cho mảng n số nguyên, có q truy vấn, mỗi truy vấn hỏi "đoạn `[l, r]` có phải toàn bộ các phần tử đều giống nhau hay không?" (trả lời CO hoặc KHONG).

**Lời giải:** Nếu XOR của đoạn `[l, r]` với số lượng phần tử lẻ mà kết quả bằng chính giá trị đầu tiên, và tất cả phần tử bằng nhau thì tổng cũng phải bằng giá trị đó nhân với số lượng phần tử. Cách đơn giản và chắc chắn hơn: dùng mảng cộng dồn giá trị lớn nhất và nhỏ nhất của đoạn (sẽ học kỹ hơn ở Chương 20 - Sparse Table), nhưng với mục đích minh hoạ mảng cộng dồn, ta có thể dùng mảng cộng dồn tổng: nếu tổng đoạn `[l, r]` bằng `a[l] × (r - l + 1)`, và đề bài đảm bảo các số dương phân biệt hoặc bằng nhau, thì đoạn đó có phần tử giống nhau.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<long long> a = {5, 5, 5, 3, 5};
    int n = (int)a.size();

    vector<long long> mangCongDon(n + 1, 0);
    for (int i = 0; i < n; i++) {
        mangCongDon[i + 1] = mangCongDon[i] + a[i];
    }

    int l = 0, r = 2;
    long long tongDoan = mangCongDon[r + 1] - mangCongDon[l];
    long long soLuongPhanTu = r - l + 1;
    long long tongNeuDeuBang = a[l] * soLuongPhanTu;

    if (tongDoan == tongNeuDeuBang) {
        cout << "CO" << endl;
    } else {
        cout << "KHONG" << endl;
    }

    return 0;
}
```
```python
a = [5, 5, 5, 3, 5]
n = len(a)

mang_cong_don = [0] * (n + 1)
for i in range(n):
    mang_cong_don[i + 1] = mang_cong_don[i] + a[i]

l = 0
r = 2
tong_doan = mang_cong_don[r + 1] - mang_cong_don[l]
so_luong_phan_tu = r - l + 1
tong_neu_deu_bang = a[l] * so_luong_phan_tu

if tong_doan == tong_neu_deu_bang:
    print("CO")
else:
    print("KHONG")
```

---

## Tổng kết Chương 5

- **Mảng cộng dồn (Prefix Sum):** dùng khi cần trả lời nhiều truy vấn tổng đoạn trên một mảng **không thay đổi**, biến mỗi truy vấn từ O(n) xuống O(1) sau khi tốn O(n) để xây dựng ban đầu.
- **Mảng hiệu (Difference Array):** kỹ thuật đối ngẫu với mảng cộng dồn, dùng khi cần cộng một giá trị vào **cả một đoạn** nhiều lần, biến mỗi thao tác cập nhật từ O(n) xuống O(1).
- **Mảng cộng dồn 2 chiều:** mở rộng của mảng cộng dồn 1D cho bài toán truy vấn tổng trên hình chữ nhật, dựa vào nguyên lý bù trừ.
- **Mảng cộng dồn XOR:** áp dụng cùng ý tưởng mảng cộng dồn nhưng với phép XOR thay vì phép cộng, tận dụng tính chất `x ^ x = 0`.
- **Điều kiện quan trọng cần nhớ:** mảng cộng dồn chỉ hoạt động đúng khi mảng gốc **không thay đổi** trong suốt quá trình truy vấn. Nếu vừa cần cập nhật điểm vừa cần truy vấn đoạn xen kẽ nhau, phải chuyển sang dùng Fenwick Tree hoặc Segment Tree — sẽ học ở Chương 20.

---

## Bài tập cuối chương — Chương 5

### PHẦN A: ĐỀ BÀI (12 bài, không kèm lời giải)

**Bài 1 — Static Range Sum Queries (CSES 1646) — Dễ**
Cho một mảng n số nguyên, có q truy vấn, mỗi truy vấn hỏi tổng các phần tử từ vị trí `a` đến vị trí `b`.

**Bài 2 — Range Sum Query - Immutable (LeetCode 303) — Dễ**
Thiết kế một cấu trúc dữ liệu cho phép trả lời nhanh nhiều truy vấn tổng đoạn trên một mảng cố định, không thay đổi.

**Bài 3 — Forest Queries (CSES 1652) — Dễ-Trung bình**
Cho một lưới ký tự gồm '.' (ô trống) và '*' (ô có cây), có q truy vấn, mỗi truy vấn hỏi có bao nhiêu cây trong một hình chữ nhật con cho trước.

**Bài 4 — Corporate Flight Bookings (LeetCode 1109) — Trung bình**
Có n chuyến bay được đánh số từ 1 đến n. Có q lần đặt vé, mỗi lần đặt `soVe` vé cho tất cả các chuyến bay trong đoạn `[first, last]`. Hãy tìm số vé đã đặt cho mỗi chuyến bay sau khi thực hiện xong tất cả các lần đặt vé.

**Bài 5 — Car Pooling (LeetCode 1094) — Trung bình**
Một chiếc xe có sức chứa tối đa là `capacity` hành khách, thực hiện các chuyến đi, mỗi chuyến đón `soKhach` hành khách tại một điểm và trả khách tại một điểm khác. Hãy kiểm tra xem xe có bao giờ bị chở quá tải hay không.

**Bài 6 — Range Update Queries (CSES 1651) — Trung bình**
Cho một mảng n số nguyên, có 2 loại truy vấn: cộng một giá trị vào toàn bộ đoạn `[a, b]`, hoặc hỏi giá trị tại một vị trí cụ thể.

**Bài 7 — Subarray Sum Equals K (LeetCode 560) — Trung bình**
Cho mảng số nguyên (có thể có số âm) và số k, đếm số lượng đoạn con liên tiếp có tổng đúng bằng k. (Gợi ý: kết hợp mảng cộng dồn với bảng băm, tương tự cách làm ở Ví dụ 2, mục 5.4 nhưng dùng cho phép cộng thay vì XOR.)

**Bài 8 — Continuous Subarray Sum (LeetCode 523) — Trung bình-Khó**
Cho mảng số nguyên và số k, kiểm tra có tồn tại một đoạn con liên tiếp có độ dài ít nhất 2 phần tử, sao cho tổng của đoạn đó chia hết cho k hay không.

**Bài 9 — XOR Queries of a Subarray (LeetCode 1310) — Trung bình**
Cho mảng số nguyên và danh sách các truy vấn `[l, r]`, hãy trả về kết quả XOR của từng đoạn `[l, r]` được hỏi.

**Bài 10 — Increment Submatrices by One (LeetCode 2536) — Trung bình-Khó**
Cho một ma trận kích thước n × n ban đầu toàn số 0, có q thao tác, mỗi thao tác cộng 1 vào toàn bộ hình chữ nhật con cho trước. Hãy in ra ma trận cuối cùng sau khi thực hiện tất cả thao tác.

**Bài 11 — My Calendar III (LeetCode 732) — Khó**
Có q lần đặt lịch, mỗi lần đặt một khoảng thời gian `[start, end)`. Sau mỗi lần đặt, hãy trả về số lượng lịch hẹn chồng lấn nhiều nhất tại một thời điểm bất kỳ tính đến hiện tại. (Gợi ý: sử dụng ý tưởng mảng hiệu, nhưng cần xử lý theo thời gian thực từng lần một, có thể kết hợp `map` thay vì mảng cố định vì thời gian có thể rất lớn.)

**Bài 12 — Bài toán tổng hợp về hiệu số bằng k, mở rộng cho tổng đoạn (tự thiết kế, dạng thường gặp trong đề thi HSG) — Khó**
Cho mảng n số nguyên và số k, đếm số lượng đoạn con liên tiếp `[l, r]` sao cho tổng của đoạn đó **chia hết cho k**. (Gợi ý: sử dụng mảng cộng dồn kết hợp với việc lấy phần dư khi chia cho k, rồi đếm số lần xuất hiện của mỗi giá trị dư bằng bảng băm — kỹ thuật tương tự Ví dụ 2, mục 5.4.)

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Static Range Sum Queries</summary>

Giống hệt Ví dụ 1, mục 5.1 — xây dựng mảng cộng dồn 1 lần, mỗi truy vấn trả lời trong O(1).
</details>

<details>
<summary>Lời giải Bài 2 — Range Sum Query - Immutable</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

class NumArray {
private:
    vector<long long> mangCongDon;

public:
    NumArray(vector<int> nums) {
        int n = (int)nums.size();
        mangCongDon.assign(n + 1, 0);
        for (int i = 0; i < n; i++) {
            mangCongDon[i + 1] = mangCongDon[i] + nums[i];
        }
    }

    long long sumRange(int l, int r) {
        return mangCongDon[r + 1] - mangCongDon[l];
    }
};
```
```python
class NumArray:
    def __init__(self, nums):
        n = len(nums)
        self.mang_cong_don = [0] * (n + 1)
        for i in range(n):
            self.mang_cong_don[i + 1] = self.mang_cong_don[i] + nums[i]

    def sum_range(self, l, r):
        return self.mang_cong_don[r + 1] - self.mang_cong_don[l]
```
</details>

<details>
<summary>Lời giải Bài 3 — Forest Queries</summary>

Giống hệt Ví dụ 2, mục 5.3 — xây dựng mảng phụ đánh dấu 1 nếu ô có cây, rồi áp dụng mảng cộng dồn 2D.
</details>

<details>
<summary>Lời giải Bài 4 — Corporate Flight Bookings</summary>

Đây chính là ứng dụng trực tiếp của mảng hiệu 1D (mục 5.2).
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<long long> corpFlightBookings(vector<vector<int>> bookings, int n) {
    vector<long long> mangHieu(n + 1, 0);

    for (int i = 0; i < (int)bookings.size(); i++) {
        int first = bookings[i][0] - 1; // đổi về 0-indexed
        int last = bookings[i][1] - 1;
        long long soVe = bookings[i][2];

        mangHieu[first] = mangHieu[first] + soVe;
        mangHieu[last + 1] = mangHieu[last + 1] - soVe;
    }

    vector<long long> ketQua(n, 0);
    long long giaTriChayDong = 0;
    for (int i = 0; i < n; i++) {
        giaTriChayDong = giaTriChayDong + mangHieu[i];
        ketQua[i] = giaTriChayDong;
    }

    return ketQua;
}
```
```python
def corp_flight_bookings(bookings, n):
    mang_hieu = [0] * (n + 1)

    for booking in bookings:
        first = booking[0] - 1
        last = booking[1] - 1
        so_ve = booking[2]

        mang_hieu[first] = mang_hieu[first] + so_ve
        mang_hieu[last + 1] = mang_hieu[last + 1] - so_ve

    ket_qua = [0] * n
    gia_tri_chay_dong = 0
    for i in range(n):
        gia_tri_chay_dong = gia_tri_chay_dong + mang_hieu[i]
        ket_qua[i] = gia_tri_chay_dong

    return ket_qua
```
</details>

<details>
<summary>Lời giải Bài 5 — Car Pooling</summary>

Dùng mảng hiệu để cộng số khách vào đoạn `[diemDon, diemTra)`, sau đó kiểm tra xem có vị trí nào vượt quá `capacity` không.
```cpp
#include <bits/stdc++.h>
using namespace std;

bool carPooling(vector<vector<int>> trips, int capacity) {
    int viTriToiDa = 1001; // theo giới hạn đề bài gốc, vị trí không vượt quá 1000

    vector<long long> mangHieu(viTriToiDa + 1, 0);

    for (int i = 0; i < (int)trips.size(); i++) {
        int soKhach = trips[i][0];
        int diemDon = trips[i][1];
        int diemTra = trips[i][2];

        mangHieu[diemDon] = mangHieu[diemDon] + soKhach;
        mangHieu[diemTra] = mangHieu[diemTra] - soKhach;
    }

    long long giaTriChayDong = 0;
    for (int i = 0; i <= viTriToiDa; i++) {
        giaTriChayDong = giaTriChayDong + mangHieu[i];
        if (giaTriChayDong > capacity) {
            return false;
        }
    }

    return true;
}
```
```python
def car_pooling(trips, capacity):
    vi_tri_toi_da = 1001

    mang_hieu = [0] * (vi_tri_toi_da + 1)

    for trip in trips:
        so_khach = trip[0]
        diem_don = trip[1]
        diem_tra = trip[2]

        mang_hieu[diem_don] = mang_hieu[diem_don] + so_khach
        mang_hieu[diem_tra] = mang_hieu[diem_tra] - so_khach

    gia_tri_chay_dong = 0
    for i in range(vi_tri_toi_da + 1):
        gia_tri_chay_dong = gia_tri_chay_dong + mang_hieu[i]
        if gia_tri_chay_dong > capacity:
            return False

    return True
```
</details>

<details>
<summary>Lời giải Bài 6 — Range Update Queries</summary>

Giống hệt Ví dụ 1, mục 5.2 — dùng mảng hiệu cho thao tác cộng đoạn, sau đó dựng lại giá trị tại từng điểm để trả lời truy vấn.
</details>

<details>
<summary>Lời giải Bài 7 — Subarray Sum Equals K</summary>

Ý tưởng: tương tự Ví dụ 2, mục 5.4 (đếm đoạn XOR bằng 0), nhưng dùng phép cộng thay vì XOR. Đoạn `[l, r]` có tổng bằng k khi và chỉ khi `mangCongDon[r+1] - mangCongDon[l] = k`, tức là `mangCongDon[l] = mangCongDon[r+1] - k`. Ta duyệt qua mảng, với mỗi vị trí kiểm tra xem giá trị `mangCongDon[hienTai] - k` đã xuất hiện bao nhiêu lần trước đó.
```cpp
#include <bits/stdc++.h>
using namespace std;

int subarraySum(vector<int> nums, int k) {
    unordered_map<long long, int> demSoLanXuatHien;
    demSoLanXuatHien[0] = 1; // ứng với mangCongDon[0] = 0

    long long tongChayDong = 0;
    int soLuongDoan = 0;

    for (int i = 0; i < (int)nums.size(); i++) {
        tongChayDong = tongChayDong + nums[i];

        long long giaTriCanTim = tongChayDong - k;
        if (demSoLanXuatHien.count(giaTriCanTim) > 0) {
            soLuongDoan = soLuongDoan + demSoLanXuatHien[giaTriCanTim];
        }

        demSoLanXuatHien[tongChayDong] = demSoLanXuatHien[tongChayDong] + 1;
    }

    return soLuongDoan;
}
```
```python
def subarray_sum(nums, k):
    dem_so_lan_xuat_hien = {0: 1}

    tong_chay_dong = 0
    so_luong_doan = 0

    for so in nums:
        tong_chay_dong = tong_chay_dong + so

        gia_tri_can_tim = tong_chay_dong - k
        if gia_tri_can_tim in dem_so_lan_xuat_hien:
            so_luong_doan = so_luong_doan + dem_so_lan_xuat_hien[gia_tri_can_tim]

        if tong_chay_dong in dem_so_lan_xuat_hien:
            dem_so_lan_xuat_hien[tong_chay_dong] = dem_so_lan_xuat_hien[tong_chay_dong] + 1
        else:
            dem_so_lan_xuat_hien[tong_chay_dong] = 1

    return so_luong_doan
```
</details>

<details>
<summary>Lời giải Bài 8 — Continuous Subarray Sum</summary>

Ý tưởng: dùng mảng cộng dồn lấy phần dư khi chia cho k (tương tự Bài 12 dưới đây), lưu **vị trí xuất hiện sớm nhất** của mỗi giá trị dư; nếu gặp lại cùng giá trị dư ở vị trí cách xa ít nhất 2, nghĩa là tồn tại đoạn con thoả điều kiện.
```cpp
#include <bits/stdc++.h>
using namespace std;

bool checkSubarraySum(vector<int> nums, int k) {
    unordered_map<int, int> viTriXuatHienSomNhat;
    viTriXuatHienSomNhat[0] = -1; // ứng với mangCongDon[0] = 0, coi như ở vị trí -1

    long long tongChayDong = 0;

    for (int i = 0; i < (int)nums.size(); i++) {
        tongChayDong = tongChayDong + nums[i];
        int soDu = (int)(tongChayDong % k);
        if (soDu < 0) {
            soDu = soDu + k;
        }

        if (viTriXuatHienSomNhat.count(soDu) > 0) {
            int viTriTruoc = viTriXuatHienSomNhat[soDu];
            if (i - viTriTruoc >= 2) {
                return true;
            }
        } else {
            viTriXuatHienSomNhat[soDu] = i;
        }
    }

    return false;
}
```
```python
def check_subarray_sum(nums, k):
    vi_tri_xuat_hien_som_nhat = {0: -1}

    tong_chay_dong = 0

    for i in range(len(nums)):
        tong_chay_dong = tong_chay_dong + nums[i]
        so_du = tong_chay_dong % k

        if so_du in vi_tri_xuat_hien_som_nhat:
            vi_tri_truoc = vi_tri_xuat_hien_som_nhat[so_du]
            if i - vi_tri_truoc >= 2:
                return True
        else:
            vi_tri_xuat_hien_som_nhat[so_du] = i

    return False
```
</details>

<details>
<summary>Lời giải Bài 9 — XOR Queries of a Subarray</summary>

Giống hệt Ví dụ 1, mục 5.4 — xây dựng mảng cộng dồn XOR, mỗi truy vấn trả lời trong O(1).
</details>

<details>
<summary>Lời giải Bài 10 — Increment Submatrices by One</summary>

Giống hệt Ví dụ 3, mục 5.3 — dùng mảng hiệu 2 chiều cho mỗi thao tác cộng hình chữ nhật, sau đó dựng lại ma trận cuối cùng.
</details>

<details>
<summary>Lời giải Bài 11 — My Calendar III</summary>

Vì thời gian có thể rất lớn (không thể dùng mảng cố định), ta dùng `map<int, int>` để lưu các "sự kiện" thay đổi (giống ý tưởng mảng hiệu, nhưng lưu bằng bảng có thứ tự thay vì mảng cố định). Sau mỗi lần đặt lịch mới, ta thêm 2 sự kiện rồi quét qua `map` để tìm giá trị chồng lấn lớn nhất.
```cpp
#include <bits/stdc++.h>
using namespace std;

class MyCalendarThree {
private:
    map<int, int> soLuongThayDoi;

public:
    int book(int start, int end) {
        soLuongThayDoi[start] = soLuongThayDoi[start] + 1;
        soLuongThayDoi[end] = soLuongThayDoi[end] - 1;

        int soLuongChongLanLonNhat = 0;
        int giaTriChayDong = 0;

        for (auto phanTu : soLuongThayDoi) {
            giaTriChayDong = giaTriChayDong + phanTu.second;
            if (giaTriChayDong > soLuongChongLanLonNhat) {
                soLuongChongLanLonNhat = giaTriChayDong;
            }
        }

        return soLuongChongLanLonNhat;
    }
};
```
```python
class MyCalendarThree:
    def __init__(self):
        self.so_luong_thay_doi = {}

    def book(self, start, end):
        if start in self.so_luong_thay_doi:
            self.so_luong_thay_doi[start] = self.so_luong_thay_doi[start] + 1
        else:
            self.so_luong_thay_doi[start] = 1

        if end in self.so_luong_thay_doi:
            self.so_luong_thay_doi[end] = self.so_luong_thay_doi[end] - 1
        else:
            self.so_luong_thay_doi[end] = -1

        so_luong_chong_lan_lon_nhat = 0
        gia_tri_chay_dong = 0

        for vi_tri in sorted(self.so_luong_thay_doi.keys()):
            gia_tri_chay_dong = gia_tri_chay_dong + self.so_luong_thay_doi[vi_tri]
            if gia_tri_chay_dong > so_luong_chong_lan_lon_nhat:
                so_luong_chong_lan_lon_nhat = gia_tri_chay_dong

        return so_luong_chong_lan_lon_nhat
```
> **Ghi chú:** cách làm trên chạy đúng nhưng khá chậm nếu số lần đặt lịch lớn (vì quét lại toàn bộ `map` sau mỗi lần đặt). Cách tối ưu hơn dùng Segment Tree sẽ được học ở Chương 20.
</details>

<details>
<summary>Lời giải Bài 12 — Đếm đoạn con có tổng chia hết cho k</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

long long demDoanChiaHetChoK(vector<int> a, int k) {
    unordered_map<int, long long> demSoLanXuatHien;
    demSoLanXuatHien[0] = 1; // ứng với mangCongDon[0] = 0, phần dư là 0

    long long tongChayDong = 0;
    long long soLuongDoan = 0;

    for (int i = 0; i < (int)a.size(); i++) {
        tongChayDong = tongChayDong + a[i];

        int soDu = (int)(tongChayDong % k);
        if (soDu < 0) {
            soDu = soDu + k;
        }

        if (demSoLanXuatHien.count(soDu) > 0) {
            soLuongDoan = soLuongDoan + demSoLanXuatHien[soDu];
        }

        demSoLanXuatHien[soDu] = demSoLanXuatHien[soDu] + 1;
    }

    return soLuongDoan;
}

int main() {
    vector<int> a = {4, 5, 0, -2, -3, 1};
    int k = 5;
    cout << demDoanChiaHetChoK(a, k) << endl;
    return 0;
}
```
```python
def dem_doan_chia_het_cho_k(a, k):
    dem_so_lan_xuat_hien = {0: 1}

    tong_chay_dong = 0
    so_luong_doan = 0

    for so in a:
        tong_chay_dong = tong_chay_dong + so
        so_du = tong_chay_dong % k

        if so_du in dem_so_lan_xuat_hien:
            so_luong_doan = so_luong_doan + dem_so_lan_xuat_hien[so_du]

        if so_du in dem_so_lan_xuat_hien:
            dem_so_lan_xuat_hien[so_du] = dem_so_lan_xuat_hien[so_du] + 1
        else:
            dem_so_lan_xuat_hien[so_du] = 1

    return so_luong_doan


a = [4, 5, 0, -2, -3, 1]
k = 5
print(dem_doan_chia_het_cho_k(a, k))
```
</details>
