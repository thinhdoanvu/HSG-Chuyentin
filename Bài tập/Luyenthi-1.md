# Một số bài luyện thi Olympic Tin học

*Tổng hợp bởi đội tuyển Olympic Chuyên Tin 2011, Khoa Công nghệ thông tin — Đại học Hàng Hải, phục vụ ôn luyện Olympic Chuyên Tin năm 2012. Nguồn bài từ trang giải bài trực tuyến SPOJ VN (`vn.spoj.pl`), các đề thi Olympic năm trước, và một số bài đề Chuyên tin khác.*

> 💡 **Cách dùng file này:** mỗi bài có phần **Đề bài** hiển thị sẵn để bạn tự suy nghĩ, còn phần **Lời giải & Code (C++ và Python)** được đặt trong khối gập — bấm vào tiêu đề `Xem lời giải` để mở ra.

## Bảng đối chiếu với 22 chương chuyên đề

| # | Bài | Kỹ thuật cốt lõi | Chương chính | Chương phụ trợ |
|---|---|---|---|---|
| I.1 | Số phong phú | Sàng kiểu Eratosthenes cho tổng ước | **17. Số học cơ bản** (17.1) | — |
| I.2 | Tìm số nguyên tố | Sàng Eratosthenes | **17. Số học cơ bản** (17.1) | — |
| I.3 | Số thân thiện | UCLN, đảo số | **17. Số học cơ bản** (17.2) | 3. Cấu trúc dữ liệu cơ bản |
| I.4 | Euler Totient Function | Phân tích thừa số + công thức Ơ-le | **19. Toán học nâng cao** (19.5) | 17. Số học cơ bản |
| II.1 | First Number | Ghép chuỗi, tìm chuỗi con | **3. Cấu trúc dữ liệu cơ bản** (string STL) | — |
| II.2 | Word Counting | Duyệt mảng đếm đoạn liên tiếp bằng nhau | **3. Cấu trúc dữ liệu cơ bản** | 6. Hai con trỏ & Cửa sổ trượt |
| II.3 | Tập số | Sinh xâu con + `set` loại trùng | **3. Cấu trúc dữ liệu cơ bản** (`set<string>`) | — |
| III.1 | Gặm cỏ | BFS trên lưới ô vuông | **11. Nền tảng đồ thị** (11.2) | — |
| III.2 | Bãi cỏ ngon nhất | Đếm thành phần liên thông (BFS/DFS) | **11. Nền tảng đồ thị** (11.2) | — |
| III.3 | Nước lạnh | Duyệt cây nhị phân (DFS) | **11. Nền tảng đồ thị** (11.2) | — |
| III.4 | Hexgame | BFS trên đồ thị trạng thái ẩn | **11. Nền tảng đồ thị** (11.2, nâng cao) | — |
| III.5 | Xây dựng thành phố | Cây khung nhỏ nhất — Prim | **13. Cây khung & DSU** (13.3) | — |
| III.6 | Đến trường | Dijkstra + đếm số đường đi ngắn nhất | **12. Đường đi ngắn nhất** (12.1) | — |
| IV.1 | Lát gạch | DP Fibonacci + cộng số lớn | **8. Quy hoạch động cơ bản** (8.2) | — |
| IV.2 | Lát gạch 3 | DP truy hồi rút gọn | **8. Quy hoạch động cơ bản** (8.2) | — |
| IV.3 | Bậc thang | DP Fibonacci có ràng buộc | **8. Quy hoạch động cơ bản** (8.2) | — |
| IV.4 | Đường đi tổng lớn nhất | DP 2 chiều trên bảng | **8. Quy hoạch động cơ bản** (8.3) | — |
| V.1 | Đấu giá | Duyệt + kiểm tra nguyên tố/đối xứng | **17. Số học cơ bản** (17.1) | 1. Nhập môn |
| V.2 | Trông xe | Đếm theo chỉ số (bucket counting) | **5. Mảng cộng dồn & Mảng hiệu** | 3. Cấu trúc dữ liệu cơ bản |
| V.3 | Cây nhị phân tìm kiếm | Kiểm tra tính chất BST bằng khoảng giá trị | **11. Nền tảng đồ thị** (cây là đồ thị đặc biệt) | — |
| V.4 | Cây khung nhỏ nhất | Kruskal + DSU | **13. Cây khung & DSU** (13.1, 13.2) | — |
| V.5 | Khối lượng phân tử | Xử lý biểu thức lồng nhau bằng Stack | **3. Cấu trúc dữ liệu cơ bản** (3.2 Stack) | — |

## Mục lục

- [Phần I. Số học](#phần-i-số-học)
- [Phần II. Xử lý chuỗi](#phần-ii-xử-lý-chuỗi)
- [Phần III. Đồ thị](#phần-iii-đồ-thị)
- [Phần IV. Quy hoạch động](#phần-iv-quy-hoạch-động)
- [Phần V. Các bài toán khác](#phần-v-các-bài-toán-khác)

---

## Phần I. Số học

### Bài 1. Số phong phú
**Chương:** 17. Số học cơ bản (17.1 Sàng nguyên tố) — **Nguồn:** SPOJ VN — NKABD

**Đề bài:** Một số nguyên dương được gọi là *số phong phú* nếu tổng các ước số của nó (không tính chính nó) lớn hơn chính số đó. Ví dụ, 12 có tổng các ước (trừ 12) là $1+2+3+4+6=16>12$, nên 12 là số phong phú.

Hãy đếm số lượng số phong phú trong đoạn $[L, R]$.

**Dữ liệu:** Hai số nguyên $L, R$ ($1 \le L \le R \le 10^5$).

**Kết quả:** Một số nguyên — số lượng số phong phú trong $[L,R]$.

**Ví dụ:**

| Dữ liệu | Kết quả | Giải thích |
|---|---|---|
| `1 50` | `9` | Các số phong phú từ 1-50: 12, 18, 20, 24, 30, 36, 40, 42, 48 |

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

**Ý tưởng chính:** thay vì kiểm tra từng số một cách độc lập (duyệt ước của từng số tốn $O(\sqrt{a})$, tổng $O(R\sqrt{R})$), ta áp dụng tư tưởng giống **sàng số nguyên tố**: với mỗi số $i$ từ 2 đến $\sqrt{R}$, cộng $i$ (và $j$ tương ứng) vào tổng-ước của mọi bội số $i \times j$. Cách này tính tổng ước của **toàn bộ** các số trong đoạn chỉ với độ phức tạp gần $O(R\log R)$.


**C++**

```cpp
#include <stdio.h>
#include <math.h>

int tongUoc[100001];

int main() {
    int l, r, i, j, dem = 0;
    scanf("%d%d", &l, &r);

    for (i = 1; i <= r; i++) tongUoc[i] = 1;  // moi so co uoc 1

    int can = (int)sqrt(r);
    for (i = 2; i <= can; i++) {
        tongUoc[i * i] += i;
        for (j = i + 1; j * i <= r; j++)
            tongUoc[j * i] += i + j;
    }

    for (i = l; i <= r; i++)
        if (tongUoc[i] > i) dem++;

    printf("%d", dem);
    return 0;
}
```

**Python** (đơn giản, dễ đọc, không tối ưu quá mức)

```python
import sys

def solve():
    l, r = map(int, sys.stdin.read().split())

    # Buoc 1: khoi tao mang tong uoc, ban dau moi so co uoc 1
    tong_uoc = [1] * (r + 1)

    # Buoc 2: giong y tuong sang nguyen to, "gieo" anh huong cua i len moi boi cua i
    can_bac_hai = int(r ** 0.5)
    for i in range(2, can_bac_hai + 1):
        tong_uoc[i * i] += i
        j = i + 1
        while j * i <= r:
            tong_uoc[j * i] += i + j
            j += 1

    # Buoc 3: dem cac so phong phu trong doan [l, r]
    dem = 0
    for so in range(l, r + 1):
        if tong_uoc[so] > so:
            dem += 1

    print(dem)

solve()
```

```
# Phiên bản học sinh:
import math

def solve(l,r):
    f = [1]*(r+1)
    print(f)
    c = int(math.sqrt(r))
    print(c)
    
    for i in range(2,c+1,1):
        for j in range(i, r//i + 1, 1):
            if i==j:
                f[i*j] += i 
            else:
                f[i*j] += i+j 
    print(f)
    
    # dem xem co bao nhieu so phong phu
    dem=0
    for i in range(r):
        if f[i] > i:
            dem+=1
    print(dem)
    
if __name__=="__main__":
    l,r = map(int, input().split())
    # print(l,r)
    solve(l,r)
```

**Bài học:** đây là ví dụ điển hình của việc chuyển từ "kiểm tra từng phần tử độc lập" sang "gieo ảnh hưởng của từng ước lên tất cả bội của nó" — cùng tư tưởng với sàng Eratosthenes.

</details>

---

### Bài 2. Tìm số nguyên tố trong đoạn
**Chương:** 17. Số học cơ bản (17.1 Sàng nguyên tố) — **Nguồn:** SPOJ VN — PNUMBER

**Đề bài:** Tìm tất cả các số nguyên tố trong đoạn $[A,B]$.

**Dữ liệu:** Hai số nguyên $A, B$ cách nhau một dấu cách ($1 \le A \le B \le 200000$).

**Kết quả:** In ra tất cả số nguyên tố trong $[A,B]$, mỗi số một dòng.

**Ví dụ:**

| Input | Output |
|---|---|
| `1 10` | `2`<br>`3`<br>`5`<br>`7` |

⏱️ Time limit: 5s — đây là bài dạng ACM (yêu cầu đúng toàn bộ test, không tính điểm từng phần).

<details>
<summary>💡 Xem lời giải</summary>

**Cách hiệu quả — Sàng nguyên tố:** đánh dấu hợp số bằng cách, với mỗi số nguyên tố $i \le \sqrt{B}$, đánh dấu mọi bội $i \times j$ ($j$ từ $i$ đến $B/i$) là hợp số.

**C++**

```cpp
#include <stdio.h>
#include <math.h>

char daDanhDau[200001];

void sang(int n) {
    int can = (int)sqrt(n);
    daDanhDau[0] = daDanhDau[1] = 1;
    for (int i = 2; i <= can; i++) {
        if (daDanhDau[i] == 0) {
            for (int j = i; (long long)j * i <= n; j++)
                daDanhDau[j * i] = 1;
        }
    }
}

int main() {
    int a, b;
    scanf("%d%d", &a, &b);
    sang(b);
    for (int i = a; i <= b; i++)
        if (daDanhDau[i] == 0) printf("%d\n", i);
    return 0;
}
```

**Python**

```python
import sys

def solve():
    a, b = map(int, sys.stdin.read().split())

    # Buoc 1: khoi tao mang danh dau, gia su moi so tu 0..b deu la nguyen to
    la_hop_so = [False] * (b + 1)
    if b >= 0:
        la_hop_so[0] = True
    if b >= 1:
        la_hop_so[1] = True

    # Buoc 2: sang Eratosthenes
    can_bac_hai = int(b ** 0.5)
    for i in range(2, can_bac_hai + 1):
        if la_hop_so[i] == False:
            j = i
            while j * i <= b:
                la_hop_so[j * i] = True
                j += 1

    # Buoc 3: in ra cac so nguyen to trong doan [a, b]
    ket_qua = []
    for so in range(a, b + 1):
        if la_hop_so[so] == False:
            ket_qua.append(str(so))

    print("\n".join(ket_qua))

solve()
```
```
# Phiên bản học sinh
import math

def sangnt(l,r):
    f = [1]*(r+1)
    # print(f)
    f[0] = f[1] = 0
    for i in range(2,int(math.sqrt(r))+1):
        for j in range(i, r//i + 1):
            f[i*j] = 0
            
    # dem so luong so nguyen to trong khoang l,try:
    dem = 0
    for i in range(l,r+1):
        if f[i]==1:
            dem+=1
    print(dem)
    
if __name__=="__main__":
    l,r = map(int, input().split())
    # print(l,r)
    sangnt(l,r)
```

**Bài học:** đây là minh hoạ chuẩn cho kỹ thuật **Sàng Eratosthenes** (thuộc Chương 17 "Số học cơ bản").

</details>

---

### Bài 3. Số thân thiện
**Chương:** 17. Số học cơ bản (17.2 UCLN/BCNN) — **Nguồn:** SPOJ VN — NKNUMFRE

**Đề bài:** Với một số tự nhiên $n$, gọi $rev(n)$ là số thu được khi đảo ngược các chữ số của $n$. Nếu $\gcd(n, rev(n)) = 1$ thì cả $n$ và $rev(n)$ đều được gọi là **số thân thiện**.

Cho hai số $a, b$ ($10 \le a \le b \le 30000$). Đếm số lượng số thân thiện trong đoạn $[a,b]$.

**Dữ liệu:** Một dòng chứa $a, b$ cách nhau một khoảng trắng.

**Kết quả:** Một số nguyên — kết quả bài toán.

**Ví dụ:**

| Dữ liệu | Kết quả |
|---|---|
| `20 30` | `3` |

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

**Các bước:**
1. Viết hàm đảo ngược một số nguyên.
2. Viết hàm tính $\gcd$ (thuật toán Euclid).
3. Với mỗi số $i$ trong $[a,b]$ chưa xét: tính $k = rev(i)$; nếu $\gcd(i,k)=1$ thì cả $i$ và $k$ (nếu $k$ cũng nằm trong $[a,b]$) đều được tính — cần mảng đánh dấu để không đếm trùng.

**C++**

```cpp
#include <stdio.h>

int daoNguoc(int n) {
    int chuSo[10], k = 0;
    while (n != 0) {
        chuSo[k++] = n % 10;
        n /= 10;
    }
    int ketQua = 0, luyThua = 1;
    for (int i = k - 1; i >= 0; i--) {
        ketQua += chuSo[i] * luyThua;
        luyThua *= 10;
    }
    return ketQua;
}

int ucln(int a, int b) {
    while (b != 0) {
        int t = b;
        b = a % b;
        a = t;
    }
    return a;
}

char daXet[30001];

int main() {
    int a, b, dem = 0;
    scanf("%d%d", &a, &b);

    for (int i = a; i <= b; i++) {
        if (daXet[i]) continue;
        int k = daoNguoc(i);
        if (ucln(i, k) == 1) {
            daXet[i] = 1;
            dem++;
            if (a <= k && k <= b && !daXet[k]) {
                daXet[k] = 1;
                dem++;
            }
        }
    }
    printf("%d", dem);
    return 0;
}
```

**Python**

```python
import sys

def dao_nguoc(n):
    chu_so = []
    while n != 0:
        chu_so.append(n % 10)
        n = n // 10

    ket_qua = 0
    luy_thua = 1
    for i in range(len(chu_so) - 1, -1, -1):
        ket_qua = ket_qua + chu_so[i] * luy_thua
        luy_thua = luy_thua * 10
    return ket_qua

def ucln(a, b):
    while b != 0:
        du = a % b
        a = b
        b = du
    return a

def solve():
    a, b = map(int, sys.stdin.read().split())

    da_xet = [False] * (b + 1)
    dem = 0

    for i in range(a, b + 1):
        if da_xet[i]:
            continue
        k = dao_nguoc(i)
        if ucln(i, k) == 1:
            da_xet[i] = True
            dem += 1
            if a <= k <= b and da_xet[k] == False:
                da_xet[k] = True
                dem += 1

    print(dem)

solve()
```

</details>

---

### Bài 4. Hàm Ơ-le (Euler Totient Function)
**Chương:** 19. Toán học nâng cao (19.5 Hàm Euler) — **Nguồn:** SPOJ VN — ETF

**Đề bài:** Hàm Ơ-le $\varphi(n)$ đếm số lượng số nguyên dương $\le n$ nguyên tố cùng nhau với $n$.

Cho $T$ test, mỗi test là một số nguyên dương $n$ ($1 \le n \le 10^6$, $T \le 20000$). Tính $\varphi(n)$ cho mỗi test.

**Ví dụ:**

| Input | Output |
|---|---|
| `5`<br>`1`<br>`2`<br>`3`<br>`4`<br>`5` | `1`<br>`1`<br>`2`<br>`2`<br>`4` |

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

**Công thức:** nếu $n = p_1^{a_1} p_2^{a_2} \cdots p_k^{a_k}$ thì

$$\varphi(n) = n \cdot \left(1-\frac{1}{p_1}\right)\left(1-\frac{1}{p_2}\right)\cdots\left(1-\frac{1}{p_k}\right)$$

Chỉ cần phân tích $n$ ra các ước nguyên tố (chia thử đến $\sqrt{n}$), rồi áp dụng công thức trên.

**C++**

```cpp
#include <stdio.h>

long long tinhPhi(int n) {
    long long ketQua = n;
    int m = n;
    for (int p = 2; (long long)p * p <= m; p++) {
        if (m % p == 0) {
            while (m % p == 0) m /= p;
            ketQua -= ketQua / p;
        }
    }
    if (m > 1) ketQua -= ketQua / m;
    return ketQua;
}

int main() {
    int t, n;
    scanf("%d", &t);
    while (t--) {
        scanf("%d", &n);
        printf("%lld\n", tinhPhi(n));
    }
    return 0;
}
```

**Python**

```python
import sys

def tinh_phi(n):
    ket_qua = n
    m = n
    p = 2
    while p * p <= m:
        if m % p == 0:
            while m % p == 0:
                m = m // p
            ket_qua = ket_qua - ket_qua // p
        p += 1
    if m > 1:
        ket_qua = ket_qua - ket_qua // m
    return ket_qua

def solve():
    data = sys.stdin.read().split()
    t = int(data[0])

    ket_qua_list = []
    for i in range(1, t + 1):
        n = int(data[i])
        ket_qua_list.append(str(tinh_phi(n)))

    print("\n".join(ket_qua_list))

solve()
```

> Với $T$ lớn (tới 20000) và $n \le 10^6$, cách hiệu quả hơn là **sàng Ơ-le** tính sẵn $\varphi(n)$ cho mọi $n$ từ 1 đến $10^6$ chỉ một lần, rồi trả lời từng test trong $O(1)$ — kỹ thuật nâng cao hơn đáng để tìm hiểu thêm (liên quan "sàng tuyến tính").

</details>

---

## Phần II. Xử lý chuỗi

### Bài 1. Vị trí xuất hiện đầu tiên
**Chương:** 3. Cấu trúc dữ liệu cơ bản (string STL) — **Nguồn:** SPOJ VN — MDIGITS2

**Đề bài:** Ghép liên tiếp các số thập phân $1, 2, 3,\dots$ ta được dãy: `12345678910111213...`. Tìm vị trí xuất hiện đầu tiên của số $N$ trong dãy này.

**Dữ liệu:** Một số nguyên $N$ ($1 \le N \le 100000$).

**Kết quả:** Vị trí xuất hiện đầu tiên của $N$ trong dãy.

**Ví dụ:**

| Input | Output |
|---|---|
| `15` | `20` |
| `34` | `3` |
| `142` | `73` |

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Bài tập luyện dùng thư viện `string`. Ghép chuỗi biểu diễn các số $1,2,\dots,N$ lại với nhau, rồi tìm vị trí xuất hiện của chuỗi biểu diễn $N$.

**C++**

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    int n;
    cin >> n;

    string day = "";
    for (int i = 1; i <= n; i++)
        day += to_string(i);

    string chuoiN = to_string(n);
    cout << day.find(chuoiN) + 1;
    return 0;
}
```

**Python**

```python
def solve():
    n = int(input())

    # Buoc 1: ghep chuoi cac so tu 1 den n
    day = ""
    for i in range(1, n + 1):
        day = day + str(i)

    # Buoc 2: tim vi tri xuat hien cua chuoi bieu dien n
    chuoi_n = str(n)
    vi_tri = day.find(chuoi_n)   # tra ve chi so bat dau tu 0

    print(vi_tri + 1)   # doi sang chi so bat dau tu 1

solve()
```

</details>

---

### Bài 2. Đếm nhóm từ dài bằng nhau
**Chương:** 3. Cấu trúc dữ liệu cơ bản — **Nguồn:** SPOJ VN — WORDCNT

**Đề bài:** Cho một xâu chỉ gồm chữ cái thường và khoảng trắng, các từ phân tách nhau bởi ít nhất một khoảng trắng. Tìm $P$ — số lượng lớn nhất các từ **có độ dài bằng nhau đứng liên tiếp nhau** trong xâu.

**Dữ liệu:** Dòng đầu là số bộ test $\le 20$; mỗi dòng sau là một xâu (không quá 1000 từ, mỗi từ không quá 20 ký tự).

**Kết quả:** Với mỗi test, in ra giá trị $P$ tương ứng.

**Ví dụ:**

| Input | Output |
|---|---|
| `2`<br>`a aa bb cc def ghi`<br>`a a a a a bb bb bb bb c c` | `3`<br>`5` |

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

**Các bước:**
1. Đọc từng dòng.
2. Duyệt xâu, tách độ dài của từng từ, lưu vào một mảng số nguyên.
3. Tìm đoạn con dài nhất trong mảng đó gồm các phần tử **liên tiếp bằng nhau**.

**C++**

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    int soTest;
    cin >> soTest;
    cin.ignore();

    while (soTest--) {
        string dong;
        getline(cin, dong);

        int doDaiTu[1001];
        int soTu = 0;
        int dem = 0;

        for (size_t i = 0; i <= dong.size(); i++) {
            if (i < dong.size() && dong[i] >= 'a' && dong[i] <= 'z') {
                dem++;
            } else {
                if (dem > 0) {
                    doDaiTu[soTu++] = dem;
                    dem = 0;
                }
            }
        }

        int maxLienTiep = 1, demLienTiep = 1;
        for (int i = 1; i < soTu; i++) {
            if (doDaiTu[i] == doDaiTu[i - 1]) {
                demLienTiep++;
                if (demLienTiep > maxLienTiep) maxLienTiep = demLienTiep;
            } else {
                demLienTiep = 1;
            }
        }

        cout << maxLienTiep << endl;
    }
    return 0;
}
```

**Python**

```python
import sys

def solve():
    lines = sys.stdin.read().split("\n")
    so_test = int(lines[0])

    ket_qua_list = []
    for t in range(1, so_test + 1):
        dong = lines[t]

        # Buoc 1: tach do dai tung tu bang split() (tu dong bo qua khoang trang)
        danh_sach_tu = dong.split()
        do_dai_tu = []
        for tu in danh_sach_tu:
            do_dai_tu.append(len(tu))

        # Buoc 2: tim doan con dai nhat co gia tri bang nhau lien tiep
        max_lien_tiep = 1
        dem_lien_tiep = 1
        for i in range(1, len(do_dai_tu)):
            if do_dai_tu[i] == do_dai_tu[i - 1]:
                dem_lien_tiep += 1
                if dem_lien_tiep > max_lien_tiep:
                    max_lien_tiep = dem_lien_tiep
            else:
                dem_lien_tiep = 1

        ket_qua_list.append(str(max_lien_tiep))

    print("\n".join(ket_qua_list))

solve()
```

</details>

---

### Bài 3. Tập số
**Chương:** 3. Cấu trúc dữ liệu cơ bản (`set`) — **Nguồn:** Đề thi Chuyên tin 2011

**Đề bài:** Cho số $n$ ở hệ cơ số 10, không quá 20 chữ số, không có số 0 thừa ở đầu.

Bằng cách xoá một hoặc một vài chữ số **liên tiếp** của $n$ (nhưng không xoá hết), ta thu được các số mới; mỗi số mới được chuẩn hoá bằng cách bỏ các chữ số 0 vô nghĩa ở đầu (nếu có).

Tập $D$ gồm $n$ và tất cả các số mới **khác nhau** (khác $n$) thu được theo cách trên.

Ví dụ với $n=1005$: xoá 1 chữ số cho 5 (từ 005), 105, 105, 100; xoá 2 chữ số cho 5 (từ 05), 15, 10; xoá 3 chữ số cho 5, 1. Tập $D = \{1005, 105, 100, 15, 10, 5, 1\}$, trong đó có 3 số chia hết cho 3 (1005, 105, 15).

**Yêu cầu:** đếm số lượng phần tử của $D$ chia hết cho 3.

**Dữ liệu:** Một số nguyên $n$.

**Kết quả:** Số lượng số chia hết cho 3 trong $D$.

**Ví dụ:**

| Dữ liệu | Kết quả |
|---|---|
| `1005` | `3` |

⏱️ Time limit: 2s

<details>
<summary>💡 Xem lời giải</summary>

Giới hạn bài này nhỏ (tối đa 20 chữ số) nên có thể giải trực tiếp bằng cấu trúc `set`:

- Đọc $n$ như một xâu ký tự.
- Sinh mọi cách xoá một đoạn con liên tiếp, chuẩn hoá (bỏ số 0 đầu), rồi cho vào `set` để tự động loại trùng.
- Duyệt tập, với mỗi phần tử tính tổng chữ số, nếu chia hết cho 3 thì đếm.

**C++**

```cpp
#include <iostream>
#include <set>
#include <string>
using namespace std;

int main() {
    string n;
    cin >> n;
    int len = n.length();

    set<string> tapD;
    tapD.insert(n);

    for (int doDaiXoa = 1; doDaiXoa < len; doDaiXoa++) {
        for (int j = 0; j <= len - doDaiXoa; j++) {
            string s = n.substr(0, j) + n.substr(j + doDaiXoa);
            while (s.size() > 1 && s[0] == '0') s = s.substr(1);
            tapD.insert(s);
        }
    }

    int dem = 0;
    for (const string &so : tapD) {
        int tongChuSo = 0;
        for (char c : so) tongChuSo += c - '0';
        if (tongChuSo % 3 == 0) dem++;
    }

    cout << dem;
    return 0;
}
```

**Python**

```python
def solve():
    n = input().strip()
    do_dai = len(n)

    tap_d = set()
    tap_d.add(n)

    # Buoc 1: sinh moi cach xoa mot doan con lien tiep
    for do_dai_xoa in range(1, do_dai):
        for j in range(0, do_dai - do_dai_xoa + 1):
            s = n[0:j] + n[j + do_dai_xoa:]
            # chuan hoa: bo cac chu so 0 thua o dau
            while len(s) > 1 and s[0] == '0':
                s = s[1:]
            tap_d.add(s)

    # Buoc 2: dem cac so chia het cho 3 trong tap D
    dem = 0
    for so in tap_d:
        tong_chu_so = 0
        for ky_tu in so:
            tong_chu_so = tong_chu_so + int(ky_tu)
        if tong_chu_so % 3 == 0:
            dem += 1

    print(dem)

solve()
```

</details>

---

## Phần III. Đồ thị

### Bài 1. Gặm cỏ
**Chương:** 11. Nền tảng đồ thị (11.2 BFS) — **Nguồn:** SPOJ VN — VMUNCH

**Đề bài:** Đồng cỏ được chia thành lưới ô vuông $R$ hàng, $C$ cột ($1 \le R, C \le 100$), mỗi ô là cỏ (`.`), đá (`*`), chuồng bò `B` (ở ô $1,1$) hoặc vị trí Bessie đứng `C`. Bessie di chuyển 4 hướng (không qua đá) để về chuồng theo đường đi **ngắn nhất**. Tính số ô cỏ mà Bessie ăn được trên đường đi ngắn nhất đó (không tính ô chuồng).

**Dữ liệu:**
- Dòng 1: hai số $R, C$
- $R$ dòng tiếp theo: mỗi dòng $C$ ký tự mô tả bản đồ

**Kết quả:** Số ô cỏ ăn được trên đường đi ngắn nhất về chuồng.

**Ví dụ:**

```
Dữ liệu:
5 6
B...*.
..*...
.**.*.
..***.
*..*.C

Kết quả: 9
```

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Bài **BFS** cổ điển trên lưới ô vuông. Xuất phát từ vị trí Bessie đứng, loang ra 4 hướng, tránh các ô đá, đến khi gặp chuồng thì dừng — khoảng cách BFS chính là số bước đi ngắn nhất, số ô cỏ ăn được = số bước - 1.

**C++**

```cpp
#include <iostream>
#include <queue>
using namespace std;

const int MAXN = 100;
bool diDuoc[MAXN][MAXN];

int main() {
    int r, c;
    cin >> r >> c;

    int xBessie, yBessie, xChuong, yChuong;

    for (int i = 0; i < r; i++) {
        for (int j = 0; j < c; j++) {
            char ch;
            cin >> ch;
            if (ch == 'B') { xChuong = j; yChuong = i; diDuoc[i][j] = false; }
            else if (ch == 'C') { xBessie = j; yBessie = i; diDuoc[i][j] = true; }
            else if (ch == '.') diDuoc[i][j] = true;
            else diDuoc[i][j] = false;
        }
    }

    int dx[] = {1, -1, 0, 0};
    int dy[] = {0, 0, 1, -1};

    queue<pair<int,int>> hangDoi;
    int khoangCach[MAXN][MAXN];
    for (int i = 0; i < r; i++)
        for (int j = 0; j < c; j++)
            khoangCach[i][j] = -1;

    hangDoi.push({xBessie, yBessie});
    khoangCach[yBessie][xBessie] = 0;

    while (!hangDoi.empty()) {
        auto [x, y] = hangDoi.front();
        hangDoi.pop();

        for (int k = 0; k < 4; k++) {
            int nx = x + dx[k];
            int ny = y + dy[k];
            if (nx >= 0 && nx < c && ny >= 0 && ny < r &&
                diDuoc[ny][nx] && khoangCach[ny][nx] == -1) {
                khoangCach[ny][nx] = khoangCach[y][x] + 1;
                hangDoi.push({nx, ny});
            }
        }
    }

    cout << khoangCach[yChuong][xChuong];
    return 0;
}
```

**Python**

```python
from collections import deque

def solve():
    r, c = map(int, input().split())

    ban_do = []
    for i in range(r):
        dong = input()
        ban_do.append(dong)

    # Buoc 1: tim vi tri Bessie va vi tri chuong
    x_bessie, y_bessie = 0, 0
    x_chuong, y_chuong = 0, 0
    di_duoc = []
    for i in range(r):
        hang = []
        for j in range(c):
            ky_tu = ban_do[i][j]
            if ky_tu == 'B':
                x_chuong, y_chuong = j, i
                hang.append(False)
            elif ky_tu == 'C':
                x_bessie, y_bessie = j, i
                hang.append(True)
            elif ky_tu == '.':
                hang.append(True)
            else:
                hang.append(False)
        di_duoc.append(hang)

    # Buoc 2: BFS tu vi tri Bessie
    khoang_cach = [[-1] * c for _ in range(r)]
    khoang_cach[y_bessie][x_bessie] = 0

    hang_doi = deque()
    hang_doi.append((x_bessie, y_bessie))

    dx = [1, -1, 0, 0]
    dy = [0, 0, 1, -1]

    while len(hang_doi) > 0:
        x, y = hang_doi.popleft()
        for k in range(4):
            nx = x + dx[k]
            ny = y + dy[k]
            if 0 <= nx < c and 0 <= ny < r:
                if di_duoc[ny][nx] and khoang_cach[ny][nx] == -1:
                    khoang_cach[ny][nx] = khoang_cach[y][x] + 1
                    hang_doi.append((nx, ny))

    print(khoang_cach[y_chuong][x_chuong])

solve()
```

</details>

---

### Bài 2. Bãi cỏ ngon nhất
**Chương:** 11. Nền tảng đồ thị (11.2 BFS/DFS) — **Nguồn:** SPOJ VN — VBGRASS

**Đề bài:** Cánh đồng $R \times C$ ô ($1 \le R, C \le 100$), mỗi ô hoặc là cỏ (`#`) hoặc trống (`.`). Một **khóm cỏ** là một nhóm các ô `#` liên thông với nhau qua cạnh. Đếm số khóm cỏ trên cánh đồng.

**Dữ liệu:**
- Dòng 1: hai số $R, C$
- $R$ dòng tiếp theo mô tả cánh đồng

**Kết quả:** Số lượng khóm cỏ.

**Ví dụ:**

```
Dữ liệu:
5 6
.#....
..#...
..#..#
...##.
.#....

Kết quả: 5
```

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Đây chính là bài đếm **số thành phần liên thông**, dùng BFS/DFS: mỗi lần gặp một ô `#` chưa thăm, tăng biến đếm và loang để đánh dấu toàn bộ khóm đó là đã thăm.

**C++**

```cpp
#include <iostream>
#include <queue>
using namespace std;

const int MAXN = 101;
char luoi[MAXN][MAXN];

int main() {
    int r, c;
    cin >> r >> c;
    for (int i = 0; i < r; i++)
        for (int j = 0; j < c; j++)
            cin >> luoi[i][j];

    int dx[] = {0, 0, 1, -1};
    int dy[] = {1, -1, 0, 0};

    int soKhom = 0;

    for (int i = 0; i < r; i++) {
        for (int j = 0; j < c; j++) {
            if (luoi[i][j] == '#') {
                soKhom++;
                luoi[i][j] = '.';

                queue<pair<int,int>> hangDoi;
                hangDoi.push({i, j});

                while (!hangDoi.empty()) {
                    auto [x, y] = hangDoi.front();
                    hangDoi.pop();

                    for (int k = 0; k < 4; k++) {
                        int nx = x + dx[k];
                        int ny = y + dy[k];
                        if (nx >= 0 && nx < r && ny >= 0 && ny < c &&
                            luoi[nx][ny] == '#') {
                            luoi[nx][ny] = '.';
                            hangDoi.push({nx, ny});
                        }
                    }
                }
            }
        }
    }

    cout << soKhom;
    return 0;
}
```

**Python**

```python
from collections import deque

def solve():
    r, c = map(int, input().split())

    luoi = []
    for i in range(r):
        dong = list(input())
        luoi.append(dong)

    dx = [0, 0, 1, -1]
    dy = [1, -1, 0, 0]

    so_khom = 0

    for i in range(r):
        for j in range(c):
            if luoi[i][j] == '#':
                so_khom += 1
                luoi[i][j] = '.'   # danh dau da tham

                hang_doi = deque()
                hang_doi.append((i, j))

                while len(hang_doi) > 0:
                    x, y = hang_doi.popleft()
                    for k in range(4):
                        nx = x + dx[k]
                        ny = y + dy[k]
                        if 0 <= nx < r and 0 <= ny < c:
                            if luoi[nx][ny] == '#':
                                luoi[nx][ny] = '.'
                                hang_doi.append((nx, ny))

    print(so_khom)

solve()
```

</details>

---

### Bài 3. Nước lạnh
**Chương:** 11. Nền tảng đồ thị (11.2 DFS) — **Nguồn:** SPOJ VN — VCOLDWAT

**Đề bài:** Hệ thống ống dẫn nước là một **cây nhị phân** gồm $N$ đường ống ($3 \le N \le 99999$, $N$ lẻ), gốc là chuồng bò. Mỗi nút ống có đúng 2 nhánh con. Cho $C$ nút ống, mỗi nút mô tả bởi bộ ba $(E_i, B1_i, B2_i)$. Ống số 1 nối trực tiếp với chuồng bò (khoảng cách 1).

**Yêu cầu:** tính khoảng cách từ chuồng bò tới **mọi** đường ống.

**Dữ liệu:**
- Dòng 1: hai số $N, C$
- $C$ dòng tiếp theo: mỗi dòng $E_i, B1_i, B2_i$

**Kết quả:** $N$ dòng, dòng $i$ ghi khoảng cách từ chuồng tới ống $i$.

**Ví dụ:**

```
Dữ liệu:
5 2
3 5 4
1 2 3

Kết quả:
1
2
2
3
3
```

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Dựng cây từ dữ liệu vào, rồi duyệt DFS từ gốc (ống 1) với bậc ban đầu bằng 1, mỗi lần đi xuống nút con thì bậc tăng thêm 1.

**C++**

```cpp
#include <stdio.h>

const int MAXN = 100001;
int con_trai[MAXN], con_phai[MAXN], khoangCach[MAXN];

void duyet(int nut, int bac) {
    khoangCach[nut] = bac;
    if (con_trai[nut] != 0 && con_phai[nut] != 0) {
        duyet(con_trai[nut], bac + 1);
        duyet(con_phai[nut], bac + 1);
    }
}

int main() {
    int n, c;
    scanf("%d%d", &n, &c);

    for (int i = 0; i < c; i++) {
        int nut, trai, phai;
        scanf("%d%d%d", &nut, &trai, &phai);
        con_trai[nut] = trai;
        con_phai[nut] = phai;
    }

    duyet(1, 1);

    for (int i = 1; i <= n; i++)
        printf("%d\n", khoangCach[i]);

    return 0;
}
```

**Python** *(dùng đệ quy với đệ quy giới hạn tăng lên vì cây có thể sâu)*

```python
import sys
sys.setrecursionlimit(200000)

def solve():
    data = sys.stdin.read().split()
    idx = 0
    n = int(data[idx]); idx += 1
    c = int(data[idx]); idx += 1

    con_trai = [0] * (n + 1)
    con_phai = [0] * (n + 1)

    for i in range(c):
        nut = int(data[idx]); idx += 1
        trai = int(data[idx]); idx += 1
        phai = int(data[idx]); idx += 1
        con_trai[nut] = trai
        con_phai[nut] = phai

    khoang_cach = [0] * (n + 1)

    def duyet(nut, bac):
        khoang_cach[nut] = bac
        if con_trai[nut] != 0 and con_phai[nut] != 0:
            duyet(con_trai[nut], bac + 1)
            duyet(con_phai[nut], bac + 1)

    duyet(1, 1)

    ket_qua = []
    for i in range(1, n + 1):
        ket_qua.append(str(khoang_cach[i]))
    print("\n".join(ket_qua))

solve()
```

</details>

---

### Bài 4. Hexgame
**Chương:** 11. Nền tảng đồ thị (11.2 BFS trên đồ thị trạng thái) — **Nguồn:** Đề thi Chuyên tin 2011

**Đề bài:** Trò chơi xếp hình gồm 10 miếng ghép lục giác xếp thành hình tổ ong, đánh số 0-9 (8 miếng ghi số 1-8, 2 miếng ghi số 0). Ở mỗi bước, chọn một trong hai vị trí tâm hợp lệ, xoay 6 miếng xung quanh tâm đó một nấc theo chiều kim đồng hồ.

**Yêu cầu:** cho một trạng thái bất kỳ, tìm số phép xoay **ít nhất** để đưa về trạng thái ban đầu.

**Dữ liệu:** 3 dòng mô tả 3 hàng của lưới lục giác (3, 4, 3 số theo thứ tự).

**Kết quả:** Một số nguyên — số phép biến đổi ít nhất.

**Ví dụ:**

```
Dữ liệu:
1 0 2
8 6 0 3
7 5 4

Kết quả: 5
```

**Ghi chú:** 50% test có đáp số không vượt quá 15.

⏱️ Time limit: 2s

<details>
<summary>💡 Xem lời giải</summary>

Đây là bài **BFS trên đồ thị trạng thái ẩn** (mỗi đỉnh là một cách sắp xếp 10 miếng ghép). Từ trạng thái đề bài cho, mỗi bước sinh ra tối đa 2 trạng thái mới; BFS cho tới khi gặp trạng thái gốc ban đầu.

**C++**

```cpp
#include <iostream>
#include <queue>
#include <array>
using namespace std;

typedef array<int, 10> TrangThai;

const TrangThai GOC = {1, 2, 3, 8, 0, 0, 4, 7, 6, 5};

TrangThai xoayTrai(const TrangThai &a) {
    TrangThai b;
    b[0]=a[3]; b[1]=a[0]; b[2]=a[2]; b[3]=a[7]; b[4]=a[4];
    b[5]=a[1]; b[6]=a[6]; b[7]=a[8]; b[8]=a[5]; b[9]=a[9];
    return b;
}

TrangThai xoayPhai(const TrangThai &a) {
    TrangThai b;
    b[0]=a[0]; b[1]=a[4]; b[2]=a[1]; b[3]=a[3]; b[4]=a[8];
    b[5]=a[5]; b[6]=a[2]; b[7]=a[7]; b[8]=a[9]; b[9]=a[6];
    return b;
}

int main() {
    TrangThai batDau;
    for (int i = 0; i < 10; i++) cin >> batDau[i];

    queue<pair<TrangThai, int>> hangDoi;
    hangDoi.push({batDau, 0});

    while (!hangDoi.empty()) {
        auto [tt, buoc] = hangDoi.front();
        hangDoi.pop();

        if (tt == GOC) {
            cout << buoc;
            return 0;
        }
        hangDoi.push({xoayTrai(tt), buoc + 1});
        hangDoi.push({xoayPhai(tt), buoc + 1});
    }
    return 0;
}
```

**Python**

```python
from collections import deque

GOC = (1, 2, 3, 8, 0, 0, 4, 7, 6, 5)

def xoay_trai(a):
    b = [0] * 10
    b[0] = a[3]; b[1] = a[0]; b[2] = a[2]; b[3] = a[7]; b[4] = a[4]
    b[5] = a[1]; b[6] = a[6]; b[7] = a[8]; b[8] = a[5]; b[9] = a[9]
    return tuple(b)

def xoay_phai(a):
    b = [0] * 10
    b[0] = a[0]; b[1] = a[4]; b[2] = a[1]; b[3] = a[3]; b[4] = a[8]
    b[5] = a[5]; b[6] = a[2]; b[7] = a[7]; b[8] = a[9]; b[9] = a[6]
    return tuple(b)

def solve():
    du_lieu = []
    for _ in range(3):
        du_lieu.extend(input().split())
    bat_dau = tuple(int(x) for x in du_lieu)

    hang_doi = deque()
    hang_doi.append((bat_dau, 0))
    da_tham = set()
    da_tham.add(bat_dau)

    while len(hang_doi) > 0:
        trang_thai, buoc = hang_doi.popleft()

        if trang_thai == GOC:
            print(buoc)
            return

        for trang_thai_moi in (xoay_trai(trang_thai), xoay_phai(trang_thai)):
            if trang_thai_moi not in da_tham:
                da_tham.add(trang_thai_moi)
                hang_doi.append((trang_thai_moi, buoc + 1))

solve()
```

> Lưu ý: BFS trên không gian trạng thái không tường minh (implicit graph) — không cần liệt kê trước toàn bộ đỉnh, chỉ cần hàm sinh trạng thái kề. Bản Python có thêm tập `da_tham` để tránh lặp vô hạn (bản C++ gốc không có, chỉ chạy tốt vì luôn tìm thấy GOC trước khi không gian trạng thái nổ quá lớn — thêm tập đã thăm là thực hành an toàn hơn).

</details>

---

### Bài 5. Xây dựng thành phố
**Chương:** 13. Cây khung & DSU (13.3 Prim's Algorithm) — **Nguồn:** SPOJ VN — NKCITY

**Đề bài:** Cho $N$ trọng điểm và $M$ tuyến đường hai chiều có thể xây ($1\le N\le1000$, $1\le M\le10000$), mỗi tuyến có thời gian hoàn thành riêng. Cần chọn ra một tập tuyến đường sao cho $N$ trọng điểm liên thông với nhau, đồng thời thời gian hoàn thành **toàn bộ** (bằng tuyến lâu nhất trong tập được chọn) là **nhỏ nhất**.

**Dữ liệu:**
- Dòng 1: $N, M$
- $M$ dòng tiếp theo: $u, v, t$

**Kết quả:** Thời gian hoàn thành sớm nhất.

**Ví dụ:**

```
Dữ liệu:
5 7
1 2 2
1 5 1
2 5 1
1 4 3
1 3 2
5 3 2
3 4 4

Kết quả: 3
```

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Đây thực chất là bài toán tìm **cây khung nhỏ nhất (MST)**: cạnh có trọng số lớn nhất trên cây khung nhỏ nhất chính là đáp số cần tìm.

**C++ (thuật toán Prim)**

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

int main() {
    int n, m;
    cin >> n >> m;

    vector<vector<pair<int,int>>> ke(n + 1);
    for (int i = 0; i < m; i++) {
        int u, v, t;
        cin >> u >> v >> t;
        ke[u].push_back({v, t});
        ke[v].push_back({u, t});
    }

    vector<bool> daXet(n + 1, false);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> hangDoi;
    hangDoi.push({0, 1});

    int soDinhDaChon = 0;
    int trongSoLonNhatTrenCay = 0;

    while (!hangDoi.empty() && soDinhDaChon < n) {
        auto [trongSo, u] = hangDoi.top();
        hangDoi.pop();

        if (daXet[u]) continue;
        daXet[u] = true;
        soDinhDaChon++;
        if (trongSo > trongSoLonNhatTrenCay) trongSoLonNhatTrenCay = trongSo;

        for (auto &[v, t] : ke[u]) {
            if (!daXet[v]) hangDoi.push({t, v});
        }
    }

    cout << trongSoLonNhatTrenCay;
    return 0;
}
```

**Python**

```python
import heapq

def solve():
    n, m = map(int, input().split())

    ke = [[] for _ in range(n + 1)]
    for _ in range(m):
        u, v, t = map(int, input().split())
        ke[u].append((v, t))
        ke[v].append((u, t))

    da_xet = [False] * (n + 1)
    hang_doi = [(0, 1)]   # (trong so, dinh)

    so_dinh_da_chon = 0
    trong_so_lon_nhat = 0

    while len(hang_doi) > 0 and so_dinh_da_chon < n:
        trong_so, u = heapq.heappop(hang_doi)

        if da_xet[u]:
            continue
        da_xet[u] = True
        so_dinh_da_chon += 1
        if trong_so > trong_so_lon_nhat:
            trong_so_lon_nhat = trong_so

        for v, t in ke[u]:
            if da_xet[v] == False:
                heapq.heappush(hang_doi, (t, v))

    print(trong_so_lon_nhat)

solve()
```

</details>

---

### Bài 6. Đến trường
**Chương:** 12. Đường đi ngắn nhất (12.1 Dijkstra) — **Nguồn:** SPOJ VN — QBSCHOOL

**Đề bài:** Bản đồ gồm $N$ nút giao thông, $M$ con đường (một chiều hoặc hai chiều, độ dài nguyên dương). Nhà ở nút 1, trường ở nút $N$. Tìm độ dài đường đi ngắn nhất từ nút 1 đến nút $N$, **và** số lượng đường đi ngắn nhất khác nhau đạt được độ dài đó.

**Dữ liệu:**
- Dòng 1: $N, M$
- $M$ dòng tiếp theo: $K, U, V, L$ ($K=1$: một chiều $U\to V$; $K=2$: hai chiều $U$-$V$; $L$ độ dài)

**Kết quả:** Hai số: độ dài đường đi ngắn nhất và số lượng đường đi ngắn nhất.

**Ví dụ:**

```
Input:
3 2
1 1 2 3
2 2 3 1

Output: 4 1
```

**Giới hạn:** $1\le N\le5000$; $1\le M\le20000$; độ dài mỗi đường $\le32000$

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

**Dijkstra cải tiến** để đồng thời đếm số đường đi ngắn nhất: ngoài mảng khoảng cách `dist[]`, duy trì thêm mảng đếm `soLuong[]`.

**C++**

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

const int MAXN = 5002;
const long long VOCUNG = 1e18;

vector<pair<int,int>> ke[MAXN];
long long dist[MAXN];
long long soLuong[MAXN];
bool daXet[MAXN];

int main() {
    int n, m;
    cin >> n >> m;

    for (int i = 0; i < m; i++) {
        int k, u, v, d;
        cin >> k >> u >> v >> d;
        ke[u].push_back({v, d});
        if (k == 2) ke[v].push_back({u, d});
    }

    for (int i = 1; i <= n; i++) dist[i] = VOCUNG;
    dist[1] = 0;
    soLuong[1] = 1;

    priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<>> hangDoi;
    hangDoi.push({0, 1});

    while (!hangDoi.empty()) {
        auto [d, u] = hangDoi.top();
        hangDoi.pop();
        if (daXet[u]) continue;
        daXet[u] = true;

        for (auto &[v, w] : ke[u]) {
            if (dist[v] > d + w) {
                dist[v] = d + w;
                soLuong[v] = soLuong[u];
                hangDoi.push({dist[v], v});
            } else if (dist[v] == d + w) {
                soLuong[v] += soLuong[u];
            }
        }
    }

    cout << dist[n] << " " << soLuong[n];
    return 0;
}
```

**Python**

```python
import heapq

def solve():
    n, m = map(int, input().split())

    ke = [[] for _ in range(n + 1)]
    for _ in range(m):
        k, u, v, d = map(int, input().split())
        ke[u].append((v, d))
        if k == 2:
            ke[v].append((u, d))

    VOCUNG = float('inf')
    dist = [VOCUNG] * (n + 1)
    so_luong = [0] * (n + 1)
    da_xet = [False] * (n + 1)

    dist[1] = 0
    so_luong[1] = 1

    hang_doi = [(0, 1)]

    while len(hang_doi) > 0:
        d, u = heapq.heappop(hang_doi)
        if da_xet[u]:
            continue
        da_xet[u] = True

        for v, w in ke[u]:
            if dist[v] > d + w:
                dist[v] = d + w
                so_luong[v] = so_luong[u]
                heapq.heappush(hang_doi, (dist[v], v))
            elif dist[v] == d + w:
                so_luong[v] += so_luong[u]

    print(dist[n], so_luong[n])

solve()
```

</details>

---

## Phần IV. Quy hoạch động

### Bài 1. Lát gạch
**Chương:** 8. Quy hoạch động cơ bản (8.2 DP 1 chiều) — **Nguồn:** SPOJ VN — LATGACH

**Đề bài:** Đếm số cách lát kín hình chữ nhật $2 \times N$ ($1 \le N \le 100$) bằng các viên gạch $1\times2$ và $2\times1$.

**Dữ liệu:** Nhiều test, dòng đầu là số test $T \le 100$, mỗi dòng sau là một số $N$.

**Kết quả:** $T$ dòng, mỗi dòng là số cách lát tương ứng.

**Ví dụ:**

```
Input:
3
1
2
3

Output:
1
2
3
```

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Gọi $f(i)$ là số cách lát hình $2\times i$. Xét viên gạch cuối cùng: hoặc một viên $2\times1$ dựng đứng (còn lại $2\times(i-1)$), hoặc hai viên $1\times2$ nằm ngang (còn lại $2\times(i-2)$). Suy ra:

$$f(i) = f(i-1) + f(i-2), \quad f(1)=1,\ f(2)=2$$

Đây chính là **dãy Fibonacci**. Với $N\le100$, $f(N)$ có thể rất lớn.

**C++ (cộng số lớn bằng mảng chữ số)**

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> congSoLon(const vector<int> &a, const vector<int> &b) {
    int n = max(a.size(), b.size());
    vector<int> ketQua(n, 0);
    int nho = 0;
    for (int i = 0; i < n; i++) {
        int tong = nho;
        if (i < (int)a.size()) tong += a[i];
        if (i < (int)b.size()) tong += b[i];
        ketQua[i] = tong % 10;
        nho = tong / 10;
    }
    if (nho > 0) ketQua.push_back(nho);
    return ketQua;
}

int main() {
    vector<vector<int>> f(101);
    f[1] = {1};
    f[2] = {2};
    for (int i = 3; i <= 100; i++)
        f[i] = congSoLon(f[i - 1], f[i - 2]);

    int t;
    cin >> t;
    while (t--) {
        int n;
        cin >> n;
        for (int i = f[n].size() - 1; i >= 0; i--)
            cout << f[n][i];
        cout << endl;
    }
    return 0;
}
```

**Python** *(Python xử lý số nguyên lớn tự động, không cần tự cài cộng số lớn)*

```python
def solve():
    # Buoc 1: tinh san day Fibonacci tu 1 den 100
    f = [0] * 101
    f[1] = 1
    f[2] = 2
    for i in range(3, 101):
        f[i] = f[i - 1] + f[i - 2]

    t = int(input())
    ket_qua_list = []
    for _ in range(t):
        n = int(input())
        ket_qua_list.append(str(f[n]))

    print("\n".join(ket_qua_list))

solve()
```

</details>

---

### Bài 2. Lát gạch 3
**Chương:** 8. Quy hoạch động cơ bản (8.2 DP 1 chiều) — **Nguồn:** SPOJ VN — M3TILE

**Đề bài:** Đếm số cách lát kín hình chữ nhật $3\times n$ ($0\le n\le30$) bằng các viên domino $2\times1$.

**Dữ liệu:** Nhiều test kết thúc bằng $-1$, mỗi test là một số $n$.

**Kết quả:** Với mỗi test, in ra số cách lát.

**Ví dụ:**

```
Input:
2
8
12
-1

Output:
3
153
2131
```

**Chú ý:** $n=0$ trả về kết quả 1.

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

**Nhận xét:** nếu $n$ lẻ, tổng số ô $3n$ là số lẻ trong khi mỗi domino phủ 2 ô (luôn chẵn) → không có cách lát → kết quả 0. Chỉ cần xét $n$ chẵn.

Đặt $f(k)$ là số cách lát $3\times k$. Bằng phân tích các trường hợp lát cột đầu tiên và biến đổi đại số, ta rút ra công thức truy hồi rút gọn:

$$f(0)=1,\quad f(2)=3,\quad f(k) = 4f(k-2) - f(k-4)\ (k\ge4,\ k\ \text{chẵn})$$

**C++**

```cpp
#include <iostream>
using namespace std;

int main() {
    long long f[31] = {0};
    f[0] = 1;
    f[2] = 3;
    f[4] = 11;
    for (int k = 6; k <= 30; k += 2)
        f[k] = 4 * f[k - 2] - f[k - 4];

    int n;
    while (cin >> n && n != -1) {
        cout << f[n] << "\n";
    }
    return 0;
}
```

**Python**

```python
import sys

def solve():
    # Buoc 1: tinh san day f cho cac gia tri chan tu 0 den 30
    f = [0] * 31
    f[0] = 1
    f[2] = 3
    f[4] = 11
    k = 6
    while k <= 30:
        f[k] = 4 * f[k - 2] - f[k - 4]
        k += 2

    ket_qua_list = []
    for dong in sys.stdin:
        dong = dong.strip()
        if dong == "":
            continue
        n = int(dong)
        if n == -1:
            break
        ket_qua_list.append(str(f[n]))

    print("\n".join(ket_qua_list))

solve()
```

**Bài học:** ví dụ cho thấy công thức truy hồi ban đầu (phụ thuộc **tổng** các giá trị trước đó) đôi khi có thể được **rút gọn** thành công thức chỉ phụ thuộc 2 số hạng liền kề — kỹ năng quan trọng khi tối ưu QHĐ.

</details>

---

### Bài 3. Bậc thang (Steps)
**Chương:** 8. Quy hoạch động cơ bản (8.2 DP 1 chiều) — **Nguồn:** SPOJ VN — VSTEPS

**Đề bài:** Cầu thang có $n$ bậc đánh số $1..n$. Từ một bậc, có thể bước lên 1 bậc hoặc nhảy lên 2 bậc. Một số bậc bị hỏng (không đi qua được); bậc 1 không bao giờ hỏng. Đếm số cách đi từ bậc 1 đến bậc $n$.

**Dữ liệu:**
- Dòng 1: $n, k$ ($0\le k<n\le100000$)
- Dòng 2: $k$ số nguyên tăng dần — chỉ số các bậc hỏng

**Kết quả:** Số cách đi, lấy phần dư khi chia cho $14062008$.

**Ví dụ:**

```
Dữ liệu:
4 2
2 3
Kết quả: 0

Dữ liệu:
90000 1
49000
Kết quả: 4108266
```

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Vẫn là công thức kiểu Fibonacci $f(i)=f(i-1)+f(i-2)$, nhưng cần xử lý riêng khi bậc bị hỏng: bậc hỏng không thể "bước tới", chỉ có thể **nhảy qua**. Nếu hai bậc hỏng liên tiếp thì hết cách đi tiếp — kết quả 0.

**C++**

```cpp
#include <stdio.h>

const long long MOD = 14062008;
const int MAXN = 100001;

int main() {
    int n, k;
    scanf("%d%d", &n, &k);

    bool hong[MAXN] = {false};
    for (int i = 0; i < k; i++) {
        int viTri;
        scanf("%d", &viTri);
        hong[viTri] = true;
    }

    long long f[MAXN];
    f[1] = 1;

    for (int i = 2; i <= n; i++) {
        if (hong[i]) { f[i] = 0; continue; }

        bool truoc1Hong = hong[i - 1];
        bool truoc2Hong = hong[i - 2];

        if (i == 2) {
            f[i] = truoc1Hong ? 0 : f[1];
        } else if (truoc1Hong && truoc2Hong) {
            f[i] = 0;
        } else if (truoc1Hong) {
            f[i] = f[i - 2];
        } else if (truoc2Hong) {
            f[i] = f[i - 1];
        } else {
            f[i] = (f[i - 1] + f[i - 2]) % MOD;
        }
    }

    printf("%lld\n", f[n] % MOD);
    return 0;
}
```

**Python**

```python
import sys

def solve():
    data = sys.stdin.read().split()
    idx = 0
    n = int(data[idx]); idx += 1
    k = int(data[idx]); idx += 1

    MOD = 14062008
    hong = [False] * (n + 1)
    for i in range(k):
        vi_tri = int(data[idx]); idx += 1
        hong[vi_tri] = True

    f = [0] * (n + 1)
    f[1] = 1

    for i in range(2, n + 1):
        if hong[i]:
            f[i] = 0
            continue

        truoc1_hong = hong[i - 1]
        truoc2_hong = hong[i - 2] if i - 2 >= 0 else True

        if i == 2:
            f[i] = 0 if truoc1_hong else f[1]
        elif truoc1_hong and truoc2_hong:
            f[i] = 0
        elif truoc1_hong:
            f[i] = f[i - 2]
        elif truoc2_hong:
            f[i] = f[i - 1]
        else:
            f[i] = (f[i - 1] + f[i - 2]) % MOD

    print(f[n] % MOD)

solve()
```

</details>

---

### Bài 4. Đường đi có tổng lớn nhất
**Chương:** 8. Quy hoạch động cơ bản (8.3 DP 2 chiều) — **Nguồn:** SPOJ VN — QBMAX

**Đề bài:** Cho bảng $A$ kích thước $m\times n$ ($1\le m,n\le100$, $|a_{ij}|\le100$). Xuất phát từ một ô bất kỳ ở cột 1, cần sang cột $n$ (ô nào cũng được). Từ ô $(i,j)$ chỉ được sang $(i,j+1)$, $(i-1,j+1)$ hoặc $(i+1,j+1)$. Tìm tổng lớn nhất có thể đạt được.

**Dữ liệu:**
- Dòng 1: $m, n$
- $m$ dòng tiếp theo: mỗi dòng $n$ số

**Kết quả:** Tổng lớn nhất tìm được.

**Ví dụ:**

```
Input:
5 7
9 -2 6 2 1 3 4
0 -1 6 7 1 3 3
8 -2 8 2 5 3 2
1 -1 6 2 1 6 1
7 -2 6 2 1 3 7

Output: 41
```

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Quy hoạch động trên bảng: gọi $b[i][j]$ là tổng lớn nhất khi tới ô $(i,j)$:

$$b[i][j] = a[i][j] + \max\big(b[i-1][j-1],\ b[i][j-1],\ b[i+1][j-1]\big)$$

Khởi tạo $b[i][1] = a[i][1]$ cho mọi hàng (xuất phát tự do ở cột 1), dùng "âm vô cùng" cho các hàng biên ngoài để tránh đi ra ngoài bảng.

**C++**

```cpp
#include <iostream>
#include <climits>
using namespace std;

const int MAXD = 102;
int a[MAXD][MAXD];
int b[MAXD][MAXD];

int main() {
    int m, n;
    cin >> m >> n;

    for (int i = 1; i <= m; i++)
        for (int j = 1; j <= n; j++)
            cin >> a[i][j];

    for (int i = 0; i <= m + 1; i++)
        for (int j = 0; j <= n; j++)
            b[i][j] = INT_MIN;

    for (int i = 1; i <= m; i++)
        b[i][1] = a[i][1];

    for (int j = 2; j <= n; j++) {
        for (int i = 1; i <= m; i++) {
            int maxTruoc = max({b[i - 1][j - 1], b[i][j - 1], b[i + 1][j - 1]});
            if (maxTruoc == INT_MIN) continue;
            b[i][j] = a[i][j] + maxTruoc;
        }
    }

    int ketQua = INT_MIN;
    for (int i = 1; i <= m; i++)
        ketQua = max(ketQua, b[i][n]);

    cout << ketQua;
    return 0;
}
```

**Python**

```python
def solve():
    m, n = map(int, input().split())

    a = []
    for i in range(m):
        hang = list(map(int, input().split()))
        a.append(hang)

    AM_VO_CUNG = float('-inf')

    # Buoc 1: khoi tao bang b co them 2 hang bien tren duoi de tranh loi chi so
    b = [[AM_VO_CUNG] * (n + 1) for _ in range(m + 2)]

    # Buoc 2: xuat phat tu do o cot 1
    for i in range(1, m + 1):
        b[i][1] = a[i - 1][0]

    # Buoc 3: dien bang QHD theo tung cot tu trai sang phai
    for j in range(2, n + 1):
        for i in range(1, m + 1):
            gia_tri_tren = b[i - 1][j - 1]
            gia_tri_giua = b[i][j - 1]
            gia_tri_duoi = b[i + 1][j - 1] if i + 1 <= m else AM_VO_CUNG

            max_truoc = max(gia_tri_tren, gia_tri_giua, gia_tri_duoi)
            if max_truoc == AM_VO_CUNG:
                continue
            b[i][j] = a[i - 1][j - 1] + max_truoc

    ket_qua = AM_VO_CUNG
    for i in range(1, m + 1):
        if b[i][n] > ket_qua:
            ket_qua = b[i][n]

    print(ket_qua)

solve()
```

</details>

---

## Phần V. Các bài toán khác

### Bài 1. Đấu giá
**Chương:** 17. Số học cơ bản (17.1 kiểm tra nguyên tố) — **Nguồn:** Đề thi Chuyên tin 2010

**Đề bài:** Một "biển số xe đẹp" là số nguyên dương $T$ thỏa: $A\le T\le B$; $T$ là số nguyên tố; $T$ là số đối xứng (palindrome). Đếm số lượng biển số đẹp.

**Dữ liệu:** Hai số nguyên dương $A, B$ ($10^4\le A<B<10^5$).

**Kết quả:** Số lượng biển số đẹp tìm được.

**Ví dụ:**

| Dữ liệu | Kết quả |
|---|---|
| `11111 22222` | `23` |

⏱️ Time limit: 2s

<details>
<summary>💡 Xem lời giải</summary>

Kết hợp 2 hàm kiểm tra đơn giản:
1. **Kiểm tra đối xứng** (so sánh chữ số đầu-cuối).
2. **Kiểm tra nguyên tố** (chia thử tới $\sqrt{a}$).

Duyệt từ $A$ đến $B$, đếm các số thỏa cả hai điều kiện.

**C++**

```cpp
#include <iostream>
using namespace std;

bool laDoiXung(int a) {
    string s = to_string(a);
    int n = s.size();
    for (int i = 0; i < n / 2; i++)
        if (s[i] != s[n - 1 - i]) return false;
    return true;
}

bool laNguyenTo(int a) {
    if (a < 2) return false;
    for (int i = 2; (long long)i * i <= a; i++)
        if (a % i == 0) return false;
    return true;
}

int main() {
    int a, b;
    cin >> a >> b;

    int dem = 0;
    for (int t = a; t <= b; t++) {
        if (laDoiXung(t) && laNguyenTo(t)) dem++;
    }

    cout << dem;
    return 0;
}
```

**Python**

```python
def la_doi_xung(a):
    s = str(a)
    n = len(s)
    for i in range(n // 2):
        if s[i] != s[n - 1 - i]:
            return False
    return True

def la_nguyen_to(a):
    if a < 2:
        return False
    i = 2
    while i * i <= a:
        if a % i == 0:
            return False
        i += 1
    return True

def solve():
    a, b = map(int, input().split())

    dem = 0
    for t in range(a, b + 1):
        if la_doi_xung(t) and la_nguyen_to(t):
            dem += 1

    print(dem)

solve()
```

</details>

---

### Bài 2. Trông xe
**Chương:** 5. Mảng cộng dồn & Mảng hiệu (kỹ thuật đếm theo chỉ số) — **Nguồn:** Đề thi Chuyên tin 2010

**Đề bài:** Bãi xe trông xe trong một tháng. Mỗi xe có số hiệu $T$ ($10102010\le T\le10109999$). Một xe có thể ra vào nhiều lần. Nếu một xe vào bãi $p$ lần, phí phải trả:

$$C = \begin{cases}100 & p\le5\\ 100+(p-5)\cdot\text{đơn giá thêm} & p>5\end{cases}$$

*(công thức phần vượt trong tài liệu gốc bị mất định dạng khi trích xuất PDF — bạn cần đối chiếu lại đề gốc để lấy đúng hệ số nhân trước khi dùng số liệu này)*

**Yêu cầu:** Tính tổng phí thu được.

**Dữ liệu:**
- Dòng 1: số nguyên dương $K$ ($0<K\le10^6$)
- $K$ dòng tiếp theo: số hiệu từng lượt xe vào

**Kết quả:** Tổng phí thu được.

**Ví dụ:**

```
Dữ liệu:
7
10102010
10108888
10102010
10102010
10102010
10102010
10102010

Kết quả: 201
```

⏱️ Time limit: 2s

<details>
<summary>💡 Xem lời giải</summary>

**Ý tưởng:** khoảng giá trị số hiệu xe chỉ là $10109999-10102010+1=7990$ giá trị khác nhau, trong khi $K$ có thể tới $10^6$ lượt — đây là kỹ thuật **đếm theo chỉ số (bucket counting)**: dùng mảng `dem[]` kích thước 7990, mỗi lượt xe vào tăng `dem[T - 10102010]`. Cuối cùng duyệt mảng, tính phí cho từng xe theo công thức đề bài, cộng dồn.

**C++**

```cpp
#include <iostream>
using namespace std;

const long long OFFSET = 10102010;
const int KICH_THUOC = 7990;
int dem[KICH_THUOC];

int main() {
    int k;
    cin >> k;

    for (int i = 0; i < k; i++) {
        long long soHieu;
        cin >> soHieu;
        dem[soHieu - OFFSET]++;
    }

    long long tongPhi = 0;
    for (int i = 0; i < KICH_THUOC; i++) {
        int p = dem[i];
        if (p == 0) continue;
        if (p <= 5) tongPhi += 100;
        else tongPhi += 100 + (p - 5);  // dieu chinh he so theo dung de goc
    }

    cout << tongPhi;
    return 0;
}
```

**Python**

```python
import sys

def solve():
    data = sys.stdin.read().split()
    k = int(data[0])

    OFFSET = 10102010
    KICH_THUOC = 7990
    dem = [0] * KICH_THUOC

    for i in range(1, k + 1):
        so_hieu = int(data[i])
        dem[so_hieu - OFFSET] += 1

    tong_phi = 0
    for i in range(KICH_THUOC):
        p = dem[i]
        if p == 0:
            continue
        if p <= 5:
            tong_phi += 100
        else:
            tong_phi += 100 + (p - 5)  # dieu chinh he so theo dung de goc

    print(tong_phi)

solve()
```

</details>

---

### Bài 3. Cây nhị phân tìm kiếm
**Chương:** 11. Nền tảng đồ thị (cây là trường hợp đặc biệt của đồ thị) — **Nguồn:** SPOJ VN — NKTREE

**Đề bài:** Cho một dãy giá trị đôi một khác nhau. Xác định xem có tồn tại một **cây nhị phân tìm kiếm (BST)** sao cho có một đường đi từ nút cha xuống nút con (liên tiếp) mang đúng dãy giá trị đã cho hay không.

Ví dụ: dãy `5 1 3 2` tồn tại BST tương ứng; dãy `5 2 3 1` thì không.

**Dữ liệu:** Dãy các giá trị (không quá 50000 phần tử, mỗi phần tử có trị tuyệt đối $\le2^{31}$), đọc đến hết dữ liệu.

**Kết quả:** In `YES` nếu tồn tại, `NO` nếu không.

**Ví dụ:**

| Dữ liệu | Kết quả |
|---|---|
| `5 1 3 2` | `YES` |
| `5 2 3 1` | `NO` |

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

**Ý tưởng:** duy trì khoảng giá trị hợp lệ $(Min, Max)$ mà giá trị tiếp theo phải nằm trong đó (tính chất BST: cây con trái luôn nhỏ hơn nút cha, cây con phải luôn lớn hơn nút cha). Gọi $b$ là giá trị nút cha (giá trị liền trước), $c$ là giá trị đang xét:

- Nếu $c$ nằm ngoài $(Min, Max)$, hoặc $c=b$ → không hợp lệ, in `NO`.
- Nếu $c<b$ → $c$ đi xuống cây con trái → cập nhật $Max=b$.
- Nếu $c>b$ → $c$ đi xuống cây con phải → cập nhật $Min=b$.

**C++**

```cpp
#include <iostream>
#include <climits>
using namespace std;

int main() {
    long long c;
    long long minGT = LLONG_MIN, maxGT = LLONG_MAX;
    long long truoc = 0;
    bool laPhanTuDauTien = true;
    bool hopLe = true;

    while (cin >> c) {
        if (!hopLe) continue;

        if (laPhanTuDauTien) {
            truoc = c;
            laPhanTuDauTien = false;
            continue;
        }

        if (c == truoc || c <= minGT || c >= maxGT) {
            hopLe = false;
            continue;
        }

        if (c < truoc) maxGT = truoc;
        else minGT = truoc;
        truoc = c;
    }

    cout << (hopLe ? "YES" : "NO");
    return 0;
}
```

**Python**

```python
import sys

def solve():
    day = sys.stdin.read().split()

    if len(day) == 0:
        print("NO")
        return

    min_gt = float('-inf')
    max_gt = float('inf')
    truoc = int(day[0])
    hop_le = True

    for i in range(1, len(day)):
        c = int(day[i])

        if c == truoc or c <= min_gt or c >= max_gt:
            hop_le = False
            break

        if c < truoc:
            max_gt = truoc
        else:
            min_gt = truoc
        truoc = c

    print("YES" if hop_le else "NO")

solve()
```

**Lưu ý quan trọng từ đề gốc:** dữ liệu vào có thể vượt phạm vi số nguyên 32-bit, cần đọc bằng kiểu số nguyên lớn hơn (`long long` trong C++; Python không cần lo vì số nguyên không giới hạn kích thước).

</details>

---

### Bài 4. Cây khung nhỏ nhất
**Chương:** 13. Cây khung & DSU (13.1 DSU, 13.2 Kruskal) — **Nguồn:** SPOJ VN — QBMST

**Đề bài:** Cho đơn đồ thị vô hướng liên thông $G=(V,E)$ gồm $n$ đỉnh, $m$ cạnh có trọng số. Tìm **tổng trọng số** của cây khung nhỏ nhất.

**Dữ liệu:**
- Dòng 1: $n, m$ ($1\le n\le10000$, $1\le m\le15000$)
- $m$ dòng tiếp theo: $u, v, c$

**Kết quả:** Tổng trọng số cây khung nhỏ nhất.

**Ví dụ:**

```
Input:
6 9
1 2 1
1 3 1
2 4 1
2 3 2
2 5 1
3 5 1
3 6 1
4 5 2
5 6 2

Output: 5
```

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Bài toán MST kinh điển. Giải bằng **Kruskal** (dùng DSU để kiểm tra chu trình khi thêm cạnh theo thứ tự trọng số tăng dần).

**C++**

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Canh { int u, v, trongSo; };

vector<int> cha;

int timCha(int x) {
    if (cha[x] != x) cha[x] = timCha(cha[x]);
    return cha[x];
}

bool hopNhat(int x, int y) {
    int rx = timCha(x), ry = timCha(y);
    if (rx == ry) return false;
    cha[rx] = ry;
    return true;
}

int main() {
    int n, m;
    cin >> n >> m;

    vector<Canh> danhSachCanh(m);
    for (auto &e : danhSachCanh)
        cin >> e.u >> e.v >> e.trongSo;

    sort(danhSachCanh.begin(), danhSachCanh.end(),
         [](const Canh &a, const Canh &b) { return a.trongSo < b.trongSo; });

    cha.resize(n + 1);
    for (int i = 1; i <= n; i++) cha[i] = i;

    long long tongTrongSo = 0;
    int soCanhDaChon = 0;

    for (auto &e : danhSachCanh) {
        if (soCanhDaChon == n - 1) break;
        if (hopNhat(e.u, e.v)) {
            tongTrongSo += e.trongSo;
            soCanhDaChon++;
        }
    }

    cout << tongTrongSo;
    return 0;
}
```

**Python**

```python
def tim_cha(cha, x):
    if cha[x] != x:
        cha[x] = tim_cha(cha, cha[x])
    return cha[x]

def hop_nhat(cha, x, y):
    rx = tim_cha(cha, x)
    ry = tim_cha(cha, y)
    if rx == ry:
        return False
    cha[rx] = ry
    return True

def solve():
    n, m = map(int, input().split())

    danh_sach_canh = []
    for _ in range(m):
        u, v, trong_so = map(int, input().split())
        danh_sach_canh.append((trong_so, u, v))

    # Buoc 1: sap xep cac canh theo trong so tang dan
    danh_sach_canh.sort()

    cha = list(range(n + 1))

    tong_trong_so = 0
    so_canh_da_chon = 0

    for trong_so, u, v in danh_sach_canh:
        if so_canh_da_chon == n - 1:
            break
        if hop_nhat(cha, u, v):
            tong_trong_so += trong_so
            so_canh_da_chon += 1

    print(tong_trong_so)

solve()
```

</details>

---

### Bài 5. Khối lượng phân tử
**Chương:** 3. Cấu trúc dữ liệu cơ bản (3.2 Stack) — **Nguồn:** SPOJ VN — MMASS

**Đề bài:** Hoá chất chỉ gồm nguyên tố C, H, O có trọng lượng nguyên tử lần lượt là 12, 1, 16. Công thức được biểu diễn dạng "nén" với dấu ngoặc và hệ số lặp (2-9). Tính tổng khối lượng phân tử.

**Dữ liệu:** Một dòng xâu mô tả công thức (không quá 100 ký tự, chỉ gồm `C`,`H`,`O`,`(`,`)`,`2`..`9`).

**Kết quả:** Khối lượng phân tử (đảm bảo $\le10000$).

**Ví dụ:**

| Input | Output |
|---|---|
| `COOH` | `45` |
| `CH(CO2H)3` | `148` |
| `((CH)2(OH2H)(C(H))O)3` | `222` |

⏱️ Time limit: 1s

<details>
<summary>💡 Xem lời giải</summary>

Xử lý biểu thức có ngoặc bằng **ngăn xếp (stack)**:

- Gặp `(` → đẩy giá trị `0` vào stack (làm "cột mốc" bắt đầu nhóm).
- Gặp `C`, `H`, `O` → đẩy khối lượng nguyên tử tương ứng vào stack.
- Gặp chữ số `2`..`9` → lấy giá trị đầu stack ra, nhân với hệ số đó, đẩy lại.
- Gặp `)` → cộng dồn các giá trị ở đầu stack cho tới khi gặp mốc `0`, đẩy tổng trở lại stack.

Cuối cùng, cộng tất cả giá trị còn lại trong stack.

**C++**

```cpp
#include <iostream>
#include <stack>
#include <string>
using namespace std;

int khoiLuong(char nguyenTo) {
    if (nguyenTo == 'C') return 12;
    if (nguyenTo == 'H') return 1;
    if (nguyenTo == 'O') return 16;
    return 0;
}

int main() {
    string s;
    cin >> s;

    stack<int> ngan;

    for (char ch : s) {
        if (ch == '(') {
            ngan.push(0);
        } else if (ch >= '2' && ch <= '9') {
            int heSo = ch - '0';
            int giaTriDau = ngan.top();
            ngan.pop();
            ngan.push(giaTriDau * heSo);
        } else if (ch == 'C' || ch == 'H' || ch == 'O') {
            ngan.push(khoiLuong(ch));
        } else if (ch == ')') {
            int tong = 0;
            while (ngan.top() != 0) {
                tong += ngan.top();
                ngan.pop();
            }
            ngan.pop();
            ngan.push(tong);
        }
    }

    int ketQua = 0;
    while (!ngan.empty()) {
        ketQua += ngan.top();
        ngan.pop();
    }

    cout << ketQua;
    return 0;
}
```

**Python**

```python
def khoi_luong(nguyen_to):
    if nguyen_to == 'C':
        return 12
    if nguyen_to == 'H':
        return 1
    if nguyen_to == 'O':
        return 16
    return 0

def solve():
    s = input().strip()

    ngan = []

    for ch in s:
        if ch == '(':
            ngan.append(0)  # moc bat dau nhom
        elif ch >= '2' and ch <= '9':
            he_so = int(ch)
            gia_tri_dau = ngan.pop()
            ngan.append(gia_tri_dau * he_so)
        elif ch in ('C', 'H', 'O'):
            ngan.append(khoi_luong(ch))
        elif ch == ')':
            tong = 0
            while ngan[-1] != 0:
                tong += ngan.pop()
            ngan.pop()  # bo moc 0
            ngan.append(tong)

    ket_qua = 0
    while len(ngan) > 0:
        ket_qua += ngan.pop()

    print(ket_qua)

solve()
```

</details>

---

*Hết.*

**Nguồn gốc bộ tài liệu:** biên soạn bởi đội tuyển Olympic Chuyên tin 2011, Khoa CNTT — Đại học Hàng Hải. Bài viết lại theo định dạng Markdown (kèm code Python song song với C++, bảng đối chiếu 22 chương chuyên đề, và phần lời giải dạng gập/mở) để tiện đọc, tra cứu và dùng bổ sung bài tập theo từng chương trên GitHub.
