# Chương 6: Hai con trỏ & Cửa sổ trượt

---

## 6.1. Hai con trỏ (Two Pointers)

**Nêu bài toán:** Cho một mảng đã được sắp xếp tăng dần gồm n số nguyên, và một số S. Hãy tìm xem có tồn tại 2 phần tử trong mảng có tổng đúng bằng S hay không.

**Phân tích vấn đề:** Cách trực quan nhất là thử tất cả các cặp phần tử có thể có trong mảng. Nhưng vì mảng đã được sắp xếp, ta có thể tận dụng tính chất đó: nếu tổng của cặp đang xét quá lớn, ta biết cần giảm giá trị; nếu tổng quá nhỏ, ta biết cần tăng giá trị — không cần thử ngẫu nhiên từng cặp.

**Giải pháp đơn thuần:** Dùng 2 vòng lặp lồng nhau, thử tất cả các cặp `(i, j)` với `i < j`, kiểm tra tổng có bằng S hay không. Độ phức tạp là O(n²).

**Khó khăn với giải pháp đơn thuần:** Khi n lên tới 200.000, độ phức tạp O(n²) sẽ xấp xỉ 4×10^10 phép tính — vượt xa khả năng xử lý trong giới hạn thời gian cho phép.

**Cách tiếp cận mới:** Dùng 2 con trỏ, một con trỏ `trai` bắt đầu từ đầu mảng, một con trỏ `phai` bắt đầu từ cuối mảng. Nếu tổng hiện tại lớn hơn S, ta giảm con trỏ `phai` xuống (để giảm tổng). Nếu tổng hiện tại nhỏ hơn S, ta tăng con trỏ `trai` lên (để tăng tổng). Vì mỗi con trỏ chỉ di chuyển theo một chiều duy nhất trong suốt quá trình, tổng số bước di chuyển của cả 2 con trỏ không vượt quá `2 × n`, nên độ phức tạp chỉ còn O(n).

### Minh hoạ lời giải chi tiết

Cho mảng `a = [1, 3, 5, 7, 9, 11]` đã sắp xếp, cần tìm cặp có tổng bằng `S = 12`.

| Bước | trai | phai | a[trai] | a[phai] | Tổng | So sánh với S=12 |
|---|---|---|---|---|---|---|
| 1 | 0 | 5 | 1 | 11 | 12 | Bằng S -> tìm thấy! |

Nếu không tìm thấy ngay, ví dụ với `S = 14`:

| Bước | trai | phai | a[trai] | a[phai] | Tổng | Hành động |
|---|---|---|---|---|---|---|
| 1 | 0 | 5 | 1 | 11 | 12 | 12 < 14 -> tăng trai |
| 2 | 1 | 5 | 3 | 11 | 14 | Bằng S -> tìm thấy! |

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {1, 3, 5, 7, 9, 11};
    int S = 14;

    int conTroTrai = 0;
    int conTroPhai = (int)a.size() - 1;
    bool timThay = false;

    while (conTroTrai < conTroPhai) {
        int tong = a[conTroTrai] + a[conTroPhai];

        if (tong == S) {
            cout << "Tim thay cap: " << a[conTroTrai] << " va " << a[conTroPhai] << endl;
            timThay = true;
            break;
        } else if (tong < S) {
            conTroTrai = conTroTrai + 1;
        } else {
            conTroPhai = conTroPhai - 1;
        }
    }

    if (timThay == false) {
        cout << "Khong tim thay cap nao" << endl;
    }

    return 0;
}
```

#### PYTHON
```python
a = [1, 3, 5, 7, 9, 11]
S = 14

con_tro_trai = 0
con_tro_phai = len(a) - 1
tim_thay = False

while con_tro_trai < con_tro_phai:
    tong = a[con_tro_trai] + a[con_tro_phai]

    if tong == S:
        print("Tim thay cap:", a[con_tro_trai], "va", a[con_tro_phai])
        tim_thay = True
        break
    elif tong < S:
        con_tro_trai = con_tro_trai + 1
    else:
        con_tro_phai = con_tro_phai - 1

if tim_thay == False:
    print("Khong tim thay cap nao")
```

### Ví dụ 1 (Dễ) — Đảo ngược mảng bằng 2 con trỏ

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> daoNguocMang(vector<int> a) {
    int conTroTrai = 0;
    int conTroPhai = (int)a.size() - 1;

    while (conTroTrai < conTroPhai) {
        int tam = a[conTroTrai];
        a[conTroTrai] = a[conTroPhai];
        a[conTroPhai] = tam;

        conTroTrai = conTroTrai + 1;
        conTroPhai = conTroPhai - 1;
    }

    return a;
}

int main() {
    vector<int> a = {1, 2, 3, 4, 5};
    vector<int> ketQua = daoNguocMang(a);

    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def dao_nguoc_mang(a):
    con_tro_trai = 0
    con_tro_phai = len(a) - 1

    while con_tro_trai < con_tro_phai:
        tam = a[con_tro_trai]
        a[con_tro_trai] = a[con_tro_phai]
        a[con_tro_phai] = tam

        con_tro_trai = con_tro_trai + 1
        con_tro_phai = con_tro_phai - 1

    return a


a = [1, 2, 3, 4, 5]
ket_qua = dao_nguoc_mang(a)
print(ket_qua)
```

### Ví dụ 2 (Trung bình) — Gộp 2 mảng đã sắp xếp thành 1 mảng sắp xếp

Bài toán: cho 2 mảng đã sắp xếp tăng dần, hãy gộp chúng thành 1 mảng duy nhất, vẫn giữ thứ tự tăng dần.

**Phân tích:** Đây là một biến thể khác của 2 con trỏ — nhưng lần này 2 con trỏ đi trên **2 mảng khác nhau**, cùng tiến về phía trước, không phải đi từ 2 đầu của cùng 1 mảng.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> gopHaiMangDaSapXep(vector<int> mangA, vector<int> mangB) {
    vector<int> ketQua;
    int conTroA = 0;
    int conTroB = 0;

    while (conTroA < (int)mangA.size() && conTroB < (int)mangB.size()) {
        if (mangA[conTroA] <= mangB[conTroB]) {
            ketQua.push_back(mangA[conTroA]);
            conTroA = conTroA + 1;
        } else {
            ketQua.push_back(mangB[conTroB]);
            conTroB = conTroB + 1;
        }
    }

    // Thêm nốt phần còn lại của mangA nếu chưa duyệt hết
    while (conTroA < (int)mangA.size()) {
        ketQua.push_back(mangA[conTroA]);
        conTroA = conTroA + 1;
    }

    // Thêm nốt phần còn lại của mangB nếu chưa duyệt hết
    while (conTroB < (int)mangB.size()) {
        ketQua.push_back(mangB[conTroB]);
        conTroB = conTroB + 1;
    }

    return ketQua;
}

int main() {
    vector<int> mangA = {1, 3, 5, 7};
    vector<int> mangB = {2, 4, 6, 8, 10};

    vector<int> ketQua = gopHaiMangDaSapXep(mangA, mangB);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def gop_hai_mang_da_sap_xep(mang_a, mang_b):
    ket_qua = []
    con_tro_a = 0
    con_tro_b = 0

    while con_tro_a < len(mang_a) and con_tro_b < len(mang_b):
        if mang_a[con_tro_a] <= mang_b[con_tro_b]:
            ket_qua.append(mang_a[con_tro_a])
            con_tro_a = con_tro_a + 1
        else:
            ket_qua.append(mang_b[con_tro_b])
            con_tro_b = con_tro_b + 1

    while con_tro_a < len(mang_a):
        ket_qua.append(mang_a[con_tro_a])
        con_tro_a = con_tro_a + 1

    while con_tro_b < len(mang_b):
        ket_qua.append(mang_b[con_tro_b])
        con_tro_b = con_tro_b + 1

    return ket_qua


mang_a = [1, 3, 5, 7]
mang_b = [2, 4, 6, 8, 10]

ket_qua = gop_hai_mang_da_sap_xep(mang_a, mang_b)
print(ket_qua)
```

### Ví dụ 3 (Khó) — Đếm số bộ ba (i, j, k) có tổng bằng 0

Bài toán: cho một mảng số nguyên (có thể có số âm), hãy đếm số lượng bộ ba chỉ số phân biệt `(i, j, k)` sao cho `a[i] + a[j] + a[k] = 0`.

**Phân tích:** Nếu dùng 3 vòng lặp lồng nhau, độ phức tạp là O(n³) — quá chậm. Ta có thể giảm xuống O(n²) bằng cách: sắp xếp mảng trước, sau đó cố định phần tử đầu tiên `a[i]`, rồi dùng kỹ thuật 2 con trỏ để tìm cặp `(j, k)` sao cho `a[j] + a[k] = -a[i]` trong phần còn lại của mảng — chính là bài toán đã giải ở phần "Minh hoạ lời giải chi tiết" phía trên.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> timBoBaCoTongBangKhong(vector<int> a) {
    sort(a.begin(), a.end());
    vector<vector<int>> ketQua;
    int n = (int)a.size();

    for (int i = 0; i < n - 2; i++) {
        // Bỏ qua các giá trị trùng lặp ở vị trí i để tránh đếm bộ ba giống nhau nhiều lần
        if (i > 0 && a[i] == a[i - 1]) {
            continue;
        }

        int conTroTrai = i + 1;
        int conTroPhai = n - 1;
        int giaTriCanTim = -a[i];

        while (conTroTrai < conTroPhai) {
            int tong = a[conTroTrai] + a[conTroPhai];

            if (tong == giaTriCanTim) {
                ketQua.push_back({a[i], a[conTroTrai], a[conTroPhai]});
                conTroTrai = conTroTrai + 1;
                conTroPhai = conTroPhai - 1;

                // Bỏ qua các giá trị trùng lặp tiếp theo
                while (conTroTrai < conTroPhai && a[conTroTrai] == a[conTroTrai - 1]) {
                    conTroTrai = conTroTrai + 1;
                }
            } else if (tong < giaTriCanTim) {
                conTroTrai = conTroTrai + 1;
            } else {
                conTroPhai = conTroPhai - 1;
            }
        }
    }

    return ketQua;
}

int main() {
    vector<int> a = {-1, 0, 1, 2, -1, -4};
    vector<vector<int>> ketQua = timBoBaCoTongBangKhong(a);

    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i][0] << " " << ketQua[i][1] << " " << ketQua[i][2] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def tim_bo_ba_co_tong_bang_khong(a):
    a_da_sap_xep = sorted(a)
    ket_qua = []
    n = len(a_da_sap_xep)

    for i in range(n - 2):
        if i > 0 and a_da_sap_xep[i] == a_da_sap_xep[i - 1]:
            continue

        con_tro_trai = i + 1
        con_tro_phai = n - 1
        gia_tri_can_tim = -a_da_sap_xep[i]

        while con_tro_trai < con_tro_phai:
            tong = a_da_sap_xep[con_tro_trai] + a_da_sap_xep[con_tro_phai]

            if tong == gia_tri_can_tim:
                ket_qua.append([a_da_sap_xep[i], a_da_sap_xep[con_tro_trai], a_da_sap_xep[con_tro_phai]])
                con_tro_trai = con_tro_trai + 1
                con_tro_phai = con_tro_phai - 1

                while con_tro_trai < con_tro_phai and a_da_sap_xep[con_tro_trai] == a_da_sap_xep[con_tro_trai - 1]:
                    con_tro_trai = con_tro_trai + 1
            elif tong < gia_tri_can_tim:
                con_tro_trai = con_tro_trai + 1
            else:
                con_tro_phai = con_tro_phai - 1

    return ket_qua


a = [-1, 0, 1, 2, -1, -4]
ket_qua = tim_bo_ba_co_tong_bang_khong(a)
print(ket_qua)
```

**Bài tập minh hoạ:** Cho một mảng đã sắp xếp có thể có phần tử trùng lặp, hãy loại bỏ các phần tử trùng lặp ngay trên mảng đó (không dùng mảng phụ), sao cho mỗi giá trị chỉ xuất hiện đúng 1 lần, và trả về độ dài mới của mảng.

**Lời giải:** Dùng 2 con trỏ cùng tiến về một hướng: con trỏ `chậm` đánh dấu vị trí ghi phần tử tiếp theo, con trỏ `nhanh` duyệt qua toàn bộ mảng để tìm phần tử mới khác với phần tử vừa ghi.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int loaiBoTrungLap(vector<int> &a) {
    if (a.size() == 0) {
        return 0;
    }

    int conTroCham = 0;

    for (int conTroNhanh = 1; conTroNhanh < (int)a.size(); conTroNhanh++) {
        if (a[conTroNhanh] != a[conTroCham]) {
            conTroCham = conTroCham + 1;
            a[conTroCham] = a[conTroNhanh];
        }
    }

    return conTroCham + 1; // độ dài mới của mảng sau khi loại trùng
}

int main() {
    vector<int> a = {1, 1, 2, 2, 2, 3, 4, 4};
    int doDaiMoi = loaiBoTrungLap(a);

    for (int i = 0; i < doDaiMoi; i++) {
        cout << a[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def loai_bo_trung_lap(a):
    if len(a) == 0:
        return 0

    con_tro_cham = 0

    for con_tro_nhanh in range(1, len(a)):
        if a[con_tro_nhanh] != a[con_tro_cham]:
            con_tro_cham = con_tro_cham + 1
            a[con_tro_cham] = a[con_tro_nhanh]

    return con_tro_cham + 1


a = [1, 1, 2, 2, 2, 3, 4, 4]
do_dai_moi = loai_bo_trung_lap(a)
print(a[0:do_dai_moi])
```

---

## 6.2. Cửa sổ trượt kích thước cố định (Fixed-size Sliding Window)

**Nêu bài toán:** Cho một mảng n số nguyên và một số k, hãy tìm tổng lớn nhất trong số tất cả các đoạn con liên tiếp có độ dài đúng bằng k.

**Phân tích vấn đề:** Có tổng cộng `n - k + 1` đoạn con cần xét. Nếu tính tổng từng đoạn từ đầu, ta tốn O(k) cho mỗi đoạn. Nhưng nhận thấy: 2 đoạn con liên tiếp `[i, i+k-1]` và `[i+1, i+k]` chỉ khác nhau đúng 1 phần tử bị bỏ ra và 1 phần tử được thêm vào — phần ở giữa hoàn toàn giống nhau.

**Giải pháp đơn thuần:** Với mỗi đoạn con, tính lại tổng từ đầu. Độ phức tạp là O(n × k).

**Khó khăn với giải pháp đơn thuần:** Nếu k tỉ lệ với n (ví dụ cả 2 đều xấp xỉ 100.000), độ phức tạp O(n × k) sẽ xấp xỉ O(n²) — quá chậm.

**Cách tiếp cận mới:** Thay vì tính lại tổng từ đầu, ta duy trì tổng của "cửa sổ" hiện tại, và cập nhật bằng cách trừ đi phần tử vừa ra khỏi cửa sổ, cộng thêm phần tử vừa vào cửa sổ. Mỗi bước trượt chỉ tốn O(1), nên tổng độ phức tạp giảm xuống O(n).

### Minh hoạ lời giải chi tiết

Cho `a = [2, 1, 5, 1, 3, 2]`, `k = 3`:

| Bước | Cửa sổ | Thao tác | Tổng cửa sổ |
|---|---|---|---|
| Khởi tạo | [2, 1, 5] | Tính trực tiếp 2+1+5 | 8 |
| Trượt 1 | [1, 5, 1] | 8 - a[0] + a[3] = 8 - 2 + 1 | 7 |
| Trượt 2 | [5, 1, 3] | 7 - a[1] + a[4] = 7 - 1 + 3 | 9 |
| Trượt 3 | [1, 3, 2] | 9 - a[2] + a[5] = 9 - 5 + 2 | 6 |

Kết quả: tổng lớn nhất là 9.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timTongLonNhatCuaCuaSo(vector<int> a, int k) {
    int n = (int)a.size();

    long long tongCuaSo = 0;
    for (int i = 0; i < k; i++) {
        tongCuaSo = tongCuaSo + a[i];
    }

    long long tongLonNhat = tongCuaSo;

    for (int i = k; i < n; i++) {
        tongCuaSo = tongCuaSo + a[i] - a[i - k];
        if (tongCuaSo > tongLonNhat) {
            tongLonNhat = tongCuaSo;
        }
    }

    return tongLonNhat;
}

int main() {
    vector<int> a = {2, 1, 5, 1, 3, 2};
    int k = 3;

    cout << timTongLonNhatCuaCuaSo(a, k) << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_tong_lon_nhat_cua_cua_so(a, k):
    n = len(a)

    tong_cua_so = 0
    for i in range(k):
        tong_cua_so = tong_cua_so + a[i]

    tong_lon_nhat = tong_cua_so

    for i in range(k, n):
        tong_cua_so = tong_cua_so + a[i] - a[i - k]
        if tong_cua_so > tong_lon_nhat:
            tong_lon_nhat = tong_cua_so

    return tong_lon_nhat


a = [2, 1, 5, 1, 3, 2]
k = 3

print(tim_tong_lon_nhat_cua_cua_so(a, k))
```

### Ví dụ 1 (Dễ) — Trung bình cộng lớn nhất của k phần tử liên tiếp

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

double timTrungBinhLonNhat(vector<int> a, int k) {
    long long tongLonNhat = timTongLonNhatCuaCuaSo(a, k);
    return (double)tongLonNhat / (double)k;
}
```
> (Hàm `timTongLonNhatCuaCuaSo` đã định nghĩa ở phần Code khung mẫu phía trên.)

#### PYTHON
```python
def tim_trung_binh_lon_nhat(a, k):
    tong_lon_nhat = tim_tong_lon_nhat_cua_cua_so(a, k)
    return tong_lon_nhat / k
```

### Ví dụ 2 (Trung bình) — Đếm số lượng ký tự phân biệt trong mọi cửa sổ kích thước k

Bài toán: cho một chuỗi ký tự và số k, với mỗi cửa sổ con kích thước k, hãy in ra số lượng ký tự phân biệt trong cửa sổ đó.

**Phân tích:** Kết hợp cửa sổ trượt cố định với một bảng đếm tần suất — khi phần tử ra khỏi cửa sổ, giảm tần suất của nó; nếu tần suất về 0, giảm số lượng ký tự phân biệt. Khi phần tử mới vào cửa sổ, tăng tần suất; nếu đây là lần đầu xuất hiện, tăng số lượng ký tự phân biệt.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> demKyTuPhanBietMoiCuaSo(string s, int k) {
    map<char, int> tanSuat;
    vector<int> ketQua;

    for (int i = 0; i < k; i++) {
        tanSuat[s[i]] = tanSuat[s[i]] + 1;
    }
    ketQua.push_back((int)tanSuat.size());

    for (int i = k; i < (int)s.size(); i++) {
        char kyTuMoi = s[i];
        char kyTuCu = s[i - k];

        tanSuat[kyTuMoi] = tanSuat[kyTuMoi] + 1;

        tanSuat[kyTuCu] = tanSuat[kyTuCu] - 1;
        if (tanSuat[kyTuCu] == 0) {
            tanSuat.erase(kyTuCu);
        }

        ketQua.push_back((int)tanSuat.size());
    }

    return ketQua;
}

int main() {
    string s = "aabbccaa";
    int k = 3;

    vector<int> ketQua = demKyTuPhanBietMoiCuaSo(s, k);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def dem_ky_tu_phan_biet_moi_cua_so(s, k):
    tan_suat = {}
    ket_qua = []

    for i in range(k):
        ky_tu = s[i]
        if ky_tu in tan_suat:
            tan_suat[ky_tu] = tan_suat[ky_tu] + 1
        else:
            tan_suat[ky_tu] = 1
    ket_qua.append(len(tan_suat))

    for i in range(k, len(s)):
        ky_tu_moi = s[i]
        ky_tu_cu = s[i - k]

        if ky_tu_moi in tan_suat:
            tan_suat[ky_tu_moi] = tan_suat[ky_tu_moi] + 1
        else:
            tan_suat[ky_tu_moi] = 1

        tan_suat[ky_tu_cu] = tan_suat[ky_tu_cu] - 1
        if tan_suat[ky_tu_cu] == 0:
            del tan_suat[ky_tu_cu]

        ket_qua.append(len(tan_suat))

    return ket_qua


s = "aabbccaa"
k = 3

ket_qua = dem_ky_tu_phan_biet_moi_cua_so(s, k)
print(ket_qua)
```

### Ví dụ 3 (Khó) — Kết hợp cửa sổ cố định với việc kiểm tra dấu hiệu Anagram

Bài toán: cho 2 chuỗi `s` và `p`, hãy tìm tất cả các vị trí bắt đầu trong `s` mà tại đó, đoạn con có độ dài bằng `p` là một anagram (biến thể đảo chữ cái) của `p`.

**Phân tích:** Đây là ứng dụng nâng cao của cửa sổ cố định kết hợp bảng đếm tần suất — kích thước cửa sổ luôn bằng `độ dài của p`, và tại mỗi vị trí, ta so sánh bảng tần suất của cửa sổ hiện tại với bảng tần suất của `p`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> timViTriAnagram(string s, string p) {
    int doDaiP = (int)p.size();
    int doDaiS = (int)s.size();
    vector<int> ketQua;

    if (doDaiS < doDaiP) {
        return ketQua;
    }

    vector<int> tanSuatP(26, 0);
    vector<int> tanSuatCuaSo(26, 0);

    for (int i = 0; i < doDaiP; i++) {
        tanSuatP[p[i] - 'a'] = tanSuatP[p[i] - 'a'] + 1;
        tanSuatCuaSo[s[i] - 'a'] = tanSuatCuaSo[s[i] - 'a'] + 1;
    }

    if (tanSuatP == tanSuatCuaSo) {
        ketQua.push_back(0);
    }

    for (int i = doDaiP; i < doDaiS; i++) {
        char kyTuMoi = s[i];
        char kyTuCu = s[i - doDaiP];

        tanSuatCuaSo[kyTuMoi - 'a'] = tanSuatCuaSo[kyTuMoi - 'a'] + 1;
        tanSuatCuaSo[kyTuCu - 'a'] = tanSuatCuaSo[kyTuCu - 'a'] - 1;

        if (tanSuatP == tanSuatCuaSo) {
            int viTriBatDau = i - doDaiP + 1;
            ketQua.push_back(viTriBatDau);
        }
    }

    return ketQua;
}

int main() {
    string s = "cbaebabacd";
    string p = "abc";

    vector<int> ketQua = timViTriAnagram(s, p);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_vi_tri_anagram(s, p):
    do_dai_p = len(p)
    do_dai_s = len(s)
    ket_qua = []

    if do_dai_s < do_dai_p:
        return ket_qua

    tan_suat_p = [0] * 26
    tan_suat_cua_so = [0] * 26

    for i in range(do_dai_p):
        tan_suat_p[ord(p[i]) - ord('a')] = tan_suat_p[ord(p[i]) - ord('a')] + 1
        tan_suat_cua_so[ord(s[i]) - ord('a')] = tan_suat_cua_so[ord(s[i]) - ord('a')] + 1

    if tan_suat_p == tan_suat_cua_so:
        ket_qua.append(0)

    for i in range(do_dai_p, do_dai_s):
        ky_tu_moi = s[i]
        ky_tu_cu = s[i - do_dai_p]

        tan_suat_cua_so[ord(ky_tu_moi) - ord('a')] = tan_suat_cua_so[ord(ky_tu_moi) - ord('a')] + 1
        tan_suat_cua_so[ord(ky_tu_cu) - ord('a')] = tan_suat_cua_so[ord(ky_tu_cu) - ord('a')] - 1

        if tan_suat_p == tan_suat_cua_so:
            vi_tri_bat_dau = i - do_dai_p + 1
            ket_qua.append(vi_tri_bat_dau)

    return ket_qua


s = "cbaebabacd"
p = "abc"

ket_qua = tim_vi_tri_anagram(s, p)
print(ket_qua)
```

**Bài tập minh hoạ:** Cho mảng n số nguyên và số k, tìm giá trị **nhỏ nhất** trong số các tổng của mọi cửa sổ con kích thước k (bài đối xứng với bài "tìm tổng lớn nhất" đã giải ở phần đầu mục này).

**Lời giải:** Áp dụng đúng khung cửa sổ cố định, chỉ đổi điều kiện so sánh từ "lớn hơn" thành "nhỏ hơn".

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timTongNhoNhatCuaCuaSo(vector<int> a, int k) {
    int n = (int)a.size();

    long long tongCuaSo = 0;
    for (int i = 0; i < k; i++) {
        tongCuaSo = tongCuaSo + a[i];
    }

    long long tongNhoNhat = tongCuaSo;

    for (int i = k; i < n; i++) {
        tongCuaSo = tongCuaSo + a[i] - a[i - k];
        if (tongCuaSo < tongNhoNhat) {
            tongNhoNhat = tongCuaSo;
        }
    }

    return tongNhoNhat;
}
```

#### PYTHON
```python
def tim_tong_nho_nhat_cua_cua_so(a, k):
    n = len(a)

    tong_cua_so = 0
    for i in range(k):
        tong_cua_so = tong_cua_so + a[i]

    tong_nho_nhat = tong_cua_so

    for i in range(k, n):
        tong_cua_so = tong_cua_so + a[i] - a[i - k]
        if tong_cua_so < tong_nho_nhat:
            tong_nho_nhat = tong_cua_so

    return tong_nho_nhat
```

---

## 6.3. Cửa sổ trượt kích thước thay đổi (Variable-size Sliding Window)

**Nêu bài toán:** Cho một mảng n số nguyên **dương** và một số S, hãy tìm độ dài của đoạn con liên tiếp **ngắn nhất** sao cho tổng của đoạn đó lớn hơn hoặc bằng S.

**Phân tích vấn đề:** Khác với mục 6.2, ở đây kích thước cửa sổ không cố định trước — ta cần tìm ra kích thước phù hợp. Vì mảng toàn số dương, khi mở rộng cửa sổ (tăng con trỏ phải), tổng luôn tăng; khi co cửa sổ (tăng con trỏ trái), tổng luôn giảm — đây là tính chất đơn điệu quan trọng.

**Giải pháp đơn thuần:** Với mỗi vị trí bắt đầu, thử mở rộng dần về bên phải cho tới khi đạt tổng đủ lớn. Độ phức tạp O(n²) trong trường hợp xấu nhất.

**Khó khăn với giải pháp đơn thuần:** Với n lớn, O(n²) không khả thi.

**Cách tiếp cận mới:** Dùng 2 con trỏ `trai` và `phai`, cả 2 đều chỉ **tiến về phía trước, không bao giờ lùi lại**. Con trỏ `phai` luôn tiến để mở rộng cửa sổ; con trỏ `trai` chỉ tiến khi cửa sổ hiện tại đã đủ điều kiện (để thử co cửa sổ lại, tìm kích thước nhỏ hơn). Vì mỗi con trỏ chỉ đi tối đa n bước trong toàn bộ quá trình, tổng độ phức tạp là O(n).

### Minh hoạ lời giải chi tiết

Cho `a = [2, 3, 1, 2, 4, 3]`, `S = 7`:

| Bước | trai | phai | Cửa sổ | Tổng | Hành động |
|---|---|---|---|---|---|
| 1 | 0 | 0 | [2] | 2 | Chưa đủ, mở rộng phai |
| 2 | 0 | 1 | [2,3] | 5 | Chưa đủ, mở rộng phai |
| 3 | 0 | 2 | [2,3,1] | 6 | Chưa đủ, mở rộng phai |
| 4 | 0 | 3 | [2,3,1,2] | 8 | Đủ điều kiện! Độ dài 4. Thử co trai |
| 5 | 1 | 3 | [3,1,2] | 6 | Không đủ nữa, mở rộng phai |
| 6 | 1 | 4 | [3,1,2,4] | 10 | Đủ điều kiện! Độ dài 4. Thử co trai |
| 7 | 2 | 4 | [1,2,4] | 7 | Đủ điều kiện! Độ dài 3. Thử co trai |
| 8 | 3 | 4 | [2,4] | 6 | Không đủ nữa, mở rộng phai |
| 9 | 3 | 5 | [2,4,3] | 9 | Đủ điều kiện! Độ dài 3. Thử co trai |
| 10 | 4 | 5 | [4,3] | 7 | Đủ điều kiện! Độ dài 2. Thử co trai |
| 11 | 5 | 5 | [3] | 3 | Không đủ, mở rộng phai -> hết mảng |

Kết quả: độ dài ngắn nhất tìm được là 2 (đoạn `[4, 3]`).

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timDoDaiNganNhatCoTongLonHonBangS(vector<int> a, long long S) {
    int n = (int)a.size();
    int conTroTrai = 0;
    long long tongHienTai = 0;
    int doDaiNganNhat = n + 1; // ban đầu gán giá trị lớn hơn mọi độ dài có thể có

    for (int conTroPhai = 0; conTroPhai < n; conTroPhai++) {
        tongHienTai = tongHienTai + a[conTroPhai];

        while (tongHienTai >= S) {
            int doDaiHienTai = conTroPhai - conTroTrai + 1;
            if (doDaiHienTai < doDaiNganNhat) {
                doDaiNganNhat = doDaiHienTai;
            }

            tongHienTai = tongHienTai - a[conTroTrai];
            conTroTrai = conTroTrai + 1;
        }
    }

    if (doDaiNganNhat == n + 1) {
        return 0; // không tồn tại đoạn con nào thoả điều kiện
    }
    return doDaiNganNhat;
}

int main() {
    vector<int> a = {2, 3, 1, 2, 4, 3};
    long long S = 7;

    cout << timDoDaiNganNhatCoTongLonHonBangS(a, S) << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_do_dai_ngan_nhat_co_tong_lon_hon_bang_s(a, S):
    n = len(a)
    con_tro_trai = 0
    tong_hien_tai = 0
    do_dai_ngan_nhat = n + 1

    for con_tro_phai in range(n):
        tong_hien_tai = tong_hien_tai + a[con_tro_phai]

        while tong_hien_tai >= S:
            do_dai_hien_tai = con_tro_phai - con_tro_trai + 1
            if do_dai_hien_tai < do_dai_ngan_nhat:
                do_dai_ngan_nhat = do_dai_hien_tai

            tong_hien_tai = tong_hien_tai - a[con_tro_trai]
            con_tro_trai = con_tro_trai + 1

    if do_dai_ngan_nhat == n + 1:
        return 0
    return do_dai_ngan_nhat


a = [2, 3, 1, 2, 4, 3]
S = 7

print(tim_do_dai_ngan_nhat_co_tong_lon_hon_bang_s(a, S))
```

### Ví dụ 1 (Dễ) — Đoạn con dài nhất không có ký tự lặp lại

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int doDaiDoanKhongLapKyTu(string s) {
    unordered_set<char> cuaSo;
    int conTroTrai = 0;
    int doDaiLonNhat = 0;

    for (int conTroPhai = 0; conTroPhai < (int)s.size(); conTroPhai++) {
        while (cuaSo.count(s[conTroPhai]) > 0) {
            cuaSo.erase(s[conTroTrai]);
            conTroTrai = conTroTrai + 1;
        }

        cuaSo.insert(s[conTroPhai]);

        int doDaiHienTai = conTroPhai - conTroTrai + 1;
        if (doDaiHienTai > doDaiLonNhat) {
            doDaiLonNhat = doDaiHienTai;
        }
    }

    return doDaiLonNhat;
}

int main() {
    string s = "abcabcbb";
    cout << doDaiDoanKhongLapKyTu(s) << endl;
    return 0;
}
```

#### PYTHON
```python
def do_dai_doan_khong_lap_ky_tu(s):
    cua_so = set()
    con_tro_trai = 0
    do_dai_lon_nhat = 0

    for con_tro_phai in range(len(s)):
        while s[con_tro_phai] in cua_so:
            cua_so.discard(s[con_tro_trai])
            con_tro_trai = con_tro_trai + 1

        cua_so.add(s[con_tro_phai])

        do_dai_hien_tai = con_tro_phai - con_tro_trai + 1
        if do_dai_hien_tai > do_dai_lon_nhat:
            do_dai_lon_nhat = do_dai_hien_tai

    return do_dai_lon_nhat


s = "abcabcbb"
print(do_dai_doan_khong_lap_ky_tu(s))
```

### Ví dụ 2 (Trung bình) — Đoạn con dài nhất có tổng chia hết cho k

Bài toán: cho mảng n số nguyên và số k, tìm độ dài đoạn con liên tiếp **dài nhất** sao cho tổng của đoạn đó chia hết cho k.

**Phân tích:** Đây là bài toán **không thể** giải trực tiếp bằng cửa sổ trượt thuần tuý, vì tính chất "chia hết" không đơn điệu theo độ dài đoạn (mở rộng cửa sổ có thể làm tổng từ "chia hết" chuyển sang "không chia hết" rồi lại "chia hết"). Ta cần kết hợp với kỹ thuật mảng cộng dồn lấy phần dư (đã học ở Chương 5): `prefix[r] - prefix[l]` chia hết cho k khi và chỉ khi `prefix[r]` và `prefix[l]` có cùng phần dư khi chia cho k.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int doDaiDoanChiaHetChoK(vector<int> a, int k) {
    unordered_map<int, int> viTriXuatHienSomNhat;
    viTriXuatHienSomNhat[0] = 0; // phần dư 0 xuất hiện "ảo" tại vị trí 0

    long long tongChayDong = 0;
    int doDaiLonNhat = 0;

    for (int i = 0; i < (int)a.size(); i++) {
        tongChayDong = tongChayDong + a[i];

        int soDu = (int)(tongChayDong % k);
        if (soDu < 0) {
            soDu = soDu + k;
        }

        if (viTriXuatHienSomNhat.count(soDu) > 0) {
            int viTriTruoc = viTriXuatHienSomNhat[soDu];
            int doDaiHienTai = (i + 1) - viTriTruoc;
            if (doDaiHienTai > doDaiLonNhat) {
                doDaiLonNhat = doDaiHienTai;
            }
        } else {
            viTriXuatHienSomNhat[soDu] = i + 1;
        }
    }

    return doDaiLonNhat;
}

int main() {
    vector<int> a = {2, 6, 3, 5, 4, 1};
    int k = 4;

    cout << doDaiDoanChiaHetChoK(a, k) << endl;

    return 0;
}
```

#### PYTHON
```python
def do_dai_doan_chia_het_cho_k(a, k):
    vi_tri_xuat_hien_som_nhat = {0: 0}

    tong_chay_dong = 0
    do_dai_lon_nhat = 0

    for i in range(len(a)):
        tong_chay_dong = tong_chay_dong + a[i]
        so_du = tong_chay_dong % k

        if so_du in vi_tri_xuat_hien_som_nhat:
            vi_tri_truoc = vi_tri_xuat_hien_som_nhat[so_du]
            do_dai_hien_tai = (i + 1) - vi_tri_truoc
            if do_dai_hien_tai > do_dai_lon_nhat:
                do_dai_lon_nhat = do_dai_hien_tai
        else:
            vi_tri_xuat_hien_som_nhat[so_du] = i + 1

    return do_dai_lon_nhat


a = [2, 6, 3, 5, 4, 1]
k = 4

print(do_dai_doan_chia_het_cho_k(a, k))
```

> **Ghi chú quan trọng:** ví dụ này minh hoạ rõ ràng giới hạn của kỹ thuật cửa sổ trượt — không phải mọi bài toán "tìm đoạn con dài nhất/ngắn nhất thoả điều kiện" đều dùng được cửa sổ trượt. Chỉ khi điều kiện có tính **đơn điệu** theo việc mở rộng/co cửa sổ, ta mới áp dụng được kỹ thuật này.

### Ví dụ 3 (Khó) — Đoạn con ngắn nhất chứa tất cả ký tự của một chuỗi khác

Bài toán: cho 2 chuỗi `s` và `t`, tìm đoạn con **ngắn nhất** trong `s` chứa tất cả các ký tự của `t` (kể cả trùng lặp — ví dụ `t = "AAB"` cần ít nhất 2 ký tự 'A' và 1 ký tự 'B').

**Phân tích:** Khác với các ví dụ tìm "dài nhất" ở trên, bài này tìm "ngắn nhất" — nghĩa là ta mở rộng cửa sổ cho tới khi **đủ điều kiện**, sau đó cố gắng co cửa sổ lại càng nhiều càng tốt trong khi vẫn giữ đủ điều kiện, ghi nhận độ dài nhỏ nhất mỗi lần.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

string timDoanNganNhatChuaHetKyTu(string s, string t) {
    unordered_map<char, int> soLuongCanCo;
    for (int i = 0; i < (int)t.size(); i++) {
        soLuongCanCo[t[i]] = soLuongCanCo[t[i]] + 1;
    }

    unordered_map<char, int> soLuongTrongCuaSo;
    int soLoaiKyTuCanCo = (int)soLuongCanCo.size();
    int soLoaiKyTuDaDu = 0;

    int conTroTrai = 0;
    int doDaiNganNhat = INT_MAX;
    int viTriBatDauTotNhat = 0;

    for (int conTroPhai = 0; conTroPhai < (int)s.size(); conTroPhai++) {
        char kyTuHienTai = s[conTroPhai];
        soLuongTrongCuaSo[kyTuHienTai] = soLuongTrongCuaSo[kyTuHienTai] + 1;

        if (soLuongCanCo.count(kyTuHienTai) > 0 &&
            soLuongTrongCuaSo[kyTuHienTai] == soLuongCanCo[kyTuHienTai]) {
            soLoaiKyTuDaDu = soLoaiKyTuDaDu + 1;
        }

        while (soLoaiKyTuDaDu == soLoaiKyTuCanCo) {
            int doDaiHienTai = conTroPhai - conTroTrai + 1;
            if (doDaiHienTai < doDaiNganNhat) {
                doDaiNganNhat = doDaiHienTai;
                viTriBatDauTotNhat = conTroTrai;
            }

            char kyTuTrai = s[conTroTrai];
            soLuongTrongCuaSo[kyTuTrai] = soLuongTrongCuaSo[kyTuTrai] - 1;
            if (soLuongCanCo.count(kyTuTrai) > 0 &&
                soLuongTrongCuaSo[kyTuTrai] < soLuongCanCo[kyTuTrai]) {
                soLoaiKyTuDaDu = soLoaiKyTuDaDu - 1;
            }

            conTroTrai = conTroTrai + 1;
        }
    }

    if (doDaiNganNhat == INT_MAX) {
        return "";
    }
    return s.substr(viTriBatDauTotNhat, doDaiNganNhat);
}

int main() {
    string s = "ADOBECODEBANC";
    string t = "ABC";

    cout << timDoanNganNhatChuaHetKyTu(s, t) << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_doan_ngan_nhat_chua_het_ky_tu(s, t):
    so_luong_can_co = {}
    for ky_tu in t:
        if ky_tu in so_luong_can_co:
            so_luong_can_co[ky_tu] = so_luong_can_co[ky_tu] + 1
        else:
            so_luong_can_co[ky_tu] = 1

    so_luong_trong_cua_so = {}
    so_loai_ky_tu_can_co = len(so_luong_can_co)
    so_loai_ky_tu_da_du = 0

    con_tro_trai = 0
    do_dai_ngan_nhat = float('inf')
    vi_tri_bat_dau_tot_nhat = 0

    for con_tro_phai in range(len(s)):
        ky_tu_hien_tai = s[con_tro_phai]
        if ky_tu_hien_tai in so_luong_trong_cua_so:
            so_luong_trong_cua_so[ky_tu_hien_tai] = so_luong_trong_cua_so[ky_tu_hien_tai] + 1
        else:
            so_luong_trong_cua_so[ky_tu_hien_tai] = 1

        if ky_tu_hien_tai in so_luong_can_co:
            if so_luong_trong_cua_so[ky_tu_hien_tai] == so_luong_can_co[ky_tu_hien_tai]:
                so_loai_ky_tu_da_du = so_loai_ky_tu_da_du + 1

        while so_loai_ky_tu_da_du == so_loai_ky_tu_can_co:
            do_dai_hien_tai = con_tro_phai - con_tro_trai + 1
            if do_dai_hien_tai < do_dai_ngan_nhat:
                do_dai_ngan_nhat = do_dai_hien_tai
                vi_tri_bat_dau_tot_nhat = con_tro_trai

            ky_tu_trai = s[con_tro_trai]
            so_luong_trong_cua_so[ky_tu_trai] = so_luong_trong_cua_so[ky_tu_trai] - 1
            if ky_tu_trai in so_luong_can_co:
                if so_luong_trong_cua_so[ky_tu_trai] < so_luong_can_co[ky_tu_trai]:
                    so_loai_ky_tu_da_du = so_loai_ky_tu_da_du - 1

            con_tro_trai = con_tro_trai + 1

    if do_dai_ngan_nhat == float('inf'):
        return ""
    return s[vi_tri_bat_dau_tot_nhat : vi_tri_bat_dau_tot_nhat + do_dai_ngan_nhat]


s = "ADOBECODEBANC"
t = "ABC"

print(tim_doan_ngan_nhat_chua_het_ky_tu(s, t))
```

**Bài tập minh hoạ:** Cho mảng n số nguyên **dương** và số nguyên S, đếm số lượng đoạn con liên tiếp có tổng **nhỏ hơn** S.

**Lời giải:** Dùng cửa sổ trượt thay đổi: với mỗi vị trí `conTroPhai`, số lượng đoạn con kết thúc tại `conTroPhai` và thoả điều kiện chính là `conTroPhai - conTroTrai + 1` (tất cả các đoạn bắt đầu từ `conTroTrai, conTroTrai+1, ..., conTroPhai`).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long demSoDoanCoTongNhoHonS(vector<int> a, long long S) {
    int conTroTrai = 0;
    long long tongHienTai = 0;
    long long soLuongDoan = 0;

    for (int conTroPhai = 0; conTroPhai < (int)a.size(); conTroPhai++) {
        tongHienTai = tongHienTai + a[conTroPhai];

        while (tongHienTai >= S && conTroTrai <= conTroPhai) {
            tongHienTai = tongHienTai - a[conTroTrai];
            conTroTrai = conTroTrai + 1;
        }

        soLuongDoan = soLuongDoan + (conTroPhai - conTroTrai + 1);
    }

    return soLuongDoan;
}

int main() {
    vector<int> a = {1, 2, 3};
    long long S = 4;

    cout << demSoDoanCoTongNhoHonS(a, S) << endl;

    return 0;
}
```

#### PYTHON
```python
def dem_so_doan_co_tong_nho_hon_s(a, S):
    con_tro_trai = 0
    tong_hien_tai = 0
    so_luong_doan = 0

    for con_tro_phai in range(len(a)):
        tong_hien_tai = tong_hien_tai + a[con_tro_phai]

        while tong_hien_tai >= S and con_tro_trai <= con_tro_phai:
            tong_hien_tai = tong_hien_tai - a[con_tro_trai]
            con_tro_trai = con_tro_trai + 1

        so_luong_doan = so_luong_doan + (con_tro_phai - con_tro_trai + 1)

    return so_luong_doan


a = [1, 2, 3]
S = 4

print(dem_so_doan_co_tong_nho_hon_s(a, S))
```

---

## 6.4. Ngăn xếp đơn điệu (Monotonic Stack)

**Nêu bài toán:** Cho một mảng n số nguyên, với mỗi phần tử, hãy tìm phần tử **lớn hơn gần nhất** ở bên phải nó (nếu không có, trả về -1).

**Phân tích vấn đề:** Cách trực quan là với mỗi phần tử, duyệt tiếp về phía phải cho tới khi gặp phần tử lớn hơn. Nhưng nếu làm vậy, trong trường hợp xấu nhất (mảng giảm dần), mỗi phần tử phải duyệt gần hết phần còn lại của mảng.

**Giải pháp đơn thuần:** Với mỗi phần tử `i`, dùng một vòng lặp con duyệt từ `i+1` trở đi để tìm phần tử lớn hơn gần nhất. Độ phức tạp O(n²) trong trường hợp xấu nhất.

**Khó khăn với giải pháp đơn thuần:** Với n lớn, O(n²) không khả thi.

**Cách tiếp cận mới:** Duy trì một ngăn xếp chứa **chỉ số** của các phần tử mà ta chưa tìm được "phần tử lớn hơn gần nhất" cho chúng, sao cho giá trị tương ứng trong ngăn xếp luôn **giảm dần** từ đáy lên đỉnh. Khi gặp phần tử mới lớn hơn đỉnh ngăn xếp, ta biết ngay phần tử ở đỉnh đã tìm được "phần tử lớn hơn gần nhất" — pop nó ra và ghi nhận kết quả, tiếp tục cho tới khi ngăn xếp rỗng hoặc đỉnh lớn hơn phần tử hiện tại.

**Vì sao độ phức tạp là O(n)?** Mỗi phần tử chỉ được **push** vào ngăn xếp đúng 1 lần, và chỉ có thể bị **pop** ra tối đa 1 lần trong suốt toàn bộ quá trình chạy. Vòng lặp `while` bên trong vòng lặp `for` trông giống O(n²), nhưng tổng số lần push cộng pop trên toàn bộ thuật toán không vượt quá `2 × n`.

### Minh hoạ lời giải chi tiết

Cho `a = [2, 1, 2, 4, 3]`:

| Bước (i) | Giá trị a[i] | Ngăn xếp trước | Hành động | Ngăn xếp sau |
|---|---|---|---|---|
| 0 | 2 | [] | Rỗng, push chỉ số 0 | [0] |
| 1 | 1 | [0] | a[0]=2 > a[1]=1, không pop, push chỉ số 1 | [0, 1] |
| 2 | 2 | [0, 1] | a[1]=1 < a[2]=2 -> pop 1, ghi nhận: kết_quả[1]=2. a[0]=2, không < a[2]=2, dừng pop. Push chỉ số 2 | [0, 2] |
| 3 | 4 | [0, 2] | a[2]=2 < a[3]=4 -> pop 2, ghi nhận: kết_quả[2]=4. a[0]=2 < a[3]=4 -> pop 0, ghi nhận: kết_quả[0]=4. Push chỉ số 3 | [3] |
| 4 | 3 | [3] | a[3]=4 > a[4]=3, không pop, push chỉ số 4 | [3, 4] |

Kết thúc: các chỉ số còn lại trong ngăn xếp (3 và 4) không tìm được "phần tử lớn hơn gần nhất", nên `kết_quả[3] = -1` và `kết_quả[4] = -1`.

Kết quả cuối cùng: `[4, 2, 4, -1, -1]`.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> timPhanTuLonHonGanNhat(vector<int> a) {
    int n = (int)a.size();
    vector<int> ketQua(n, -1);
    stack<int> nganXep; // lưu CHỈ SỐ, giá trị tương ứng giảm dần từ đáy lên đỉnh

    for (int i = 0; i < n; i++) {
        while (!nganXep.empty() && a[nganXep.top()] < a[i]) {
            int chiSoCanGhi = nganXep.top();
            nganXep.pop();
            ketQua[chiSoCanGhi] = a[i];
        }
        nganXep.push(i);
    }

    return ketQua;
}

int main() {
    vector<int> a = {2, 1, 2, 4, 3};
    vector<int> ketQua = timPhanTuLonHonGanNhat(a);

    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_phan_tu_lon_hon_gan_nhat(a):
    n = len(a)
    ket_qua = [-1] * n
    ngan_xep = []  # lưu chỉ số, giá trị tương ứng giảm dần từ đáy lên đỉnh

    for i in range(n):
        while len(ngan_xep) > 0 and a[ngan_xep[-1]] < a[i]:
            chi_so_can_ghi = ngan_xep.pop()
            ket_qua[chi_so_can_ghi] = a[i]
        ngan_xep.append(i)

    return ket_qua


a = [2, 1, 2, 4, 3]
ket_qua = tim_phan_tu_lon_hon_gan_nhat(a)
print(ket_qua)
```

### Ví dụ 1 (Dễ) — Số ngày phải chờ để có nhiệt độ ấm hơn

Bài toán: cho danh sách nhiệt độ theo từng ngày, với mỗi ngày, hãy tính số ngày cần chờ để có một ngày ấm hơn ngày đó (nếu không có, trả về 0).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> tinhSoNgayChoAmHon(vector<int> nhietDo) {
    int n = (int)nhietDo.size();
    vector<int> ketQua(n, 0);
    stack<int> nganXep;

    for (int i = 0; i < n; i++) {
        while (!nganXep.empty() && nhietDo[nganXep.top()] < nhietDo[i]) {
            int chiSoCanGhi = nganXep.top();
            nganXep.pop();
            ketQua[chiSoCanGhi] = i - chiSoCanGhi;
        }
        nganXep.push(i);
    }

    return ketQua;
}

int main() {
    vector<int> nhietDo = {73, 74, 75, 71, 69, 72, 76, 73};
    vector<int> ketQua = tinhSoNgayChoAmHon(nhietDo);

    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def tinh_so_ngay_cho_am_hon(nhiet_do):
    n = len(nhiet_do)
    ket_qua = [0] * n
    ngan_xep = []

    for i in range(n):
        while len(ngan_xep) > 0 and nhiet_do[ngan_xep[-1]] < nhiet_do[i]:
            chi_so_can_ghi = ngan_xep.pop()
            ket_qua[chi_so_can_ghi] = i - chi_so_can_ghi
        ngan_xep.append(i)

    return ket_qua


nhiet_do = [73, 74, 75, 71, 69, 72, 76, 73]
ket_qua = tinh_so_ngay_cho_am_hon(nhiet_do)
print(ket_qua)
```

### Ví dụ 2 (Trung bình) — Hình chữ nhật lớn nhất trong biểu đồ cột

Bài toán: cho một biểu đồ cột, mỗi cột có một chiều cao cho trước, các cột có độ rộng bằng nhau (bằng 1). Hãy tìm diện tích hình chữ nhật lớn nhất có thể tạo ra từ các cột liên tiếp trong biểu đồ.

**Phân tích:** Với mỗi cột, nếu ta biết được "cột thấp hơn gần nhất bên trái" và "cột thấp hơn gần nhất bên phải" của nó, ta có thể tính được chiều rộng lớn nhất mà hình chữ nhật có chiều cao bằng cột đó có thể đạt tới — chính là khoảng cách giữa 2 cột thấp hơn gần nhất đó.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timDienTichHinhChuNhatLonNhat(vector<int> chieuCao) {
    int n = (int)chieuCao.size();
    stack<int> nganXep; // lưu chỉ số, chiều cao tương ứng tăng dần từ đáy lên đỉnh
    long long dienTichLonNhat = 0;

    for (int i = 0; i <= n; i++) {
        int chieuCaoHienTai;
        if (i == n) {
            chieuCaoHienTai = 0; // thêm cột ảo chiều cao 0 để "xả" hết ngăn xếp ở cuối
        } else {
            chieuCaoHienTai = chieuCao[i];
        }

        while (!nganXep.empty() && chieuCao[nganXep.top()] > chieuCaoHienTai) {
            int chiSoDinh = nganXep.top();
            nganXep.pop();
            int chieuCaoCanTinh = chieuCao[chiSoDinh];

            int chieuRong;
            if (nganXep.empty()) {
                chieuRong = i;
            } else {
                chieuRong = i - nganXep.top() - 1;
            }

            long long dienTichHienTai = (long long)chieuCaoCanTinh * (long long)chieuRong;
            if (dienTichHienTai > dienTichLonNhat) {
                dienTichLonNhat = dienTichHienTai;
            }
        }

        nganXep.push(i);
    }

    return dienTichLonNhat;
}

int main() {
    vector<int> chieuCao = {2, 1, 5, 6, 2, 3};
    cout << timDienTichHinhChuNhatLonNhat(chieuCao) << endl;
    return 0;
}
```

#### PYTHON
```python
def tim_dien_tich_hinh_chu_nhat_lon_nhat(chieu_cao):
    n = len(chieu_cao)
    ngan_xep = []  # lưu chỉ số, chiều cao tương ứng tăng dần từ đáy lên đỉnh
    dien_tich_lon_nhat = 0

    for i in range(n + 1):
        if i == n:
            chieu_cao_hien_tai = 0
        else:
            chieu_cao_hien_tai = chieu_cao[i]

        while len(ngan_xep) > 0 and chieu_cao[ngan_xep[-1]] > chieu_cao_hien_tai:
            chi_so_dinh = ngan_xep.pop()
            chieu_cao_can_tinh = chieu_cao[chi_so_dinh]

            if len(ngan_xep) == 0:
                chieu_rong = i
            else:
                chieu_rong = i - ngan_xep[-1] - 1

            dien_tich_hien_tai = chieu_cao_can_tinh * chieu_rong
            if dien_tich_hien_tai > dien_tich_lon_nhat:
                dien_tich_lon_nhat = dien_tich_hien_tai

        ngan_xep.append(i)

    return dien_tich_lon_nhat


chieu_cao = [2, 1, 5, 6, 2, 3]
print(tim_dien_tich_hinh_chu_nhat_lon_nhat(chieu_cao))
```

### Ví dụ 3 (Khó) — Tổng độ rộng của các đoạn con nhìn thấy được (Sum of Subarray Minimums)

Bài toán: cho mảng n số nguyên, hãy tính tổng của giá trị nhỏ nhất trên **tất cả** các đoạn con liên tiếp có thể có của mảng đó.

**Phân tích:** Số lượng đoạn con của một mảng n phần tử là O(n²), nên không thể tính trực tiếp giá trị nhỏ nhất cho từng đoạn. Thay vào đó, với mỗi phần tử `a[i]`, ta tính xem nó là giá trị nhỏ nhất của **bao nhiêu đoạn con** — bằng cách tìm "phần tử nhỏ hơn gần nhất bên trái" và "phần tử nhỏ hơn gần nhất bên phải" của nó (dùng đúng kỹ thuật monotonic stack), rồi nhân số lượng lựa chọn bên trái với số lượng lựa chọn bên phải.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long tongCacGiaTriNhoNhat(vector<int> a) {
    int n = (int)a.size();
    const long long MOD = 1000000007;

    vector<int> khoangCachTrai(n);
    vector<int> khoangCachPhai(n);

    // Tìm khoảng cách tới phần tử nhỏ hơn gần nhất bên trái (dùng ngăn xếp tăng dần)
    stack<int> nganXepTrai;
    for (int i = 0; i < n; i++) {
        while (!nganXepTrai.empty() && a[nganXepTrai.top()] >= a[i]) {
            nganXepTrai.pop();
        }
        if (nganXepTrai.empty()) {
            khoangCachTrai[i] = i + 1;
        } else {
            khoangCachTrai[i] = i - nganXepTrai.top();
        }
        nganXepTrai.push(i);
    }

    // Tìm khoảng cách tới phần tử nhỏ hơn gần nhất bên phải
    stack<int> nganXepPhai;
    for (int i = n - 1; i >= 0; i--) {
        while (!nganXepPhai.empty() && a[nganXepPhai.top()] > a[i]) {
            nganXepPhai.pop();
        }
        if (nganXepPhai.empty()) {
            khoangCachPhai[i] = n - i;
        } else {
            khoangCachPhai[i] = nganXepPhai.top() - i;
        }
        nganXepPhai.push(i);
    }

    long long tongKetQua = 0;
    for (int i = 0; i < n; i++) {
        long long soLuongDoanConChuaAi = (long long)khoangCachTrai[i] * (long long)khoangCachPhai[i];
        long long dongGop = (long long)a[i] * soLuongDoanConChuaAi;
        tongKetQua = (tongKetQua + dongGop) % MOD;
    }

    return tongKetQua;
}

int main() {
    vector<int> a = {3, 1, 2, 4};
    cout << tongCacGiaTriNhoNhat(a) << endl;
    return 0;
}
```

#### PYTHON
```python
def tong_cac_gia_tri_nho_nhat(a):
    n = len(a)
    MOD = 1000000007

    khoang_cach_trai = [0] * n
    khoang_cach_phai = [0] * n

    ngan_xep_trai = []
    for i in range(n):
        while len(ngan_xep_trai) > 0 and a[ngan_xep_trai[-1]] >= a[i]:
            ngan_xep_trai.pop()
        if len(ngan_xep_trai) == 0:
            khoang_cach_trai[i] = i + 1
        else:
            khoang_cach_trai[i] = i - ngan_xep_trai[-1]
        ngan_xep_trai.append(i)

    ngan_xep_phai = []
    for i in range(n - 1, -1, -1):
        while len(ngan_xep_phai) > 0 and a[ngan_xep_phai[-1]] > a[i]:
            ngan_xep_phai.pop()
        if len(ngan_xep_phai) == 0:
            khoang_cach_phai[i] = n - i
        else:
            khoang_cach_phai[i] = ngan_xep_phai[-1] - i
        ngan_xep_phai.append(i)

    tong_ket_qua = 0
    for i in range(n):
        so_luong_doan_con_chua_ai = khoang_cach_trai[i] * khoang_cach_phai[i]
        dong_gop = a[i] * so_luong_doan_con_chua_ai
        tong_ket_qua = (tong_ket_qua + dong_gop) % MOD

    return tong_ket_qua


a = [3, 1, 2, 4]
print(tong_cac_gia_tri_nho_nhat(a))
```

**Bài tập minh hoạ:** Cho mảng n số nguyên, với mỗi phần tử, hãy tìm phần tử **nhỏ hơn gần nhất** ở bên trái nó (nếu không có, trả về -1).

**Lời giải:** Tương tự bài toán chính của mục này, nhưng duyệt từ trái sang phải và tìm phần tử **nhỏ hơn** thay vì lớn hơn, đồng thời tìm ở **bên trái** thay vì bên phải.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> timPhanTuNhoHonGanNhatBenTrai(vector<int> a) {
    int n = (int)a.size();
    vector<int> ketQua(n, -1);
    stack<int> nganXep;

    for (int i = 0; i < n; i++) {
        while (!nganXep.empty() && a[nganXep.top()] >= a[i]) {
            nganXep.pop();
        }

        if (!nganXep.empty()) {
            ketQua[i] = a[nganXep.top()];
        }

        nganXep.push(i);
    }

    return ketQua;
}

int main() {
    vector<int> a = {4, 2, 1, 5, 3};
    vector<int> ketQua = timPhanTuNhoHonGanNhatBenTrai(a);

    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_phan_tu_nho_hon_gan_nhat_ben_trai(a):
    n = len(a)
    ket_qua = [-1] * n
    ngan_xep = []

    for i in range(n):
        while len(ngan_xep) > 0 and a[ngan_xep[-1]] >= a[i]:
            ngan_xep.pop()

        if len(ngan_xep) > 0:
            ket_qua[i] = a[ngan_xep[-1]]

        ngan_xep.append(i)

    return ket_qua


a = [4, 2, 1, 5, 3]
ket_qua = tim_phan_tu_nho_hon_gan_nhat_ben_trai(a)
print(ket_qua)
```

---

## 6.5. Hàng đợi đơn điệu (Monotonic Queue / Deque)

**Nêu bài toán:** Cho mảng n số nguyên và số k, với mỗi cửa sổ con liên tiếp có độ dài k, hãy tìm giá trị **lớn nhất** trong cửa sổ đó.

**Phân tích vấn đề:** Đây là bài toán kết hợp giữa "cửa sổ trượt cố định" (mục 6.2) và "tìm giá trị lớn nhất". Nếu với mỗi cửa sổ, ta duyệt lại từ đầu để tìm max, độ phức tạp sẽ là O(n × k).

**Giải pháp đơn thuần:** Với mỗi cửa sổ, duyệt qua k phần tử để tìm giá trị lớn nhất. Độ phức tạp O(n × k).

**Khó khăn với giải pháp đơn thuần:** Khi k lớn (tỉ lệ với n), độ phức tạp gần O(n²) — quá chậm với n lớn.

**Giải pháp trung gian (chưa tối ưu nhất):** Dùng một `priority_queue` (heap) để luôn lấy được giá trị lớn nhất — độ phức tạp O(n log n). Đây là cải thiện đáng kể so với O(n × k), nhưng vẫn chưa phải tối ưu nhất, vì heap phải chứa cả những phần tử **đã ra khỏi cửa sổ từ lâu** (cần thêm kỹ thuật "xoá trễ" - lazy deletion để xử lý).

**Cách tiếp cận mới — Monotonic Deque:** Duy trì một deque chứa **chỉ số** các phần tử, sao cho giá trị tương ứng trong deque luôn **giảm dần** từ đầu đến cuối. Khi thêm phần tử mới, ta loại bỏ (pop từ đuôi) tất cả các phần tử ở đuôi deque có giá trị **nhỏ hơn** phần tử mới — vì chúng vừa nhỏ hơn vừa cũ hơn, sẽ không bao giờ là giá trị lớn nhất nữa. Khi cửa sổ trượt qua, nếu chỉ số ở đầu deque đã ra khỏi phạm vi cửa sổ, ta loại bỏ nó (pop từ đầu). Giá trị lớn nhất của cửa sổ hiện tại luôn nằm ở **đầu** deque. Vì mỗi phần tử chỉ được push và pop đúng 1 lần trong suốt quá trình, độ phức tạp tổng thể là O(n).

### Minh hoạ lời giải chi tiết

Cho `a = [1, 3, -1, -3, 5, 3, 6, 7]`, `k = 3`. Ta theo dõi deque (lưu chỉ số), giá trị tương ứng luôn giảm dần từ đầu đến cuối:

| i | a[i] | Deque trước | Hành động | Deque sau | Max cửa sổ |
|---|---|---|---|---|---|
| 0 | 1 | [] | Push 0 | [0] | (chưa đủ k phần tử) |
| 1 | 3 | [0] | a[0]=1 < 3 -> pop 0. Push 1 | [1] | (chưa đủ k phần tử) |
| 2 | -1 | [1] | a[1]=3 > -1, không pop. Push 2 | [1, 2] | a[1]=3 |
| 3 | -3 | [1, 2] | a[2]=-1 > -3, không pop. Push 3 | [1, 2, 3] | a[1]=3 |
| 4 | 5 | [1, 2, 3] | Chỉ số 1 đã ra khỏi cửa sổ [2,4] -> pop đầu. a[2]=-1<5 pop. a[3]=-3<5 pop. Push 4 | [4] | a[4]=5 |
| 5 | 3 | [4] | a[4]=5 > 3, không pop. Push 5 | [4, 5] | a[4]=5 |
| 6 | 6 | [4, 5] | Chỉ số 4 vẫn trong cửa sổ [4,6]. a[4]=5<6 pop. a[5]=3<6 pop. Push 6 | [6] | a[6]=6 |
| 7 | 7 | [6] | Chỉ số 6 vẫn trong cửa sổ [5,7]. a[6]=6<7 pop. Push 7 | [7] | a[7]=7 |

Kết quả: `[3, 3, 5, 5, 6, 7]`.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> timGiaTriLonNhatMoiCuaSo(vector<int> a, int k) {
    deque<int> hangDoi; // lưu CHỈ SỐ, giá trị tương ứng giảm dần từ đầu đến cuối
    vector<int> ketQua;

    for (int i = 0; i < (int)a.size(); i++) {
        // Bỏ các chỉ số đã ra khỏi cửa sổ [i-k+1, i]
        while (!hangDoi.empty() && hangDoi.front() <= i - k) {
            hangDoi.pop_front();
        }

        // Bỏ các phần tử ở đuôi nhỏ hơn phần tử hiện tại
        while (!hangDoi.empty() && a[hangDoi.back()] < a[i]) {
            hangDoi.pop_back();
        }

        hangDoi.push_back(i);

        if (i >= k - 1) {
            ketQua.push_back(a[hangDoi.front()]);
        }
    }

    return ketQua;
}

int main() {
    vector<int> a = {1, 3, -1, -3, 5, 3, 6, 7};
    int k = 3;

    vector<int> ketQua = timGiaTriLonNhatMoiCuaSo(a, k);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def tim_gia_tri_lon_nhat_moi_cua_so(a, k):
    hang_doi = deque()  # lưu chỉ số, giá trị tương ứng giảm dần từ đầu đến cuối
    ket_qua = []

    for i in range(len(a)):
        while len(hang_doi) > 0 and hang_doi[0] <= i - k:
            hang_doi.popleft()

        while len(hang_doi) > 0 and a[hang_doi[-1]] < a[i]:
            hang_doi.pop()

        hang_doi.append(i)

        if i >= k - 1:
            ket_qua.append(a[hang_doi[0]])

    return ket_qua


a = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3

ket_qua = tim_gia_tri_lon_nhat_moi_cua_so(a, k)
print(ket_qua)
```

### Ví dụ 1 (Dễ) — Giá trị nhỏ nhất trong mỗi cửa sổ trượt

**Phân tích:** Đối xứng với bài toán chính, chỉ cần đảo chiều so sánh: deque duy trì giá trị **tăng dần** từ đầu đến cuối thay vì giảm dần.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> timGiaTriNhoNhatMoiCuaSo(vector<int> a, int k) {
    deque<int> hangDoi; // lưu chỉ số, giá trị tương ứng tăng dần từ đầu đến cuối
    vector<int> ketQua;

    for (int i = 0; i < (int)a.size(); i++) {
        while (!hangDoi.empty() && hangDoi.front() <= i - k) {
            hangDoi.pop_front();
        }

        while (!hangDoi.empty() && a[hangDoi.back()] > a[i]) {
            hangDoi.pop_back();
        }

        hangDoi.push_back(i);

        if (i >= k - 1) {
            ketQua.push_back(a[hangDoi.front()]);
        }
    }

    return ketQua;
}
```

#### PYTHON
```python
from collections import deque


def tim_gia_tri_nho_nhat_moi_cua_so(a, k):
    hang_doi = deque()
    ket_qua = []

    for i in range(len(a)):
        while len(hang_doi) > 0 and hang_doi[0] <= i - k:
            hang_doi.popleft()

        while len(hang_doi) > 0 and a[hang_doi[-1]] > a[i]:
            hang_doi.pop()

        hang_doi.append(i)

        if i >= k - 1:
            ket_qua.append(a[hang_doi[0]])

    return ket_qua
```

### Ví dụ 2 (Trung bình) — Đoạn con dài nhất có (max - min ≤ giới hạn)

Bài toán: cho mảng n số nguyên và số `gioiHan`, tìm độ dài lớn nhất của đoạn con liên tiếp sao cho hiệu giữa giá trị lớn nhất và giá trị nhỏ nhất trong đoạn đó không vượt quá `gioiHan`.

**Phân tích:** Đây là bài toán kết hợp giữa cửa sổ trượt thay đổi (mục 6.3) và monotonic deque — cần duy trì **đồng thời 2 deque**, một để theo dõi giá trị lớn nhất, một để theo dõi giá trị nhỏ nhất của cửa sổ hiện tại.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int doDaiDoanConThoaGioiHan(vector<int> a, int gioiHan) {
    deque<int> dequeMax; // giảm dần từ đầu đến cuối
    deque<int> dequeMin; // tăng dần từ đầu đến cuối

    int conTroTrai = 0;
    int doDaiLonNhat = 0;

    for (int conTroPhai = 0; conTroPhai < (int)a.size(); conTroPhai++) {
        while (!dequeMax.empty() && a[dequeMax.back()] < a[conTroPhai]) {
            dequeMax.pop_back();
        }
        dequeMax.push_back(conTroPhai);

        while (!dequeMin.empty() && a[dequeMin.back()] > a[conTroPhai]) {
            dequeMin.pop_back();
        }
        dequeMin.push_back(conTroPhai);

        while (a[dequeMax.front()] - a[dequeMin.front()] > gioiHan) {
            if (dequeMax.front() == conTroTrai) {
                dequeMax.pop_front();
            }
            if (dequeMin.front() == conTroTrai) {
                dequeMin.pop_front();
            }
            conTroTrai = conTroTrai + 1;
        }

        int doDaiHienTai = conTroPhai - conTroTrai + 1;
        if (doDaiHienTai > doDaiLonNhat) {
            doDaiLonNhat = doDaiHienTai;
        }
    }

    return doDaiLonNhat;
}

int main() {
    vector<int> a = {8, 2, 4, 7};
    int gioiHan = 4;

    cout << doDaiDoanConThoaGioiHan(a, gioiHan) << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def do_dai_doan_con_thoa_gioi_han(a, gioi_han):
    deque_max = deque()  # giảm dần từ đầu đến cuối
    deque_min = deque()  # tăng dần từ đầu đến cuối

    con_tro_trai = 0
    do_dai_lon_nhat = 0

    for con_tro_phai in range(len(a)):
        while len(deque_max) > 0 and a[deque_max[-1]] < a[con_tro_phai]:
            deque_max.pop()
        deque_max.append(con_tro_phai)

        while len(deque_min) > 0 and a[deque_min[-1]] > a[con_tro_phai]:
            deque_min.pop()
        deque_min.append(con_tro_phai)

        while a[deque_max[0]] - a[deque_min[0]] > gioi_han:
            if deque_max[0] == con_tro_trai:
                deque_max.popleft()
            if deque_min[0] == con_tro_trai:
                deque_min.popleft()
            con_tro_trai = con_tro_trai + 1

        do_dai_hien_tai = con_tro_phai - con_tro_trai + 1
        if do_dai_hien_tai > do_dai_lon_nhat:
            do_dai_lon_nhat = do_dai_hien_tai

    return do_dai_lon_nhat


a = [8, 2, 4, 7]
gioi_han = 4

print(do_dai_doan_con_thoa_gioi_han(a, gioi_han))
```

### Ví dụ 3 (Khó) — Tối ưu hoá quy hoạch động bằng Monotonic Queue

Bài toán: cho n công việc theo thứ tự, công việc thứ i mang lại lợi ích `loiIch[i]`, nhưng nếu chọn công việc `i`, ta không được chọn bất kỳ công việc nào trong khoảng `k` công việc liền trước đó. Hãy tìm tổng lợi ích lớn nhất có thể đạt được.

**Phân tích:** Đặt `dp[i]` là tổng lợi ích lớn nhất khi xét đến công việc thứ i và **có chọn** công việc đó. Công thức: `dp[i] = loiIch[i] + max(dp[j])` với `j` chạy từ `0` đến `i - k - 1`. Nếu tính trực tiếp `max(dp[j])` cho mỗi `i`, độ phức tạp là O(n × k). Nhưng vì cửa sổ xét max trượt dần một cách đơn điệu theo `i`, ta có thể dùng monotonic deque để duy trì max trong O(1) khấu hao mỗi bước, tổng độ phức tạp giảm xuống O(n).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long toiUuHoaBangMonotonicQueue(vector<int> loiIch, int k) {
    int n = (int)loiIch.size();
    vector<long long> dp(n, 0);
    deque<int> hangDoi; // lưu chỉ số j sao cho dp[j] giảm dần từ đầu đến cuối

    hangDoi.push_back(-1); // "công việc ảo" trước công việc đầu tiên, dp = 0

    for (int i = 0; i < n; i++) {
        // Loại bỏ các chỉ số đã ra khỏi phạm vi cho phép (j <= i - k - 1)
        while (!hangDoi.empty() && hangDoi.front() < i - k) {
            hangDoi.pop_front();
        }

        long long giaTriTotNhatTruocDo = dp[hangDoi.front() >= 0 ? hangDoi.front() : 0];
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

    long long ketQuaTotNhat = 0;
    for (int i = 0; i < n; i++) {
        if (dp[i] > ketQuaTotNhat) {
            ketQuaTotNhat = dp[i];
        }
    }

    return ketQuaTotNhat;
}

int main() {
    vector<int> loiIch = {5, 1, 2, 8, 3, 4};
    int k = 2;

    cout << toiUuHoaBangMonotonicQueue(loiIch, k) << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def toi_uu_hoa_bang_monotonic_queue(loi_ich, k):
    n = len(loi_ich)
    dp = [0] * n
    hang_doi = deque()

    hang_doi.append(-1)  # "công việc ảo" trước công việc đầu tiên, dp = 0

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

    ket_qua_tot_nhat = 0
    for i in range(n):
        if dp[i] > ket_qua_tot_nhat:
            ket_qua_tot_nhat = dp[i]

    return ket_qua_tot_nhat


loi_ich = [5, 1, 2, 8, 3, 4]
k = 2

print(toi_uu_hoa_bang_monotonic_queue(loi_ich, k))
```

> **Ghi chú:** đây là ví dụ nâng cao kết hợp Quy hoạch động (sẽ học kỹ ở Chương 8) với Monotonic Queue — cho thấy 2 kỹ thuật của chương này (cửa sổ trượt và ngăn xếp/hàng đợi đơn điệu) không chỉ dùng độc lập mà còn có thể **kết hợp với các kỹ thuật khác** để tối ưu hoá độ phức tạp.

**Bài tập minh hoạ:** Cho mảng n số nguyên và số k, tính **chi phí** của mỗi cửa sổ trượt kích thước k, với chi phí được định nghĩa là hiệu giữa giá trị lớn nhất và giá trị nhỏ nhất trong cửa sổ đó.

**Lời giải:** Kết hợp cả 2 kỹ thuật đã học ở mục này: dùng đồng thời 1 deque theo dõi max và 1 deque theo dõi min cho cửa sổ cố định kích thước k.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> tinhChiPhiMoiCuaSo(vector<int> a, int k) {
    deque<int> dequeMax;
    deque<int> dequeMin;
    vector<int> ketQua;

    for (int i = 0; i < (int)a.size(); i++) {
        while (!dequeMax.empty() && dequeMax.front() <= i - k) {
            dequeMax.pop_front();
        }
        while (!dequeMax.empty() && a[dequeMax.back()] < a[i]) {
            dequeMax.pop_back();
        }
        dequeMax.push_back(i);

        while (!dequeMin.empty() && dequeMin.front() <= i - k) {
            dequeMin.pop_front();
        }
        while (!dequeMin.empty() && a[dequeMin.back()] > a[i]) {
            dequeMin.pop_back();
        }
        dequeMin.push_back(i);

        if (i >= k - 1) {
            int chiPhi = a[dequeMax.front()] - a[dequeMin.front()];
            ketQua.push_back(chiPhi);
        }
    }

    return ketQua;
}

int main() {
    vector<int> a = {4, 2, 8, 5, 1, 9};
    int k = 3;

    vector<int> ketQua = tinhChiPhiMoiCuaSo(a, k);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def tinh_chi_phi_moi_cua_so(a, k):
    deque_max = deque()
    deque_min = deque()
    ket_qua = []

    for i in range(len(a)):
        while len(deque_max) > 0 and deque_max[0] <= i - k:
            deque_max.popleft()
        while len(deque_max) > 0 and a[deque_max[-1]] < a[i]:
            deque_max.pop()
        deque_max.append(i)

        while len(deque_min) > 0 and deque_min[0] <= i - k:
            deque_min.popleft()
        while len(deque_min) > 0 and a[deque_min[-1]] > a[i]:
            deque_min.pop()
        deque_min.append(i)

        if i >= k - 1:
            chi_phi = a[deque_max[0]] - a[deque_min[0]]
            ket_qua.append(chi_phi)

    return ket_qua


a = [4, 2, 8, 5, 1, 9]
k = 3

ket_qua = tinh_chi_phi_moi_cua_so(a, k)
print(ket_qua)
```

---

## Tổng kết Chương 6

- **Hai con trỏ (Two Pointers):** dùng khi mảng đã sắp xếp hoặc có tính chất đơn điệu, 2 con trỏ đi từ 2 đầu vào giữa (hoặc trên 2 mảng khác nhau), giảm độ phức tạp từ O(n²) xuống O(n).
- **Cửa sổ trượt cố định:** dùng khi đề cho sẵn kích thước đoạn con k, cập nhật O(1) mỗi bước trượt bằng cách trừ phần tử cũ, cộng phần tử mới.
- **Cửa sổ trượt thay đổi:** dùng khi cần tìm đoạn dài nhất/ngắn nhất thoả điều kiện đơn điệu, 2 con trỏ đều chỉ tiến, không bao giờ lùi lại — khác với hai con trỏ cổ điển (đi từ 2 đầu vào giữa).
- **Ngăn xếp đơn điệu (Monotonic Stack):** giải bài toán "phần tử lớn hơn/nhỏ hơn gần nhất" trong O(n), nhờ tính chất mỗi phần tử chỉ được push và pop đúng 1 lần.
- **Hàng đợi đơn điệu (Monotonic Queue/Deque):** giải bài toán "giá trị lớn nhất/nhỏ nhất trong mọi cửa sổ trượt" trong O(n), thường kết hợp với cửa sổ trượt cố định hoặc thay đổi.
- **Điều kiện quan trọng cần nhớ:** tất cả các kỹ thuật trong chương này đều dựa vào tính chất **đơn điệu** — nếu bài toán không có tính đơn điệu (như Ví dụ 2 mục 6.3 — chia hết cho k), các kỹ thuật này sẽ cho kết quả sai, cần chuyển sang kỹ thuật khác (thường là mảng cộng dồn kết hợp bảng băm).

---

## Bài tập cuối chương — Chương 6

### PHẦN A: ĐỀ BÀI (18 bài, không kèm lời giải)

**Bài 1 — Two Sum II - Input Array Is Sorted (LeetCode 167) — Dễ**
Cho một mảng đã sắp xếp tăng dần và số target, tìm 2 chỉ số (1-indexed) sao cho tổng giá trị tại 2 vị trí đó bằng target.

**Bài 2 — Valid Palindrome (LeetCode 125) — Dễ**
Cho một chuỗi, kiểm tra xem chuỗi đó (sau khi bỏ qua các ký tự không phải chữ và số, không phân biệt hoa thường) có phải là chuỗi đối xứng hay không.

**Bài 3 — Container With Most Water (LeetCode 11) — Trung bình**
Cho n cột có chiều cao khác nhau đặt trên trục hoành, hãy tìm 2 cột sao cho lượng nước chứa được giữa 2 cột đó (theo chiều rộng và chiều cao thấp hơn trong 2 cột) là lớn nhất.

**Bài 4 — 3Sum (LeetCode 15) — Trung bình**
Cho một mảng số nguyên, tìm tất cả các bộ ba phân biệt có tổng bằng 0 (không được trùng lặp bộ ba).

**Bài 5 — Sort Colors (LeetCode 75) — Trung bình**
Cho mảng chỉ chứa các số 0, 1, 2, hãy sắp xếp mảng đó chỉ trong một lượt duyệt duy nhất, không dùng hàm sắp xếp có sẵn.

**Bài 6 — Maximum Average Subarray I (LeetCode 643) — Dễ**
Cho mảng số nguyên và số k, tìm trung bình cộng lớn nhất của một đoạn con liên tiếp có độ dài đúng bằng k.

**Bài 7 — Grumpy Bookstore Owner (LeetCode 1052) — Trung bình**
Cho số lượng khách hàng mỗi phút trong ngày và trạng thái "cáu gắt" của chủ cửa hàng mỗi phút, chủ cửa hàng có thể dùng kỹ năng đặc biệt để không cáu gắt trong đúng k phút liên tiếp. Hãy tìm số lượng khách hàng hài lòng tối đa có thể đạt được.

**Bài 8 — Longest Substring Without Repeating Characters (LeetCode 3) — Trung bình**
Cho một chuỗi, tìm độ dài chuỗi con liên tiếp dài nhất không có ký tự lặp lại.

**Bài 9 — Fruit Into Baskets (LeetCode 904) — Trung bình**
Cho một dãy các loại trái cây, hãy tìm độ dài đoạn con liên tiếp dài nhất chỉ chứa tối đa 2 loại trái cây khác nhau.

**Bài 10 — Minimum Size Subarray Sum (LeetCode 209) — Trung bình**
Cho mảng số nguyên dương và số target, tìm độ dài đoạn con liên tiếp ngắn nhất có tổng lớn hơn hoặc bằng target.

**Bài 11 — Subarray Product Less Than K (LeetCode 713) — Trung bình**
Cho mảng số nguyên dương và số k, đếm số lượng đoạn con liên tiếp có tích các phần tử nhỏ hơn k.

**Bài 12 — Daily Temperatures (LeetCode 739) — Trung bình**
Giống hệt Ví dụ 1, mục 6.4 đã trình bày (dùng để luyện tập lại).

**Bài 13 — Largest Rectangle in Histogram (LeetCode 84) — Khó**
Giống hệt Ví dụ 2, mục 6.4 đã trình bày (dùng để luyện tập lại).

**Bài 14 — Trapping Rain Water (LeetCode 42) — Khó**
Cho một biểu đồ cột thể hiện độ cao địa hình, hãy tính lượng nước mưa có thể đọng lại giữa các cột sau khi mưa.

**Bài 15 — Sliding Window Maximum (LeetCode 239) — Khó**
Giống hệt bài toán chính, mục 6.5 đã trình bày (dùng để luyện tập lại).

**Bài 16 — Shortest Subarray with Sum at Least K (LeetCode 862) — Khó**
Cho mảng số nguyên (có thể có số âm) và số K, tìm độ dài đoạn con liên tiếp ngắn nhất có tổng lớn hơn hoặc bằng K. (Gợi ý: bài này khác Bài 10 ở chỗ mảng có thể có số âm, nên hai con trỏ thuần tuý không áp dụng được — cần kết hợp mảng cộng dồn với monotonic deque.)

**Bài 17 — Constrained Subsequence Sum (LeetCode 1425) — Khó**
Cho mảng số nguyên (có thể âm) và số k, chọn một dãy con (không nhất thiết liên tiếp) sao cho khoảng cách giữa 2 chỉ số được chọn liên tiếp trong dãy con không vượt quá k, tối đa hoá tổng của dãy con đó. (Đây là dạng mở rộng của Ví dụ 3, mục 6.5.)

**Bài 18 — Jump Game VI (LeetCode 1696) — Khó**
Cho mảng điểm số và số k, xuất phát từ vị trí đầu tiên, mỗi bước được nhảy tối đa k vị trí về phía trước, đích đến là vị trí cuối cùng. Hãy tìm tổng điểm lớn nhất có thể thu được trên đường đi. (Cũng là một dạng DP tối ưu bằng Monotonic Queue.)

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Two Sum II</summary>

Áp dụng đúng khung 2 con trỏ đã trình bày ở "Minh hoạ lời giải chi tiết", mục 6.1, chỉ cần trả về chỉ số (1-indexed) thay vì giá trị.

#### C++
```cpp
vector<int> twoSumSorted(vector<int> numbers, int target) {
    int conTroTrai = 0;
    int conTroPhai = (int)numbers.size() - 1;

    while (conTroTrai < conTroPhai) {
        int tong = numbers[conTroTrai] + numbers[conTroPhai];
        if (tong == target) {
            vector<int> ketQua;
            ketQua.push_back(conTroTrai + 1);
            ketQua.push_back(conTroPhai + 1);
            return ketQua;
        } else if (tong < target) {
            conTroTrai = conTroTrai + 1;
        } else {
            conTroPhai = conTroPhai - 1;
        }
    }

    return {};
}
```

#### PYTHON
```python
def two_sum_sorted(numbers, target):
    con_tro_trai = 0
    con_tro_phai = len(numbers) - 1

    while con_tro_trai < con_tro_phai:
        tong = numbers[con_tro_trai] + numbers[con_tro_phai]
        if tong == target:
            return [con_tro_trai + 1, con_tro_phai + 1]
        elif tong < target:
            con_tro_trai = con_tro_trai + 1
        else:
            con_tro_phai = con_tro_phai - 1

    return []
```
</details>

<details>
<summary>Lời giải Bài 2 — Valid Palindrome</summary>

#### C++
```cpp
bool isValidPalindrome(string s) {
    int conTroTrai = 0;
    int conTroPhai = (int)s.size() - 1;

    while (conTroTrai < conTroPhai) {
        while (conTroTrai < conTroPhai && !isalnum(s[conTroTrai])) {
            conTroTrai = conTroTrai + 1;
        }
        while (conTroTrai < conTroPhai && !isalnum(s[conTroPhai])) {
            conTroPhai = conTroPhai - 1;
        }

        char kyTuTrai = tolower(s[conTroTrai]);
        char kyTuPhai = tolower(s[conTroPhai]);

        if (kyTuTrai != kyTuPhai) {
            return false;
        }

        conTroTrai = conTroTrai + 1;
        conTroPhai = conTroPhai - 1;
    }

    return true;
}
```

#### PYTHON
```python
def is_valid_palindrome(s):
    con_tro_trai = 0
    con_tro_phai = len(s) - 1

    while con_tro_trai < con_tro_phai:
        while con_tro_trai < con_tro_phai and not s[con_tro_trai].isalnum():
            con_tro_trai = con_tro_trai + 1
        while con_tro_trai < con_tro_phai and not s[con_tro_phai].isalnum():
            con_tro_phai = con_tro_phai - 1

        ky_tu_trai = s[con_tro_trai].lower()
        ky_tu_phai = s[con_tro_phai].lower()

        if ky_tu_trai != ky_tu_phai:
            return False

        con_tro_trai = con_tro_trai + 1
        con_tro_phai = con_tro_phai - 1

    return True
```
</details>

<details>
<summary>Lời giải Bài 3 — Container With Most Water</summary>

**Ý tưởng:** dùng 2 con trỏ từ 2 đầu, luôn di chuyển con trỏ có chiều cao **thấp hơn** vào trong — vì cột thấp hơn chính là yếu tố giới hạn lượng nước, giữ nguyên nó chỉ làm giảm chiều rộng mà không cải thiện được gì.

#### C++
```cpp
long long maxArea(vector<int> chieuCao) {
    int conTroTrai = 0;
    int conTroPhai = (int)chieuCao.size() - 1;
    long long dienTichLonNhat = 0;

    while (conTroTrai < conTroPhai) {
        int chieuCaoThapHon = min(chieuCao[conTroTrai], chieuCao[conTroPhai]);
        int chieuRong = conTroPhai - conTroTrai;
        long long dienTichHienTai = (long long)chieuCaoThapHon * (long long)chieuRong;

        if (dienTichHienTai > dienTichLonNhat) {
            dienTichLonNhat = dienTichHienTai;
        }

        if (chieuCao[conTroTrai] < chieuCao[conTroPhai]) {
            conTroTrai = conTroTrai + 1;
        } else {
            conTroPhai = conTroPhai - 1;
        }
    }

    return dienTichLonNhat;
}
```

#### PYTHON
```python
def max_area(chieu_cao):
    con_tro_trai = 0
    con_tro_phai = len(chieu_cao) - 1
    dien_tich_lon_nhat = 0

    while con_tro_trai < con_tro_phai:
        chieu_cao_thap_hon = min(chieu_cao[con_tro_trai], chieu_cao[con_tro_phai])
        chieu_rong = con_tro_phai - con_tro_trai
        dien_tich_hien_tai = chieu_cao_thap_hon * chieu_rong

        if dien_tich_hien_tai > dien_tich_lon_nhat:
            dien_tich_lon_nhat = dien_tich_hien_tai

        if chieu_cao[con_tro_trai] < chieu_cao[con_tro_phai]:
            con_tro_trai = con_tro_trai + 1
        else:
            con_tro_phai = con_tro_phai - 1

    return dien_tich_lon_nhat
```
</details>

<details>
<summary>Lời giải Bài 4 — 3Sum</summary>

Giống hệt Ví dụ 3, mục 6.1, hàm `timBoBaCoTongBangKhong`/`tim_bo_ba_co_tong_bang_khong`.
</details>

<details>
<summary>Lời giải Bài 5 — Sort Colors</summary>

**Ý tưởng:** dùng 3 con trỏ (Dutch National Flag Algorithm) — `thap` đánh dấu biên số 0, `giua` duyệt qua mảng, `cao` đánh dấu biên số 2.

#### C++
```cpp
void sortColors(vector<int> &a) {
    int conTroThap = 0;
    int conTroGiua = 0;
    int conTroCao = (int)a.size() - 1;

    while (conTroGiua <= conTroCao) {
        if (a[conTroGiua] == 0) {
            swap(a[conTroThap], a[conTroGiua]);
            conTroThap = conTroThap + 1;
            conTroGiua = conTroGiua + 1;
        } else if (a[conTroGiua] == 1) {
            conTroGiua = conTroGiua + 1;
        } else {
            swap(a[conTroGiua], a[conTroCao]);
            conTroCao = conTroCao - 1;
            // Không tăng conTroGiua ở đây, vì phần tử vừa đổi từ conTroCao về chưa được kiểm tra
        }
    }
}
```

#### PYTHON
```python
def sort_colors(a):
    con_tro_thap = 0
    con_tro_giua = 0
    con_tro_cao = len(a) - 1

    while con_tro_giua <= con_tro_cao:
        if a[con_tro_giua] == 0:
            a[con_tro_thap], a[con_tro_giua] = a[con_tro_giua], a[con_tro_thap]
            con_tro_thap = con_tro_thap + 1
            con_tro_giua = con_tro_giua + 1
        elif a[con_tro_giua] == 1:
            con_tro_giua = con_tro_giua + 1
        else:
            a[con_tro_giua], a[con_tro_cao] = a[con_tro_cao], a[con_tro_giua]
            con_tro_cao = con_tro_cao - 1
```
</details>

<details>
<summary>Lời giải Bài 6 — Maximum Average Subarray I</summary>

Giống hệt Ví dụ 1, mục 6.2, hàm `timTrungBinhLonNhat`/`tim_trung_binh_lon_nhat`.
</details>

<details>
<summary>Lời giải Bài 7 — Grumpy Bookstore Owner</summary>

**Ý tưởng:** tính số khách hàng đã hài lòng sẵn (không cáu gắt) trước; sau đó dùng cửa sổ trượt cố định kích thước k để tìm đoạn "cứu được nhiều khách thêm nhất" (đoạn mà chủ cửa hàng đang cáu gắt).

#### C++
```cpp
int maxSatisfied(vector<int> khachHang, vector<int> cauGat, int k) {
    int n = (int)khachHang.size();

    int khachHaiLongCoSan = 0;
    for (int i = 0; i < n; i++) {
        if (cauGat[i] == 0) {
            khachHaiLongCoSan = khachHaiLongCoSan + khachHang[i];
        }
    }

    int khachCuuDuocTrongCuaSo = 0;
    for (int i = 0; i < k; i++) {
        if (cauGat[i] == 1) {
            khachCuuDuocTrongCuaSo = khachCuuDuocTrongCuaSo + khachHang[i];
        }
    }

    int khachCuuDuocLonNhat = khachCuuDuocTrongCuaSo;

    for (int i = k; i < n; i++) {
        if (cauGat[i] == 1) {
            khachCuuDuocTrongCuaSo = khachCuuDuocTrongCuaSo + khachHang[i];
        }
        if (cauGat[i - k] == 1) {
            khachCuuDuocTrongCuaSo = khachCuuDuocTrongCuaSo - khachHang[i - k];
        }
        if (khachCuuDuocTrongCuaSo > khachCuuDuocLonNhat) {
            khachCuuDuocLonNhat = khachCuuDuocTrongCuaSo;
        }
    }

    return khachHaiLongCoSan + khachCuuDuocLonNhat;
}
```

#### PYTHON
```python
def max_satisfied(khach_hang, cau_gat, k):
    n = len(khach_hang)

    khach_hai_long_co_san = 0
    for i in range(n):
        if cau_gat[i] == 0:
            khach_hai_long_co_san = khach_hai_long_co_san + khach_hang[i]

    khach_cuu_duoc_trong_cua_so = 0
    for i in range(k):
        if cau_gat[i] == 1:
            khach_cuu_duoc_trong_cua_so = khach_cuu_duoc_trong_cua_so + khach_hang[i]

    khach_cuu_duoc_lon_nhat = khach_cuu_duoc_trong_cua_so

    for i in range(k, n):
        if cau_gat[i] == 1:
            khach_cuu_duoc_trong_cua_so = khach_cuu_duoc_trong_cua_so + khach_hang[i]
        if cau_gat[i - k] == 1:
            khach_cuu_duoc_trong_cua_so = khach_cuu_duoc_trong_cua_so - khach_hang[i - k]
        if khach_cuu_duoc_trong_cua_so > khach_cuu_duoc_lon_nhat:
            khach_cuu_duoc_lon_nhat = khach_cuu_duoc_trong_cua_so

    return khach_hai_long_co_san + khach_cuu_duoc_lon_nhat
```
</details>

<details>
<summary>Lời giải Bài 8 — Longest Substring Without Repeating Characters</summary>

Giống hệt Ví dụ 1, mục 6.3, hàm `doDaiDoanKhongLapKyTu`/`do_dai_doan_khong_lap_ky_tu`.
</details>

<details>
<summary>Lời giải Bài 9 — Fruit Into Baskets</summary>

**Ý tưởng:** đây thực chất là bài "đoạn con dài nhất chứa tối đa 2 loại phần tử khác nhau" — dùng cửa sổ trượt thay đổi kết hợp bảng đếm tần suất, co cửa sổ khi số loại vượt quá 2.

#### C++
```cpp
int totalFruit(vector<int> loaiTraiCay) {
    unordered_map<int, int> tanSuat;
    int conTroTrai = 0;
    int doDaiLonNhat = 0;

    for (int conTroPhai = 0; conTroPhai < (int)loaiTraiCay.size(); conTroPhai++) {
        tanSuat[loaiTraiCay[conTroPhai]] = tanSuat[loaiTraiCay[conTroPhai]] + 1;

        while ((int)tanSuat.size() > 2) {
            int loaiCanGiam = loaiTraiCay[conTroTrai];
            tanSuat[loaiCanGiam] = tanSuat[loaiCanGiam] - 1;
            if (tanSuat[loaiCanGiam] == 0) {
                tanSuat.erase(loaiCanGiam);
            }
            conTroTrai = conTroTrai + 1;
        }

        int doDaiHienTai = conTroPhai - conTroTrai + 1;
        if (doDaiHienTai > doDaiLonNhat) {
            doDaiLonNhat = doDaiHienTai;
        }
    }

    return doDaiLonNhat;
}
```

#### PYTHON
```python
def total_fruit(loai_trai_cay):
    tan_suat = {}
    con_tro_trai = 0
    do_dai_lon_nhat = 0

    for con_tro_phai in range(len(loai_trai_cay)):
        loai_hien_tai = loai_trai_cay[con_tro_phai]
        if loai_hien_tai in tan_suat:
            tan_suat[loai_hien_tai] = tan_suat[loai_hien_tai] + 1
        else:
            tan_suat[loai_hien_tai] = 1

        while len(tan_suat) > 2:
            loai_can_giam = loai_trai_cay[con_tro_trai]
            tan_suat[loai_can_giam] = tan_suat[loai_can_giam] - 1
            if tan_suat[loai_can_giam] == 0:
                del tan_suat[loai_can_giam]
            con_tro_trai = con_tro_trai + 1

        do_dai_hien_tai = con_tro_phai - con_tro_trai + 1
        if do_dai_hien_tai > do_dai_lon_nhat:
            do_dai_lon_nhat = do_dai_hien_tai

    return do_dai_lon_nhat
```
</details>

<details>
<summary>Lời giải Bài 10 — Minimum Size Subarray Sum</summary>

Giống hệt bài toán chính, mục 6.3, hàm `timDoDaiNganNhatCoTongLonHonBangS`/`tim_do_dai_ngan_nhat_co_tong_lon_hon_bang_s`.
</details>

<details>
<summary>Lời giải Bài 11 — Subarray Product Less Than K</summary>

**Ý tưởng:** dùng cửa sổ trượt thay đổi, duy trì tích của cửa sổ hiện tại, co cửa sổ khi tích vượt quá k. Số lượng đoạn con thoả điều kiện kết thúc tại `conTroPhai` chính là `conTroPhai - conTroTrai + 1` (tương tự bài tập minh hoạ, mục 6.3).

#### C++
```cpp
int numSubarrayProductLessThanK(vector<int> a, int k) {
    if (k <= 1) {
        return 0; // vì mọi phần tử dương đều >= 1, tích không thể nhỏ hơn 1
    }

    int conTroTrai = 0;
    long long tichHienTai = 1;
    int soLuongDoan = 0;

    for (int conTroPhai = 0; conTroPhai < (int)a.size(); conTroPhai++) {
        tichHienTai = tichHienTai * a[conTroPhai];

        while (tichHienTai >= k) {
            tichHienTai = tichHienTai / a[conTroTrai];
            conTroTrai = conTroTrai + 1;
        }

        soLuongDoan = soLuongDoan + (conTroPhai - conTroTrai + 1);
    }

    return soLuongDoan;
}
```

#### PYTHON
```python
def num_subarray_product_less_than_k(a, k):
    if k <= 1:
        return 0

    con_tro_trai = 0
    tich_hien_tai = 1
    so_luong_doan = 0

    for con_tro_phai in range(len(a)):
        tich_hien_tai = tich_hien_tai * a[con_tro_phai]

        while tich_hien_tai >= k:
            tich_hien_tai = tich_hien_tai // a[con_tro_trai]
            con_tro_trai = con_tro_trai + 1

        so_luong_doan = so_luong_doan + (con_tro_phai - con_tro_trai + 1)

    return so_luong_doan
```
</details>

<details>
<summary>Lời giải Bài 12 — Daily Temperatures</summary>

Giống hệt Ví dụ 1, mục 6.4, hàm `tinhSoNgayChoAmHon`/`tinh_so_ngay_cho_am_hon`.
</details>

<details>
<summary>Lời giải Bài 13 — Largest Rectangle in Histogram</summary>

Giống hệt Ví dụ 2, mục 6.4, hàm `timDienTichHinhChuNhatLonNhat`/`tim_dien_tich_hinh_chu_nhat_lon_nhat`.
</details>

<details>
<summary>Lời giải Bài 14 — Trapping Rain Water</summary>

**Ý tưởng:** dùng 2 con trỏ từ 2 đầu, duy trì chiều cao lớn nhất đã gặp ở mỗi bên. Nước đọng tại một vị trí bằng `min(chiều cao lớn nhất bên trái, chiều cao lớn nhất bên phải) - chiều cao tại vị trí đó`.

#### C++
```cpp
long long trap(vector<int> chieuCao) {
    int conTroTrai = 0;
    int conTroPhai = (int)chieuCao.size() - 1;
    int chieuCaoLonNhatTrai = 0;
    int chieuCaoLonNhatPhai = 0;
    long long tongNuocDong = 0;

    while (conTroTrai < conTroPhai) {
        if (chieuCao[conTroTrai] < chieuCao[conTroPhai]) {
            if (chieuCao[conTroTrai] >= chieuCaoLonNhatTrai) {
                chieuCaoLonNhatTrai = chieuCao[conTroTrai];
            } else {
                tongNuocDong = tongNuocDong + (chieuCaoLonNhatTrai - chieuCao[conTroTrai]);
            }
            conTroTrai = conTroTrai + 1;
        } else {
            if (chieuCao[conTroPhai] >= chieuCaoLonNhatPhai) {
                chieuCaoLonNhatPhai = chieuCao[conTroPhai];
            } else {
                tongNuocDong = tongNuocDong + (chieuCaoLonNhatPhai - chieuCao[conTroPhai]);
            }
            conTroPhai = conTroPhai - 1;
        }
    }

    return tongNuocDong;
}
```

#### PYTHON
```python
def trap(chieu_cao):
    con_tro_trai = 0
    con_tro_phai = len(chieu_cao) - 1
    chieu_cao_lon_nhat_trai = 0
    chieu_cao_lon_nhat_phai = 0
    tong_nuoc_dong = 0

    while con_tro_trai < con_tro_phai:
        if chieu_cao[con_tro_trai] < chieu_cao[con_tro_phai]:
            if chieu_cao[con_tro_trai] >= chieu_cao_lon_nhat_trai:
                chieu_cao_lon_nhat_trai = chieu_cao[con_tro_trai]
            else:
                tong_nuoc_dong = tong_nuoc_dong + (chieu_cao_lon_nhat_trai - chieu_cao[con_tro_trai])
            con_tro_trai = con_tro_trai + 1
        else:
            if chieu_cao[con_tro_phai] >= chieu_cao_lon_nhat_phai:
                chieu_cao_lon_nhat_phai = chieu_cao[con_tro_phai]
            else:
                tong_nuoc_dong = tong_nuoc_dong + (chieu_cao_lon_nhat_phai - chieu_cao[con_tro_phai])
            con_tro_phai = con_tro_phai - 1

    return tong_nuoc_dong
```
</details>

<details>
<summary>Lời giải Bài 15 — Sliding Window Maximum</summary>

Giống hệt bài toán chính, mục 6.5, hàm `timGiaTriLonNhatMoiCuaSo`/`tim_gia_tri_lon_nhat_moi_cua_so`.
</details>

<details>
<summary>Lời giải Bài 16 — Shortest Subarray with Sum at Least K</summary>

**Ý tưởng:** Vì mảng có thể có số âm, ta không dùng cửa sổ trượt thuần tuý mà kết hợp mảng cộng dồn với monotonic deque: với mỗi vị trí `i`, ta cần tìm vị trí `j < i` sao cho `prefix[i] - prefix[j] >= K` và `i - j` nhỏ nhất. Duy trì deque các chỉ số `j` sao cho `prefix[j]` **tăng dần** — khi `prefix[i] - prefix[deque.front()] >= K`, ta ghi nhận kết quả và pop đầu (vì các `i` lớn hơn sau này sẽ luôn ưu tiên `j` lớn hơn, gần hơn). Đồng thời loại các chỉ số ở đuôi có `prefix` lớn hơn hoặc bằng `prefix[i]` (vì chúng vừa xa hơn vừa không tốt hơn).

#### C++
```cpp
int shortestSubarray(vector<int> a, int K) {
    int n = (int)a.size();
    vector<long long> prefix(n + 1, 0);
    for (int i = 0; i < n; i++) {
        prefix[i + 1] = prefix[i] + a[i];
    }

    deque<int> hangDoi; // lưu chỉ số, prefix tương ứng tăng dần từ đầu đến cuối
    int doDaiNganNhat = n + 1;

    for (int i = 0; i <= n; i++) {
        while (!hangDoi.empty() && prefix[i] - prefix[hangDoi.front()] >= K) {
            int doDaiHienTai = i - hangDoi.front();
            if (doDaiHienTai < doDaiNganNhat) {
                doDaiNganNhat = doDaiHienTai;
            }
            hangDoi.pop_front();
        }

        while (!hangDoi.empty() && prefix[hangDoi.back()] >= prefix[i]) {
            hangDoi.pop_back();
        }

        hangDoi.push_back(i);
    }

    if (doDaiNganNhat == n + 1) {
        return -1;
    }
    return doDaiNganNhat;
}
```

#### PYTHON
```python
from collections import deque


def shortest_subarray(a, K):
    n = len(a)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + a[i]

    hang_doi = deque()
    do_dai_ngan_nhat = n + 1

    for i in range(n + 1):
        while len(hang_doi) > 0 and prefix[i] - prefix[hang_doi[0]] >= K:
            do_dai_hien_tai = i - hang_doi[0]
            if do_dai_hien_tai < do_dai_ngan_nhat:
                do_dai_ngan_nhat = do_dai_hien_tai
            hang_doi.popleft()

        while len(hang_doi) > 0 and prefix[hang_doi[-1]] >= prefix[i]:
            hang_doi.pop()

        hang_doi.append(i)

    if do_dai_ngan_nhat == n + 1:
        return -1
    return do_dai_ngan_nhat
```
</details>

<details>
<summary>Lời giải Bài 17 — Constrained Subsequence Sum</summary>

**Ý tưởng:** Đây là mở rộng của Ví dụ 3, mục 6.5. Đặt `dp[i]` là tổng lớn nhất của dãy con kết thúc tại vị trí i (bắt buộc chọn i). Công thức: `dp[i] = a[i] + max(0, dp[j])` với j trong khoảng `[i-k, i-1]`. Dùng monotonic deque để duy trì max của dp trong cửa sổ kích thước k.

#### C++
```cpp
long long constrainedSubsetSum(vector<int> a, int k) {
    int n = (int)a.size();
    vector<long long> dp(n, 0);
    deque<int> hangDoi; // lưu chỉ số, dp tương ứng giảm dần từ đầu đến cuối

    long long ketQuaTotNhat = LLONG_MIN;

    for (int i = 0; i < n; i++) {
        while (!hangDoi.empty() && hangDoi.front() < i - k) {
            hangDoi.pop_front();
        }

        long long giaTriTotNhatTruocDo = 0;
        if (!hangDoi.empty() && dp[hangDoi.front()] > 0) {
            giaTriTotNhatTruocDo = dp[hangDoi.front()];
        }

        dp[i] = a[i] + giaTriTotNhatTruocDo;

        if (dp[i] > ketQuaTotNhat) {
            ketQuaTotNhat = dp[i];
        }

        while (!hangDoi.empty() && dp[hangDoi.back()] < dp[i]) {
            hangDoi.pop_back();
        }
        hangDoi.push_back(i);
    }

    return ketQuaTotNhat;
}
```

#### PYTHON
```python
from collections import deque


def constrained_subset_sum(a, k):
    n = len(a)
    dp = [0] * n
    hang_doi = deque()

    ket_qua_tot_nhat = float('-inf')

    for i in range(n):
        while len(hang_doi) > 0 and hang_doi[0] < i - k:
            hang_doi.popleft()

        gia_tri_tot_nhat_truoc_do = 0
        if len(hang_doi) > 0 and dp[hang_doi[0]] > 0:
            gia_tri_tot_nhat_truoc_do = dp[hang_doi[0]]

        dp[i] = a[i] + gia_tri_tot_nhat_truoc_do

        if dp[i] > ket_qua_tot_nhat:
            ket_qua_tot_nhat = dp[i]

        while len(hang_doi) > 0 and dp[hang_doi[-1]] < dp[i]:
            hang_doi.pop()
        hang_doi.append(i)

    return ket_qua_tot_nhat
```
</details>

<details>
<summary>Lời giải Bài 18 — Jump Game VI</summary>

**Ý tưởng:** Đặt `dp[i]` là tổng điểm lớn nhất khi đến được vị trí i. Công thức: `dp[i] = diem[i] + max(dp[j])` với j trong khoảng `[i-k, i-1]`. Dùng monotonic deque để duy trì max trong cửa sổ kích thước k — gần giống hệt cấu trúc của Ví dụ 3, mục 6.5.

#### C++
```cpp
long long maxResult(vector<int> diem, int k) {
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
```

#### PYTHON
```python
from collections import deque


def max_result(diem, k):
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
```
</details>
