# Tổng hợp 42 bài tập HSG Tin học 9 – Năm học 2022–2023

> Biên soạn lại từ tài liệu `tailieuhsgtin_133202411.pdf` (11 đề: BR-VT, Đà Nẵng, Đắk Nông, Hà Nội, Hậu Giang, TP.HCM, Khánh Hòa, Lâm Đồng, Nghệ An, Quảng Trị, Quảng Ninh).
>
> Mỗi bài được gắn nhãn **Kỹ thuật cốt lõi**, **Chương chính** và **Chương phụ trợ** theo khung chương trình 9 Phần / 29 Chương (Lê Minh Hoàng, CPH, Halim, CLRS) đang xây dựng. Bảng chú giải Phần bên dưới dùng để tra cứu nhanh.
>
> Mỗi bài được đặt trong khối `<details>` để **ẩn/hiện**, tránh trang quá dài. Code được viết **tường minh từng dòng** (không gộp lệnh trên một dòng) và có đầy đủ cả bản **C++** lẫn **Python**.

## Chú giải các Phần (dùng để ghi ở mục "Chương chính/phụ trợ")

| Phần | Nội dung |
|---|---|
| **P1** | Nhập môn & Nền tảng (Ch1 Nhập môn, Ch2 Độ phức tạp thuật toán, Ch3 Cấu trúc dữ liệu cơ bản/STL) |
| **P2** | Xử lý mảng & chuỗi cơ bản (Sort/Binary search, Prefix sum/Difference array, Two pointers/Sliding window/Monotonic stack&queue, Backtracking) |
| **P3** | Quy hoạch động (DP cơ bản, Tree DP/Bitmask DP, DP nâng cao: CHT/D&C opt/Digit DP) |
| **P4** | Đồ thị (DFS/BFS/Topo, Đường đi ngắn nhất, MST/DSU, Ghép cặp, Luồng, SCC/Cầu-khớp/LCA/HLD) |
| **P5** | Toán học & tổ hợp (Số học, Tổ hợp, CRT/Lũy thừa ma trận/Xác suất) |
| **P6** | Cấu trúc dữ liệu nâng cao (Fenwick/Segment Tree/Sparse Table, Coordinate compression/Offline/Sweep line/Mo's, Trie/Persistent segtree) |
| **P7** | Xử lý xâu (KMP/Z-function, Hashing/Manacher/Suffix array) |
| **P8** | Kỹ thuật đặc biệt (Greedy, Chia để trị nâng cao, Meet in the middle/Branch & Bound) |
| **P9** | Kỹ năng thi đấu thực chiến & tổng ôn tập |

## Mục lục theo đề thi

1. [2023 HSG9 BR-VT](#1-2023-hsg9-brvt) — Bài 1–3
2. [2023 HSG9 Đà Nẵng](#2-2023-hsg9-danang) — Bài 4–7
3. [2023 HSG9 Đắk Nông](#3-2023-hsg9-daknong) — Bài 8–11
4. [2023 HSG9 Hà Nội](#4-2023-hsg9-hanoi) — Bài 12–16
5. [2023 HSG9 Hậu Giang](#5-2023-hsg9-haugiang) — Bài 17–19
6. [2023 HSG9 TP.HCM](#6-2023-hsg9-hcm) — Bài 20–22
7. [2023 HSG9 Khánh Hòa](#7-2023-hsg9-khanhhoa) — Bài 23–26
8. [2023 HSG9 Lâm Đồng](#8-2023-hsg9-lamdong) — Bài 27–30
9. [2023 HSG9 Nghệ An](#9-2023-hsg9-nghean) — Bài 31–34
10. [2023 HSG9 Quảng Trị](#10-2023-hsg9-quangtri) — Bài 35–38
11. [2023 HSG9 Quảng Ninh](#11-2023-hsg9-quangninh) — Bài 39–42

---

## 1. 2023 HSG9 BRVT

<details>
<summary><b>Bài 1. CDIV – Tìm ước chung lớn nhất</b></summary>

**Đề bài:** Cho mảng A gồm N số nguyên dương. Tìm 2 số trong mảng có UCLN lớn nhất.

**Kỹ thuật cốt lõi:** Đếm ước bằng mảng đánh dấu (không sinh tất cả cặp O(N²)), duyệt ước từ lớn xuống nhỏ.

**Chương chính:** P5 – Số học (ước số, UCLN)
**Chương phụ trợ:** P2 – Mảng đếm cơ bản

**Ý tưởng:** Gọi `maxx = max(a[i])`. Với mỗi `a[i]`, duyệt các ước `j` của nó (chỉ cần duyệt tới `sqrt(a[i])`) và tăng `d[j]++`. Sau đó duyệt `j` từ `maxx` xuống 1, giá trị `j` đầu tiên có `d[j] > 1` (tức có ít nhất 2 phần tử cùng chia hết cho `j`) chính là UCLN cần tìm.

```cpp
#include <bits/stdc++.h>
using namespace std;

int n;
int a[1000005];
int d[1000005];

void nhap()
{
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
}

void giai()
{
    int maxx = 0;
    for (int i = 1; i <= n; i++)
    {
        maxx = max(maxx, a[i]);
    }

    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= (int)sqrt(a[i]); j++)
        {
            if (a[i] % j == 0)
            {
                d[j]++;
                if (j != a[i] / j)
                {
                    d[a[i] / j]++;
                }
            }
        }
    }

    for (int i = maxx; i >= 1; i--)
    {
        if (d[i] > 1)
        {
            cout << i;
            break;
        }
    }
}

int main()
{
    nhap();
    giai();
    return 0;
}
```

```python
import math


def nhap():
    n = int(input())
    a = list(map(int, input().split()))
    return n, a


def giai(n, a):
    maxx = max(a)
    d = [0] * (maxx + 1)

    for i in range(n):
        v = a[i]
        limit = int(math.isqrt(v))
        for j in range(1, limit + 1):
            if v % j == 0:
                d[j] += 1
                if j != v // j:
                    d[v // j] += 1

    for j in range(maxx, 0, -1):
        if d[j] > 1:
            return j
    return 1


if __name__ == "__main__":
    n, a = nhap()
    print(giai(n, a))
```

</details>

<details>
<summary><b>Bài 2. GIFT – Đố vui tin học</b></summary>

**Đề bài:** Chọn dãy con tăng chỉ số, mỗi phần tử sau phải lớn hơn phần tử trước ít nhất K đơn vị. Tìm số lượng phần tử chọn được nhiều nhất.

**Kỹ thuật cốt lõi:** DP dãy con tăng dài nhất (LIS biến thể) + Tìm kiếm nhị phân để tối ưu từ O(N²) xuống O(N log N).

**Chương chính:** P3 – Quy hoạch động cơ bản
**Chương phụ trợ:** P2 – Sort/Binary search

**Ý tưởng:** `f[i]` = độ dài dãy con dài nhất kết thúc tại `a[i]`. `f[i] = max(f[j]) + 1` với mọi `j < i` thỏa `a[i] >= a[j] + K`. Duy trì mảng `b[len]` = chỉ số có `f = len` nhỏ nhất về giá trị, dùng nhị phân để tìm `f[j]` lớn nhất thỏa điều kiện.

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, kk;
int a[100001];
int f[100001];
int b[100001];

void nhap()
{
    cin >> n >> kk;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
}

int bs(int x, int l, int r)
{
    int res = 0;
    int m;
    while (l <= r)
    {
        m = (l + r) / 2;
        if (a[b[m]] + kk <= x)
        {
            res = m;
            l = m + 1;
        }
        else
        {
            r = m - 1;
        }
    }
    return b[res];
}

void giai()
{
    int res = 1;
    f[1] = 1;
    b[1] = 1;
    for (int i = 2; i <= n; i++)
    {
        int jmax = bs(a[i], 1, f[res]);
        f[i] = f[jmax] + 1;
        if (a[i] < a[b[f[i]]] || f[i] > f[res])
        {
            b[f[i]] = i;
        }
        if (f[i] > f[res])
        {
            res = i;
        }
    }
    cout << f[res] << endl;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    nhap();
    giai();
    return 0;
}
```

```python
import bisect


def giai(n, k, a):
    # tails[len] = giá trị a nhỏ nhất có thể để đạt độ dài len,
    # dùng bisect trên mảng "a[i] - i*k" đã được dịch chuyển
    # Cách trực quan: dùng mảng f[] O(n^2) khi n nhỏ,
    # với n <= 1e5 ta cần O(n log n) bằng segment values.
    tails = []  # tails[t-1] = giá trị a[] nhỏ nhất để có dãy dài t
    for i in range(n):
        # tìm vị trí lớn nhất t sao cho tails[t-1] + k <= a[i]
        lo, hi = 0, len(tails)
        while lo < hi:
            mid = (lo + hi) // 2
            if tails[mid] + k <= a[i]:
                lo = mid + 1
            else:
                hi = mid
        if lo == len(tails):
            tails.append(a[i])
        else:
            if a[i] < tails[lo]:
                tails[lo] = a[i]
    return len(tails)


if __name__ == "__main__":
    n, k = map(int, input().split())
    a = [int(input()) for _ in range(n)]
    print(giai(n, k, a))
```

</details>

<details>
<summary><b>Bài 3. GAME1 – Trò chơi</b></summary>

**Đề bài:** Nhảy từ ô 1 đến ô N, mỗi lần nhảy tối đa K ô, chi phí là trị tuyệt đối chênh lệch năng lượng. Tìm chi phí nhỏ nhất.

**Kỹ thuật cốt lõi:** Quy hoạch động 1 chiều với cửa sổ K phần tử phía trước.

**Chương chính:** P3 – Quy hoạch động cơ bản
**Chương phụ trợ:** P2 – Sliding window

**Ý tưởng:** `f[i]` = chi phí nhỏ nhất để đến ô `i`. `f[i] = min(f[j] + |h[i]-h[j]|)` với `j` từ `i-k` đến `i-1`.

```cpp
#include <bits/stdc++.h>
using namespace std;

long long n;
long long h[100001];
long long f[100001];
int k;

void nhap()
{
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    {
        cin >> h[i];
    }
}

void giai()
{
    f[1] = 0;
    for (int i = 2; i <= n; i++)
    {
        f[i] = 1e18;
        for (int j = i - k; j <= i - 1; j++)
        {
            if (j > 0 && f[j] + abs(h[i] - h[j]) <= f[i])
            {
                f[i] = f[j] + abs(h[i] - h[j]);
            }
        }
    }
    cout << f[n];
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    nhap();
    giai();
    return 0;
}
```

```python
def giai(n, k, h):
    INF = float("inf")
    f = [INF] * (n + 1)
    f[1] = 0
    for i in range(2, n + 1):
        best = INF
        lo = max(1, i - k)
        for j in range(lo, i):
            cand = f[j] + abs(h[i] - h[j])
            if cand < best:
                best = cand
        f[i] = best
    return f[n]


if __name__ == "__main__":
    n, k = map(int, input().split())
    h = [0] + list(map(int, input().split()))
    print(giai(n, k, h))
```

</details>

---

## 2. 2023 HSG9 Đanang

<details>
<summary><b>Bài 4. UTS – Ước thực sự lớn nhất</b></summary>

**Đề bài:** Tìm ước thực sự lớn nhất của số x (x > 1).

**Kỹ thuật cốt lõi:** Ước thực sự lớn nhất = x / (ước nhỏ nhất khác 1), duyệt tới `sqrt(x)`.

**Chương chính:** P5 – Số học
**Chương phụ trợ:** P1 – Nhập môn (vòng lặp cơ bản)

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

ll x;

void giai()
{
    for (int i = 2; i <= (int)sqrt(x); i++)
    {
        if (x % i == 0)
        {
            cout << x / i;
            return;
        }
    }
    cout << 1;
}

int main()
{
    cin >> x;
    giai();
    return 0;
}
```

```python
import math


def giai(x):
    limit = int(math.isqrt(x))
    for i in range(2, limit + 1):
        if x % i == 0:
            return x // i
    return 1


if __name__ == "__main__":
    x = int(input())
    print(giai(x))
```

</details>

<details>
<summary><b>Bài 5. DKT – Đếm ký tự</b></summary>

**Đề bài:** Đếm số ký tự xuất hiện ít nhất 2 lần trong xâu (phân biệt hoa/thường).

**Kỹ thuật cốt lõi:** Mảng đếm tần suất ký tự.

**Chương chính:** P2 – Mảng đếm cơ bản
**Chương phụ trợ:** P7 – Xử lý xâu cơ bản

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

ll d[1000];
ll res;
string s;

int main()
{
    getline(cin, s);
    for (int i = 0; i < (int)s.size(); i++)
    {
        d[(int)s[i]]++;
    }
    for (int i = 0; i <= 500; i++)
    {
        if (d[i] > 1)
        {
            res++;
        }
    }
    cout << res;
    return 0;
}
```

```python
def giai(s):
    d = {}
    for ch in s:
        d[ch] = d.get(ch, 0) + 1
    res = 0
    for ch in d:
        if d[ch] > 1:
            res += 1
    return res


if __name__ == "__main__":
    s = input()
    print(giai(s))
```

</details>

<details>
<summary><b>Bài 6. NTDX – Số nguyên tố đối xứng</b></summary>

**Đề bài:** Đếm số nguyên tố đối xứng (số palindrome và là số nguyên tố) trong khoảng (10, x).

**Kỹ thuật cốt lõi:** Sinh số đối xứng từ một nửa (không duyệt toàn bộ khoảng tới 10^10), rồi kiểm tra nguyên tố.

**Chương chính:** P5 – Số học (kiểm tra nguyên tố)
**Chương phụ trợ:** P7 – Xử lý xâu (đảo số/đối xứng)

**Ý tưởng:** Với x ≤ 10^10, số đối xứng dài tối đa 10 chữ số nên "nửa trái" `i` chỉ chạy tới 10^5. Với mỗi `i`, sinh 2 dạng đối xứng độ dài chẵn (`NkkN`) và độ dài lẻ (`NkN`), kiểm tra nguyên tố và nằm trong khoảng.

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

bool ktnt(ll a)
{
    if (a < 2)
    {
        return false;
    }
    if (a == 2 || a == 3)
    {
        return true;
    }
    if (a % 2 == 0 || a % 3 == 0)
    {
        return false;
    }
    for (ll j = 5; j <= (ll)sqrt(a); j += 6)
    {
        if (a % j == 0 || a % (j + 2) == 0)
        {
            return false;
        }
    }
    return true;
}

// ghép đối xứng độ dài chẵn: x -> x đảo ngược nối tiếp
ll dx1(ll x)
{
    ll y = x / 10;
    while (y > 0)
    {
        x = x * 10 + (y % 10);
        y = y / 10;
    }
    return x;
}

// ghép đối xứng độ dài lẻ: x -> x đảo ngược (không lặp chữ số giữa) nối tiếp
ll dx2(ll x)
{
    ll y = x;
    while (y > 0)
    {
        x = x * 10 + (y % 10);
        y = y / 10;
    }
    return x;
}

ll n;
ll res;

int main()
{
    cin >> n;
    for (ll i = 1; i < 100000; i++)
    {
        ll k = dx1(i);
        if (k >= n)
        {
            break;
        }
        if (k > 10 && ktnt(k))
        {
            res++;
        }
        k = dx2(i);
        if (k < n && ktnt(k))
        {
            res++;
        }
    }
    cout << res;
    return 0;
}
```

```python
def ktnt(a):
    if a < 2:
        return False
    if a == 2 or a == 3:
        return True
    if a % 2 == 0 or a % 3 == 0:
        return False
    j = 5
    while j * j <= a:
        if a % j == 0 or a % (j + 2) == 0:
            return False
        j += 6
    return True


def dx1(x):
    # đối xứng độ dài chẵn
    y = x // 10
    while y > 0:
        x = x * 10 + (y % 10)
        y = y // 10
    return x


def dx2(x):
    # đối xứng độ dài lẻ
    y = x
    while y > 0:
        x = x * 10 + (y % 10)
        y = y // 10
    return x


def giai(n):
    res = 0
    i = 1
    while i < 100000:
        k = dx1(i)
        if k >= n:
            break
        if k > 10 and ktnt(k):
            res += 1
        k = dx2(i)
        if k < n and ktnt(k):
            res += 1
        i += 1
    return res


if __name__ == "__main__":
    n = int(input())
    print(giai(n))
```

</details>

<details>
<summary><b>Bài 7. TONGHV – Tổng của các hoán vị</b></summary>

**Đề bài:** Tính tổng của số x (n chữ số phân biệt) và tất cả các hoán vị chữ số của nó.

**Kỹ thuật cốt lõi:** Công thức tổ hợp: mỗi chữ số xuất hiện ở mỗi hàng đúng (n-1)! lần → `s = tổng_chữ_số * (n-1)! * (10^(n-1)+...+10^0)`, sau đó trừ đi phần hoán vị có 0 đứng đầu (không hợp lệ).

**Chương chính:** P5 – Tổ hợp (hoán vị)
**Chương phụ trợ:** P2 – Xử lý xâu/số cơ bản

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

ll lt(ll x, ll y)
{
    ll s = 1;
    for (int i = 1; i <= y; i++)
    {
        s = s * x;
    }
    return s;
}

ll gt(ll x)
{
    ll s = 1;
    for (int i = 1; i <= x; i++)
    {
        s = s * i;
    }
    return s;
}

string str(ll x)
{
    string s = "";
    while (x > 0)
    {
        int k = x % 10;
        x = x / 10;
        s = (char)(k + 48) + s;
    }
    return s;
}

int main()
{
    ll n;
    cin >> n;
    ll n1 = n;
    ll s = 0;
    int d = 0;
    bool so0 = false;
    while (n1 > 0)
    {
        if (n1 % 10 == 0)
        {
            so0 = true;
        }
        s = s + n1 % 10;
        d++;
        n1 = n1 / 10;
    }

    ll res = 0;
    for (int i = 0; i < d; i++)
    {
        res = res + s * gt(d - 1) * lt(10, i);
    }

    ll res1 = 0;
    if (so0 == true)
    {
        for (int i = 0; i < d - 1; i++)
        {
            res1 = res1 + s * gt(d - 2) * lt(10, i);
        }
    }

    cout << res - res1;
    return 0;
}
```

```python
from itertools import permutations


def giai_cong_thuc(n):
    s_str = str(n)
    d = len(s_str)
    tong_cs = sum(int(c) for c in s_str)
    co_so_0 = "0" in s_str

    def giai_thua(x):
        r = 1
        for i in range(1, x + 1):
            r *= i
        return r

    res = 0
    for i in range(d):
        res += tong_cs * giai_thua(d - 1) * (10 ** i)

    res1 = 0
    if co_so_0:
        for i in range(d - 1):
            res1 += tong_cs * giai_thua(d - 2) * (10 ** i)

    return res - res1


if __name__ == "__main__":
    n = int(input())
    print(giai_cong_thuc(n))
```

</details>

---

## 3. 2023 HSG9 DAKNONG

<details>
<summary><b>Bài 8. CHIPHI – Chi phí</b></summary>

**Đề bài:** Đặt ghế đá xen kẽ dưới n cây, cứ 2 cây liền kề đặt 1 ghế. Tìm tổng chi phí để đặt được nhiều ghế nhất.

**Kỹ thuật cốt lõi:** Công thức chẵn/lẻ đơn giản.

**Chương chính:** P1 – Nhập môn (if/else)
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;

long long n, x;

int main()
{
    cin >> n >> x;
    if (n % 2 == 0)
    {
        cout << (n / 2 * x);
    }
    else
    {
        cout << (n / 2 + 1) * x;
    }
    return 0;
}
```

```python
def giai(n, x):
    if n % 2 == 0:
        return (n // 2) * x
    else:
        return (n // 2 + 1) * x


if __name__ == "__main__":
    n, x = map(int, input().split())
    print(giai(n, x))
```

</details>

<details>
<summary><b>Bài 9. PASSWORD2 – Mật khẩu</b></summary>

**Đề bài:** Tìm các số nguyên tố trong [L, R] mà tổng các chữ số của nó cũng là số nguyên tố.

**Kỹ thuật cốt lõi:** Sàng nguyên tố (Sieve of Eratosthenes) để kiểm tra nhanh.

**Chương chính:** P5 – Số học (sàng nguyên tố)
**Chương phụ trợ:** P2 – Mảng đếm

```cpp
#include <bits/stdc++.h>
using namespace std;

int p[10000005];

void sang(int maxx)
{
    p[0] = 1;
    p[1] = 1;
    for (int i = 2; i <= (int)sqrt(maxx); i++)
    {
        if (p[i] == 0)
        {
            int j = i * i;
            while (j <= maxx)
            {
                p[j] = 1;
                j = j + i;
            }
        }
    }
}

int tongcs(int x)
{
    int res = 0;
    while (x > 0)
    {
        res = res + x % 10;
        x = x / 10;
    }
    return res;
}

int main()
{
    int L, R;
    cin >> L >> R;
    sang(R);
    for (int i = L; i <= R; i++)
    {
        if (p[i] == 0 && p[tongcs(i)] == 0)
        {
            cout << i << " ";
        }
    }
    return 0;
}
```

```python
def sang(maxx):
    p = [False] * (maxx + 1)
    p[0] = True
    if maxx >= 1:
        p[1] = True
    i = 2
    while i * i <= maxx:
        if not p[i]:
            j = i * i
            while j <= maxx:
                p[j] = True
                j += i
        i += 1
    return p


def tongcs(x):
    res = 0
    while x > 0:
        res += x % 10
        x //= 10
    return res


def giai(L, R):
    p = sang(R)
    ket_qua = []
    for i in range(L, R + 1):
        if not p[i] and not p[tongcs(i)]:
            ket_qua.append(i)
    return ket_qua


if __name__ == "__main__":
    L, R = map(int, input().split())
    print(*giai(L, R))
```

</details>

<details>
<summary><b>Bài 10. PHANTU1 – Phân tử lượng</b></summary>

**Đề bài:** Cho công thức hóa học gồm H, O, N, C, tính phân tử lượng.

**Kỹ thuật cốt lõi:** Duyệt xâu, tách số và chữ cái.

**Chương chính:** P7 – Xử lý xâu cơ bản
**Chương phụ trợ:** P1 – Nhập môn

```cpp
#include <bits/stdc++.h>
using namespace std;

int tinh(char c, int x)
{
    if (c == 'H')
    {
        return 1 * x;
    }
    if (c == 'O')
    {
        return 16 * x;
    }
    if (c == 'N')
    {
        return 14 * x;
    }
    if (c == 'C')
    {
        return 12 * x;
    }
    return 0;
}

int main()
{
    string s;
    cin >> s;
    int x = 0;
    char c = ' ';
    long long res = 0;

    for (int i = 0; i < (int)s.size(); i++)
    {
        if ('0' <= s[i] && s[i] <= '9')
        {
            x = x * 10 + (int)(s[i]) - 48;
        }
        else
        {
            if (x > 0)
            {
                res = res + tinh(c, x);
                x = 0;
            }
            c = s[i];
        }
    }
    res = res + tinh(c, x);
    cout << res;
    return 0;
}
```

```python
def tinh(c, x):
    bang = {"H": 1, "O": 16, "N": 14, "C": 12}
    return bang.get(c, 0) * x


def giai(s):
    x = 0
    c = " "
    res = 0
    for ch in s:
        if ch.isdigit():
            x = x * 10 + int(ch)
        else:
            if x > 0:
                res += tinh(c, x)
                x = 0
            c = ch
    res += tinh(c, x)
    return res


if __name__ == "__main__":
    s = input()
    print(giai(s))
```

</details>

<details>
<summary><b>Bài 11. FLASHBACK – Virus</b></summary>

**Đề bài:** Sinh sản virus theo quy luật đặc biệt, sau k ngày có bao nhiêu cá thể (mod 10^9+7).

**Kỹ thuật cốt lõi:** Nhận diện dãy đặc biệt liên hệ với dãy Fibonacci: `a(0)=1, a(i)=f(2i)`.

**Chương chính:** P3 – Dãy truy hồi/DP cơ bản
**Chương phụ trợ:** P5 – Số học modulo

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = (int)2e5 + 100;
const long long INF = 1e18;
const int MOD = (int)1e9 + 7;
int n, k;
long long f[N];
long long a[N];

int main()
{
    f[0] = 0;
    f[1] = 1;
    for (int i = 2; i < N; i++)
    {
        f[i] = (f[i - 1] + f[i - 2]) % MOD;
    }

    a[0] = 1;
    for (int i = 1; i <= (int)1e5; i++)
    {
        a[i] = f[i * 2];
    }

    cin >> n >> k;
    long long res = 0;
    for (int i = 0; i <= k; i++)
    {
        res = (res + a[i]) % MOD;
    }
    cout << 1LL * res * n % MOD;
    return 0;
}
```

```python
MOD = 10 ** 9 + 7


def giai(n, k):
    size = k * 2 + 5
    f = [0] * size
    f[1] = 1
    for i in range(2, size):
        f[i] = (f[i - 1] + f[i - 2]) % MOD

    a = [0] * (k + 1)
    a[0] = 1
    for i in range(1, k + 1):
        a[i] = f[i * 2]

    res = 0
    for i in range(k + 1):
        res = (res + a[i]) % MOD

    return (res * n) % MOD


if __name__ == "__main__":
    n, k = map(int, input().split())
    print(giai(n, k))
```

</details>

---

## 4. 2023 HSG9 HANOI

<details>
<summary><b>Bài 12. TG2 – Thời gian</b></summary>

**Đề bài:** Cho thời gian sát hạch của từng thí sinh (bắt đầu 8:00), tính thời điểm kết thúc dạng giờ:phút.

**Kỹ thuật cốt lõi:** Phép chia lấy dư/nguyên để đổi phút sang giờ:phút, định dạng số 0 phía trước.

**Chương chính:** P1 – Nhập môn
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    int n, a, g, p;
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> a;
        g = 8 + a / 60;
        p = a % 60;
        if (g < 10)
        {
            cout << "0";
        }
        cout << g << ":";
        if (p < 10)
        {
            cout << "0";
        }
        cout << p << endl;
    }
    return 0;
}
```

```python
def solve(n_phut):
    gio = 8 + n_phut // 60
    phut = n_phut % 60
    if gio >= 10:
        h = str(gio) + ":"
    else:
        h = "0" + str(gio) + ":"
    if phut >= 10:
        m = str(phut)
    else:
        m = "0" + str(phut)
    return h + m


if __name__ == "__main__":
    n = int(input())
    for _ in range(n):
        a = int(input())
        print(solve(a))
```

</details>

<details>
<summary><b>Bài 13. MM1 – Mật mã</b></summary>

**Đề bài:** Đếm số lượng số nguyên phân biệt (bỏ số 0 vô nghĩa đầu) xuất hiện trong xâu.

**Kỹ thuật cốt lõi:** Duyệt xâu tách cụm chữ số, chuyển thành số, dùng mảng đếm đánh dấu giá trị đã gặp.

**Chương chính:** P2 – Mảng đếm cơ bản
**Chương phụ trợ:** P7 – Xử lý xâu

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

ll xau_thanh_so(string s)
{
    ll u = 0;
    for (int i = 0; i < (int)s.size(); i++)
    {
        if (u == 0 && s[i] == '0')
        {
            continue;
        }
        u = u * 10 + ((int)(s[i]) - 48);
    }
    return u;
}

int d[1000];

int main()
{
    string s, t = "";
    cin >> s;
    for (int i = 0; i < (int)s.size(); i++)
    {
        if ('0' <= s[i] && s[i] <= '9')
        {
            t = t + s[i];
        }
        else
        {
            if (t != "")
            {
                ll k = xau_thanh_so(t);
                d[k]++;
            }
            t = "";
        }
    }
    if (t != "")
    {
        ll k = xau_thanh_so(t);
        d[k]++;
    }

    ll res = 0;
    for (int i = 0; i < 1000; i++)
    {
        if (d[i] > 0)
        {
            res++;
        }
    }
    cout << res;
    return 0;
}
```

```python
def xau_thanh_so(t):
    return int(t)


def giai(s):
    tap_so = set()
    t = ""
    for ch in s:
        if ch.isdigit():
            t += ch
        else:
            if t != "":
                tap_so.add(xau_thanh_so(t))
            t = ""
    if t != "":
        tap_so.add(xau_thanh_so(t))
    return len(tap_so)


if __name__ == "__main__":
    s = input()
    print(giai(s))
```

</details>

<details>
<summary><b>Bài 14. TPS – Trạm phát sóng</b></summary>

**Đề bài:** Đặt K trạm phát sóng trên đường tròn 10^6 điểm sao cho phủ hết N trạm thu, tìm bán kính phát nhỏ nhất.

**Kỹ thuật cốt lõi:** Tìm kiếm nhị phân trên đáp án (Binary search on answer) kết hợp Greedy kiểm tra khả thi (check).

**Chương chính:** P2 – Sort/Binary search
**Chương phụ trợ:** P8 – Greedy

**Ý tưởng:** Với bán kính `x` cho trước, hàm `check(x)` tham lam đặt trạm phát bắt đầu từ trạm thu đầu tiên chưa được phủ, đặt tiếp tới khi hết. Vì `x` càng lớn thì càng dễ thỏa mãn (số trạm cần càng ít), nên nhị phân được trên `x`.

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

int a[1005], n, k;

bool check(ll x)
{
    ll t = a[1] + x;
    ll d = 1;
    for (int i = 2; i <= n; i++)
    {
        if (a[i] > t + x)
        {
            d++;
            t = a[i] + x;
        }
    }

    t = a[2] + x;
    ll d1 = 1;
    for (int i = 3; i <= n + 1; i++)
    {
        if (a[i] > t + x)
        {
            d1++;
            t = a[i] + x;
        }
    }

    return min(d, d1) <= k;
}

ll bs(ll l, ll r)
{
    ll res = 0;
    while (l <= r)
    {
        ll mid = (l + r) / 2;
        if (check(mid))
        {
            res = mid;
            r = mid - 1;
        }
        else
        {
            l = mid + 1;
        }
    }
    return res;
}

int main()
{
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    cin >> k;
    sort(a + 1, a + 1 + n);
    a[n + 1] = (int)1e6 + a[1];
    cout << bs(1, (int)1e6);
    return 0;
}
```

```python
def check(x, a, n, k):
    t = a[1] + x
    d = 1
    for i in range(2, n + 1):
        if a[i] > t + x:
            d += 1
            t = a[i] + x

    t = a[2] + x
    d1 = 1
    for i in range(3, n + 2):
        if a[i] > t + x:
            d1 += 1
            t = a[i] + x

    return min(d, d1) <= k


def giai(n, a, k):
    a = a[:]  # a đánh chỉ số từ 1
    a.append(10 ** 6 + a[1])  # a[n+1]
    l, r = 1, 10 ** 6
    res = 0
    while l <= r:
        mid = (l + r) // 2
        if check(mid, a, n, k):
            res = mid
            r = mid - 1
        else:
            l = mid + 1
    return res


if __name__ == "__main__":
    n = int(input())
    a = [0] + [int(input()) for _ in range(n)]
    k = int(input())
    a[1:n + 1] = sorted(a[1:n + 1])
    print(giai(n, a, k))
```

</details>

<details>
<summary><b>Bài 15. TL – Triển lãm</b></summary>

**Đề bài:** Chọn 1 đoạn bức tranh liên tiếp (sau khi sort theo kích thước) để tối đa hóa `H = tổng_giá_trị - (kích_thước_max - kích_thước_min)`.

**Kỹ thuật cốt lõi:** Sort theo kích thước, quy về bài toán chọn đoạn con liên tiếp; dùng mảng tiền tố và mảng "max tiền tố" để tính O(N).

**Chương chính:** P2 – Prefix sum + Sort
**Chương phụ trợ:** P3 – (biến đổi công thức DP)

**Ý tưởng:** Sau khi sort theo A tăng dần, chọn đoạn `[L, R]` cho `cost(L,R) = (preB[R] - preB[L-1]) - (A[R]-A[L])`. Biến đổi: `cost(L,R) = (preB[R] - A[R]) + (A[L] - preB[L-1])`. Với mỗi `R`, cần `max` của `(A[L] - preB[L-1])` trên `L <= R`, gọi là `maxx[R]`, tính tiền tố dần.

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;

struct tranh
{
    ll a, b;
};

const int maxN = (int)1e6 + 9;
ll n, pre[maxN], maxx[maxN];
tranh x[maxN];

bool cmp(tranh u, tranh v)
{
    return u.a < v.a;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> x[i].a >> x[i].b;
    }
    sort(x + 1, x + n + 1, cmp);

    pre[0] = 0;
    for (int i = 1; i <= n; i++)
    {
        pre[i] = pre[i - 1] + x[i].b;
    }

    maxx[1] = x[1].a - pre[0];
    for (int i = 2; i <= n; i++)
    {
        maxx[i] = max(maxx[i - 1], x[i].a - pre[i - 1]);
    }

    ll res = 0;
    for (int i = 1; i <= n; i++)
    {
        res = max(res, (pre[i] - x[i].a) + maxx[i]);
    }
    cout << res;
    return 0;
}
```

```python
def giai(n, tranh_list):
    # tranh_list: danh sách các tuple (a, b), chỉ số từ 1
    tranh_list_sorted = sorted(tranh_list, key=lambda t: t[0])
    a = [0] * (n + 1)
    b = [0] * (n + 1)
    for i in range(1, n + 1):
        a[i], b[i] = tranh_list_sorted[i - 1]

    pre = [0] * (n + 1)
    for i in range(1, n + 1):
        pre[i] = pre[i - 1] + b[i]

    maxx = [0] * (n + 1)
    maxx[1] = a[1] - pre[0]
    for i in range(2, n + 1):
        maxx[i] = max(maxx[i - 1], a[i] - pre[i - 1])

    res = 0
    for i in range(1, n + 1):
        res = max(res, (pre[i] - a[i]) + maxx[i])
    return res


if __name__ == "__main__":
    n = int(input())
    tranh_list = []
    for _ in range(n):
        ai, bi = map(int, input().split())
        tranh_list.append((ai, bi))
    print(giai(n, tranh_list))
```

</details>

<details>
<summary><b>Bài 16. DD – Dãy đẹp</b></summary>

**Đề bài:** Đếm số dãy con liên tiếp của A mà khi sắp xếp tăng dần trở thành dãy số nguyên liên tục.

**Kỹ thuật cốt lõi:** Nhận xét `max(l,r) - min(l,r) = r - l` và các phần tử phân biệt ⇒ dùng cấu trúc dữ liệu (stack đơn điệu tính prefix max/min, hoặc chia căn/Fenwick nâng cao) để đếm O(N log N) hoặc O(N√N).

**Chương chính:** P6 – Cấu trúc dữ liệu nâng cao (chia căn / kỹ thuật nâng cao)
**Chương phụ trợ:** P2 – Two pointers/Monotonic stack (bài toán con "max - min = length" cơ bản)

**Lời giải cơ bản (Subtask 1, 2 – O(N²)):** Duyệt tất cả đoạn `[i, j]`, kiểm tra phân biệt bằng mảng đếm và điều kiện `max - min = j - i`.

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;

const int maxN = (int)1e5 + 9;
int n, a[maxN];

void sub12()
{
    ll ans = 0;
    for (int i = 1; i <= n; i++)
    {
        int d[2005] = {0};
        int maxi = -1;
        int mini = INT_MAX;
        for (int j = i; j <= n; j++)
        {
            if (d[a[j]] == 0)
            {
                d[a[j]]++;
            }
            else
            {
                break;
            }
            maxi = max(maxi, a[j]);
            mini = min(mini, a[j]);
            if (j - i == maxi - mini)
            {
                ans++;
            }
        }
    }
    cout << ans;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    sub12();
    return 0;
}
```

*(Lời giải tối ưu O(N√N) dùng chia căn kết hợp stack đơn điệu, xem chi tiết trong tài liệu gốc trang 29–31; đây là kỹ thuật nâng cao thuộc Phần VI, không bắt buộc với học sinh mới học.)*

```python
def sub_co_ban(n, a):
    # a: 1-indexed list, độ phức tạp O(n^2) - dùng cho n nhỏ (n <= 2000)
    ans = 0
    for i in range(1, n + 1):
        da_gap = set()
        maxi = -1
        mini = float("inf")
        for j in range(i, n + 1):
            if a[j] in da_gap:
                break
            da_gap.add(a[j])
            maxi = max(maxi, a[j])
            mini = min(mini, a[j])
            if j - i == maxi - mini:
                ans += 1
    return ans


if __name__ == "__main__":
    n = int(input())
    a = [0] + list(map(int, input().split()))
    print(sub_co_ban(n, a))
```

</details>

---

## 5. 2023 HSG9 HAUGIANG

<details>
<summary><b>Bài 17. KYTU – Ký tự</b></summary>

**Đề bài:** Cho xâu S chỉ gồm chữ in hoa, tìm các ký tự còn thiếu.

**Kỹ thuật cốt lõi:** Mảng đếm 26 chữ cái.

**Chương chính:** P2 – Mảng đếm cơ bản
**Chương phụ trợ:** P7 – Xử lý xâu

```cpp
#include <bits/stdc++.h>
using namespace std;

int d[500];

int main()
{
    string s;
    cin >> s;
    for (int i = 0; i < (int)s.size(); i++)
    {
        d[(int)s[i]]++;
    }
    for (int i = (int)'A'; i <= (int)'Z'; i++)
    {
        if (d[i] == 0)
        {
            cout << (char)i;
        }
    }
    return 0;
}
```

```python
def giai(s):
    xuat_hien = set(s)
    ket_qua = ""
    for i in range(ord("A"), ord("Z") + 1):
        c = chr(i)
        if c not in xuat_hien:
            ket_qua += c
    return ket_qua


if __name__ == "__main__":
    s = input()
    print(giai(s))
```

</details>

<details>
<summary><b>Bài 18. DT – Đổi tiền</b></summary>

**Đề bài:** Đổi số tiền N thành 7 loại mệnh giá sao cho số tờ mệnh giá lớn ≤ số tờ mệnh giá nhỏ hơn, tổng số tờ ít nhất.

**Kỹ thuật cốt lõi:** Duyệt mệnh giá từ lớn đến nhỏ, với mỗi mệnh giá dùng tìm kiếm nhị phân để tìm số tờ lớn nhất thỏa ràng buộc "không vượt quá n khi phân bổ hết cho các mệnh giá còn lại".

**Chương chính:** P8 – Greedy (tham lam có ràng buộc)
**Chương phụ trợ:** P2 – Binary search / Prefix sum

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

int n;
int a[8] = {0, 1, 2, 5, 10, 20, 50, 100};
ll sum[8];
int ans[8];

void giai()
{
    for (int i = 1; i <= 7; i++)
    {
        sum[i] = sum[i - 1] + a[i];
    }
    for (int i = 7; i >= 1; i--)
    {
        int l = 0, r = (int)1e5, res = 0;
        while (l <= r)
        {
            int mid = (l + r) / 2;
            if (sum[i] * mid <= n)
            {
                res = mid;
                l = mid + 1;
            }
            else
            {
                r = mid - 1;
            }
        }
        ans[i] = res;
        n -= res * a[i];
    }
    for (int i = 1; i <= 7; i++)
    {
        cout << a[i] << " " << ans[i] << '\n';
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    cin >> n;
    giai();
    return 0;
}
```

```python
def giai(n):
    a = [0, 1, 2, 5, 10, 20, 50, 100]
    tong = [0] * 8
    for i in range(1, 8):
        tong[i] = tong[i - 1] + a[i]

    ans = [0] * 8
    for i in range(7, 0, -1):
        l, r, res = 0, 10 ** 5, 0
        while l <= r:
            mid = (l + r) // 2
            if tong[i] * mid <= n:
                res = mid
                l = mid + 1
            else:
                r = mid - 1
        ans[i] = res
        n -= res * a[i]

    ket_qua = []
    for i in range(1, 8):
        ket_qua.append((a[i], ans[i]))
    return ket_qua


if __name__ == "__main__":
    n = int(input())
    for menh_gia, so_to in giai(n):
        print(menh_gia, so_to)
```

</details>

<details>
<summary><b>Bài 19. TS – Tìm số</b></summary>

**Đề bài:** Tìm số nguyên dương P nhỏ nhất lớn hơn M, chứa nhiều nhất số nguyên phân biệt có trong dãy A.

**Kỹ thuật cốt lõi:** Vét cạn quay lui (backtracking) sinh số nhỏ nhất từ tập chữ số cho phép, kết hợp so sánh xâu.

**Chương chính:** P2 – Backtracking
**Chương phụ trợ:** P7 – Xử lý xâu (so sánh số dạng xâu)

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> a;
int d[100], mm, dd;
int n;
string m, ss, minn;

void nhap()
{
    cin >> n >> m;
    int x;
    for (int i = 1; i <= n; i++)
    {
        cin >> x;
        d[x + 48] = 1;
    }
    for (int i = '0'; i <= '9'; i++)
    {
        if (d[i] == 1)
        {
            a.push_back(i);
        }
    }
}

char b[20];

bool nho(string x, string y)
{
    if (x.size() < y.size())
    {
        return true;
    }
    if (x.size() > y.size())
    {
        return false;
    }
    if (x < y)
    {
        return true;
    }
    return false;
}

void vet(int i)
{
    for (int j = '0'; j <= '9'; j++)
    {
        if (d[j] == 1)
        {
            b[i] = (char)j;
            d[j] = 0;
            if (i == mm - 1)
            {
                ss = "";
                for (int k = 0; k <= mm - 1; k++)
                {
                    ss = ss + b[k];
                }
                if (ss > m && nho(ss, minn) == true)
                {
                    minn = ss;
                }
            }
            else
            {
                vet(i + 1);
            }
            d[j] = 1;
        }
    }
}

void giai()
{
    mm = (int)m.size();
    dd = (int)a.size();
    if (dd < mm)
    {
        cout << "Khong tim duoc";
        return;
    }

    minn = "";
    if (dd == mm)
    {
        for (int i = '9'; i >= '0'; i--)
        {
            if (d[i] == 1)
            {
                minn = minn + (char)i;
            }
        }
        if (minn <= m)
        {
            cout << "Khong tim duoc";
            return;
        }
    }

    if (dd > mm)
    {
        for (int i = '0'; i <= '9'; i++)
        {
            if (d[i] == 1)
            {
                minn = minn + (char)i;
            }
        }
        if (minn[0] == '0')
        {
            char t = minn[0];
            minn[0] = minn[1];
            minn[1] = t;
        }
    }

    for (int i = (int)m[0]; i <= '9'; i++)
    {
        if (d[i] == 1)
        {
            b[0] = (char)i;
            d[i] = 0;
            vet(1);
            d[i] = 1;
        }
    }
    cout << minn;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    nhap();
    giai();
    return 0;
}
```

```python
def giai(n, m, chu_so_a):
    m_len = len(m)
    ki_hieu = sorted(set(chu_so_a))  # danh sách chữ số phân biệt, dạng str
    dd = len(ki_hieu)

    if dd < m_len:
        return "Khong tim duoc"

    if dd == m_len:
        minn = "".join(sorted(ki_hieu, reverse=True))
        if minn <= m:
            return "Khong tim duoc"
        # dd == m_len nhưng minn hợp lệ (trường hợp hiếm, đề đảm bảo không xảy ra khi hợp lệ)

    best = [None]  # dùng list để có thể gán trong closure

    def hop_le(so_str):
        if best[0] is None:
            return True
        if len(so_str) != len(best[0]):
            return len(so_str) < len(best[0])
        return so_str < best[0]

    da_dung = [False] * len(ki_hieu)
    b = [""] * (dd + 1)
    do_dai_can = dd if dd > m_len else m_len

    def vet(i, do_dai):
        if i == do_dai:
            ss = "".join(b[:do_dai])
            if ss[0] != "0" and ss > m and hop_le(ss):
                best[0] = ss
            return
        for j in range(len(ki_hieu)):
            if not da_dung[j]:
                b[i] = ki_hieu[j]
                da_dung[j] = True
                vet(i + 1, do_dai)
                da_dung[j] = False

    vet(0, do_dai_can)

    if best[0] is None:
        return "Khong tim duoc"
    return best[0]


if __name__ == "__main__":
    n, m = input().split()
    n = int(n)
    chu_so_a = [input().strip() for _ in range(n)]
    print(giai(n, m, chu_so_a))
```

> Lưu ý: bài này có nhiều trường hợp biên phức tạp (số 0 đứng đầu, độ dài bằng/khác độ dài M). Bản Python trên là bản đơn giản hóa lại tư tưởng vét cạn của bản C++ gốc; với dữ liệu lớn nên tối ưu như bản C++ (xây `minn` trực tiếp thay vì vét toàn bộ hoán vị).

</details>

---

## 6. 2023 HSG9 HCM

<details>
<summary><b>Bài 20. TBC1 – Trung bình cộng</b></summary>

**Đề bài:** Cho mảng B với B[i] là trung bình cộng của i phần tử đầu mảng A, tìm mảng A.

**Kỹ thuật cốt lõi:** Biến đổi công thức tổng tiền tố: `a[i] = i*b[i] - s[i-1]`.

**Chương chính:** P2 – Prefix sum
**Chương phụ trợ:** P1 – Nhập môn

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = (int)1e6 + 5;
long long a[N], b[N], s[N], n;

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> b[i];
    }

    a[1] = b[1];
    s[1] = a[1];
    for (int i = 1; i <= n; i++)
    {
        a[i] = i * b[i] - s[i - 1];
        s[i] = s[i - 1] + a[i];
    }

    for (int i = 1; i <= n; i++)
    {
        cout << a[i] << " ";
    }
    return 0;
}
```

```python
def giai(n, b):
    # b: 1-indexed list
    a = [0] * (n + 1)
    s = [0] * (n + 1)
    a[1] = b[1]
    s[1] = a[1]
    for i in range(1, n + 1):
        a[i] = i * b[i] - s[i - 1]
        s[i] = s[i - 1] + a[i]
    return a[1:n + 1]


if __name__ == "__main__":
    n = int(input())
    b = [0] + list(map(int, input().split()))
    print(*giai(n, b))
```

</details>

<details>
<summary><b>Bài 21. MATMA – Mật mã</b></summary>

**Đề bài:** Xâu có m ký tự '#', mỗi ký tự thay bằng 1 trong k ký tự cho trước. Tìm xâu thứ x theo thứ tự từ điển.

**Kỹ thuật cốt lõi:** Sort các phương án thay thế tăng dần, dùng vét cạn quay lui để sinh danh sách theo đúng thứ tự từ điển (đếm tới x thì dừng).

**Chương chính:** P2 – Backtracking
**Chương phụ trợ:** P2 – Sort

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m, k, x, d = 0;
string s;
char a[1000][1000];
char e[1000];
bool ok = false;

void vet(int i)
{
    if (ok == true)
    {
        return;
    }
    for (int j = 1; j <= k; j++)
    {
        if (ok == true)
        {
            return;
        }
        e[i] = a[i][j];
        if (i == m)
        {
            d++;
            if (d == x)
            {
                ok = true;
                return;
            }
        }
        else
        {
            vet(i + 1);
        }
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin >> n >> m >> k >> x;
    cin >> s;
    for (int i = 1; i <= m; i++)
    {
        for (int j = 1; j <= k; j++)
        {
            cin >> a[i][j];
        }
        sort(a[i] + 1, a[i] + 1 + k);
    }

    vet(1);

    int t = 1;
    for (int i = 0; i < (int)s.size(); i++)
    {
        if (s[i] == '#')
        {
            cout << e[t];
            t++;
        }
        else
        {
            cout << s[i];
        }
    }
    return 0;
}
```

```python
def giai(n, m, k, x, s, phuong_an):
    # phuong_an: list các list ký tự (index 1..m -> dùng 0-indexed nội bộ)
    phuong_an_sorted = [sorted(p) for p in phuong_an]

    e = [""] * (m + 1)
    dem = [0]
    ket_qua = [None]

    def vet(i):
        if ket_qua[0] is not None:
            return
        for ky_tu in phuong_an_sorted[i - 1]:
            if ket_qua[0] is not None:
                return
            e[i] = ky_tu
            if i == m:
                dem[0] += 1
                if dem[0] == x:
                    ket_qua[0] = e[1:m + 1][:]
                    return
            else:
                vet(i + 1)

    vet(1)

    t = 0
    out = []
    for ch in s:
        if ch == "#":
            out.append(ket_qua[0][t])
            t += 1
        else:
            out.append(ch)
    return "".join(out)


if __name__ == "__main__":
    n, m, k, x = map(int, input().split())
    s = input()
    phuong_an = []
    for _ in range(m):
        phuong_an.append(list(input().strip()))
    print(giai(n, m, k, x, s, phuong_an))
```

</details>

<details>
<summary><b>Bài 22. SOXO – Xổ số</b></summary>

**Đề bài:** Xét tất cả đoạn con K phần tử của mảng A, tính tổng các số lớn nhất của các đoạn con đó, mod 10^9+7.

**Kỹ thuật cốt lõi:** Sort mảng, dùng tổ hợp `C(i-1, k-1)` (số cách chọn k-1 phần tử nhỏ hơn đứng trước `a[i]`), tính lũy thừa nhanh (chia để trị) cho nghịch đảo modulo.

**Chương chính:** P5 – Tổ hợp & số học modulo
**Chương phụ trợ:** P2 – Sort

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long m = 1000000007;
long long a[100005], n, k;
long long gt[1000005];
long long g[1000005];

long long lt(long long a, long long n)
{
    if (n == 0)
    {
        return 1;
    }
    long long t = lt(a, n / 2);
    t = ((t % m) * (t % m)) % m;
    if (n % 2 == 0)
    {
        return t;
    }
    return ((t % m) * (a % m)) % m;
}

long long th(long long k, long long n)
{
    long long B = gt[k] * gt[n - k] % m;
    long long s = (gt[n] * lt(B, m - 2)) % m;
    return s;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    sort(a + 1, a + 1 + n);

    gt[0] = 1;
    for (int i = 1; i <= n; i++)
    {
        gt[i] = (gt[i - 1] * i) % m;
    }

    long long res = 0;
    for (int i = (int)k; i <= n; i++)
    {
        res = (res + a[i] * th(k - 1, i - 1)) % m;
    }
    cout << res;
    return 0;
}
```

```python
MOD = 1000000007


def lt(a, n):
    # lũy thừa nhanh a^n mod MOD
    return pow(a, n, MOD)


def to_hop(k, n, gt):
    # C(k, n) mod MOD dùng nghịch đảo Fermat
    if k < 0 or k > n:
        return 0
    B = (gt[k] * gt[n - k]) % MOD
    return (gt[n] * lt(B, MOD - 2)) % MOD


def giai(n, k, a):
    # a: 1-indexed, đã sort tăng dần
    gt = [1] * (n + 1)
    for i in range(1, n + 1):
        gt[i] = (gt[i - 1] * i) % MOD

    res = 0
    for i in range(k, n + 1):
        res = (res + a[i] * to_hop(k - 1, i - 1, gt)) % MOD
    return res


if __name__ == "__main__":
    n, k = map(int, input().split())
    a = [0] + list(map(int, input().split()))
    a[1:n + 1] = sorted(a[1:n + 1])
    print(giai(n, k, a))
```

</details>

---

## 7. 2023 HSG9 KHANHHOA

<details>
<summary><b>Bài 23. ATHORUA – Vượt chướng ngại vật</b></summary>

**Đề bài:** Thỏ và Rùa leo dốc V mét, mỗi ngày leo A mét, mỗi đêm tụt B mét (trừ khi đã lên đỉnh). Ai lên đỉnh với số ngày ít hơn thì thắng.

**Kỹ thuật cốt lõi:** Mô phỏng vòng lặp trực tiếp.

**Chương chính:** P1 – Nhập môn (mô phỏng)
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;

int giai(int x, int y, int h)
{
    int s = 0, d = 0;
    while (s < h)
    {
        s += x;
        d += 1;
        if (s >= h)
        {
            break;
        }
        else
        {
            s -= y;
        }
    }
    return d;
}

int main()
{
    int v, a1, b1, a2, b2;
    cin >> v >> a1 >> b1 >> a2 >> b2;
    int d1 = giai(a1, b1, v);
    int d2 = giai(a2, b2, v);
    if (d1 < d2)
    {
        cout << "THO";
    }
    if (d1 == d2)
    {
        cout << "HOA";
    }
    if (d1 > d2)
    {
        cout << "RUA";
    }
    return 0;
}
```

```python
def giai(x, y, h):
    s = 0
    d = 0
    while s < h:
        s += x
        d += 1
        if s >= h:
            break
        else:
            s -= y
    return d


if __name__ == "__main__":
    v, a1, b1, a2, b2 = map(int, [input(), input(), input(), input(), input()])
    d1 = giai(a1, b1, v)
    d2 = giai(a2, b2, v)
    if d1 < d2:
        print("THO")
    elif d1 == d2:
        print("HOA")
    else:
        print("RUA")
```

</details>

<details>
<summary><b>Bài 24. CHIABANH1 – Chia bánh</b></summary>

**Đề bài:** Chọn 4 mảnh liên tiếp trong 8 mảnh bánh (vòng cố định) có tổng nắm nhiều nhất.

**Kỹ thuật cốt lõi:** Cửa sổ trượt (sliding window) kích thước cố định.

**Chương chính:** P2 – Sliding window
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    int a[9];
    for (int i = 1; i <= 8; i++)
    {
        cin >> a[i];
    }

    int s = 0;
    for (int i = 1; i <= 4; i++)
    {
        s = s + a[i];
    }
    int maxx = s;

    for (int i = 5; i <= 8; i++)
    {
        s = s + a[i] - a[i - 4];
        if (s > maxx)
        {
            maxx = s;
        }
    }
    cout << maxx;
    return 0;
}
```

```python
def giai(a):
    # a: 1-indexed, độ dài 8
    s = sum(a[1:5])
    maxx = s
    for i in range(5, 9):
        s = s + a[i] - a[i - 4]
        if s > maxx:
            maxx = s
    return maxx


if __name__ == "__main__":
    a = [0] + list(map(int, input().split()))
    print(giai(a))
```

</details>

<details>
<summary><b>Bài 25. NUMBER1 – Chữ số thứ K</b></summary>

**Đề bài:** Tìm chữ số thứ K trong chuỗi số tự nhiên nối tiếp "123456789101112...".

**Kỹ thuật cốt lõi:** Phân tích theo nhóm chữ số cùng độ dài (bài toán kinh điển "digit at position").

**Chương chính:** P5 – Số học (phân tích theo lũy thừa 10)
**Chương phụ trợ:** P7 – Xử lý xâu

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

ll n, k, res;

string str(ll x)
{
    string s = "";
    while (x > 0)
    {
        int k2 = x % 10;
        x = x / 10;
        s = (char)(k2 + 48) + s;
    }
    return s;
}

void tim_so_n()
{
    ll s[105];
    ll kk = 1;
    s[1] = 9;
    for (int i = 2; i <= 17; i++)
    {
        kk = kk * 10;
        s[i] = i * 9 * kk;
    }

    int i;
    for (i = 1; i <= 17; i++)
    {
        if (n - s[i] >= 0)
        {
            n = n - s[i];
        }
        else
        {
            break;
        }
    }

    ll t = 1;
    for (int j = 1; j < i; j++)
    {
        t = t * 10;
    }
    // t = 10^(i-1)

    ll du = n % i;
    ll thuong = n / i;
    ll so_can_tim = t + thuong - (du == 0 ? 1 : 0);
    string st = str(so_can_tim);

    if (du == 0)
    {
        cout << st[i - 1] - '0';
    }
    else
    {
        cout << st[du - 1] - '0';
    }
}

int main()
{
    cin >> n;
    tim_so_n();
    return 0;
}
```

```python
def giai(k):
    n = k
    do_dai = 1
    so_luong = 9
    while n > do_dai * so_luong:
        n -= do_dai * so_luong
        do_dai += 1
        so_luong *= 10

    # n bây giờ chỉ vị trí trong nhóm các số có "do_dai" chữ số
    so_bat_dau = 10 ** (do_dai - 1)
    chi_so_so = (n - 1) // do_dai  # số thứ mấy trong nhóm (0-indexed)
    chi_so_chu_so = (n - 1) % do_dai  # vị trí chữ số trong số đó (0-indexed)

    so_can_tim = so_bat_dau + chi_so_so
    chuoi_so = str(so_can_tim)
    return int(chuoi_so[chi_so_chu_so])


if __name__ == "__main__":
    k = int(input())
    print(giai(k))
```

</details>

<details>
<summary><b>Bài 26. RCHIAKEO – Chia kẹo</b></summary>

**Đề bài:** Chia M viên kẹo cho N học sinh (mỗi em muốn số lượng khác nhau), tối thiểu hóa tổng bình phương số kẹo thiếu.

**Kỹ thuật cốt lõi:** Sort mong muốn tăng dần, chia đều số kẹo thiếu trung bình cho các học sinh (có xét ràng buộc "không thể thiếu nhiều hơn mong muốn"), duyệt điểm chia tối ưu.

**Chương chính:** P8 – Greedy
**Chương phụ trợ:** P2 – Sort

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

ll m, n, s;
ll a[100000];

int main()
{
    ios_base::sync_with_stdio(0);
    cout.tie(0);
    cin.tie(0);

    cin >> m >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        s = s + a[i];
    }
    s = s - m; // s = tổng số kẹo còn thiếu

    sort(a + 1, a + 1 + n);
    ll minn = (ll)1e18;
    a[0] = 0;

    for (int i = 0; i <= n; i++)
    {
        ll res = 0;
        ll j, s1 = s;
        for (j = 1; j <= i; j++)
        {
            s1 = s1 - a[j];
            res = res + a[j] * a[j];
        }
        if (s1 < 0)
        {
            continue;
        }

        ll u = n - j + 1; // số học sinh còn lại (nhận k hoặc k+1)
        ll k = s1 / u;
        if (a[j] < k)
        {
            continue;
        }
        ll v = s1 % u;

        res = res + v * (k + 1) * (k + 1) + (u - v) * k * k;
        if (res < minn)
        {
            minn = res;
        }
    }
    cout << minn;
    return 0;
}
```

```python
def giai(m, n, mong_muon):
    # mong_muon: 1-indexed list
    s = sum(mong_muon[1:n + 1]) - m  # tổng số kẹo còn thiếu
    a = [0] + sorted(mong_muon[1:n + 1])

    minn = float("inf")
    for i in range(0, n + 1):
        res = 0
        s1 = s
        j = 1
        while j <= i:
            s1 -= a[j]
            res += a[j] * a[j]
            j += 1
        if s1 < 0:
            continue

        u = n - j + 1
        if u == 0:
            continue
        k = s1 // u
        if a[j] < k:
            continue
        v = s1 % u

        res = res + v * (k + 1) * (k + 1) + (u - v) * k * k
        if res < minn:
            minn = res

    return minn


if __name__ == "__main__":
    m, n = map(int, input().split())
    mong_muon = [0] + [int(input()) for _ in range(n)]
    print(giai(m, n, mong_muon))
```

</details>

---

## 8. 2023 HSG9 LAMDONG

<details>
<summary><b>Bài 27. TINHTONG2 – Tính tổng</b></summary>

**Đề bài:** Tính tổng `1.2.3 + 2.3.4 + ... + (n-1).n.(n+1)` với n có thể tới 10^20 (số lớn).

**Kỹ thuật cốt lõi:** Biến đổi công thức về tổng lập phương và tổng số tự nhiên, cài đặt số học số lớn (bignum) cho cộng/trừ/nhân/chia.

**Chương chính:** P5 – Toán học (biến đổi công thức)
**Chương phụ trợ:** P6 – Cấu trúc dữ liệu (biểu diễn số lớn bằng mảng/vector)

**Công thức:** `(i-1)*i*(i+1) = i^3 - i`, nên `S = (1³+2³+...+n³) - (1+2+...+n) = [n(n+1)/2]² - n(n+1)/2`.

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef vector<int> vi;

// ==== các hàm số lớn (bignum) rút gọn từ bản gốc ====
void del_zero(vi &a)
{
    reverse(a.begin(), a.end());
    while (a.size() >= 2)
    {
        if (a.back() == 0)
        {
            a.pop_back();
        }
        else
        {
            break;
        }
    }
    reverse(a.begin(), a.end());
}

void add_zero(vi &a, int sz)
{
    reverse(a.begin(), a.end());
    while ((int)a.size() < sz)
    {
        a.push_back(0);
    }
    reverse(a.begin(), a.end());
}

void change(vi &a, vi &b)
{
    int sz = max(a.size(), b.size());
    add_zero(a, sz);
    add_zero(b, sz);
}

vi operator+(vi a, vi b)
{
    change(a, b);
    int sz = (int)a.size();
    vi c;
    int rem = 0;
    for (int i = sz - 1; i >= 0; --i)
    {
        int x = a[i] + b[i] + rem;
        rem = x / 10;
        x %= 10;
        c.push_back(x);
    }
    c.push_back(rem);
    reverse(c.begin(), c.end());
    del_zero(c);
    return c;
}

vi operator-(vi a, vi b)
{
    change(a, b);
    int sz = (int)a.size();
    vi c;
    int rem = 0;
    for (int i = sz - 1; i >= 0; --i)
    {
        int x = a[i] - b[i] - rem;
        if (x < 0)
        {
            x += 10;
            rem = 1;
        }
        else
        {
            rem = 0;
        }
        c.push_back(x);
    }
    reverse(c.begin(), c.end());
    del_zero(c);
    return c;
}

vi operator*(vi a, vi b)
{
    reverse(a.begin(), a.end());
    reverse(b.begin(), b.end());
    vi c(a.size() + b.size() + 1, 0);
    for (int i = 0; i < (int)a.size(); ++i)
    {
        for (int j = 0; j < (int)b.size(); ++j)
        {
            c[i + j] += a[i] * b[j];
            c[i + j + 1] += c[i + j] / 10;
            c[i + j] %= 10;
        }
    }
    c.push_back(0);
    for (int i = 0; i < (int)c.size() - 1; ++i)
    {
        c[i + 1] += c[i] / 10;
        c[i] %= 10;
    }
    reverse(c.begin(), c.end());
    del_zero(c);
    return c;
}

vi int_to_vi(int n)
{
    vi res;
    if (n == 0)
    {
        res.push_back(n);
        return res;
    }
    while (n)
    {
        res.push_back(n % 10);
        n /= 10;
    }
    reverse(res.begin(), res.end());
    return res;
}

void xuat(vi a)
{
    for (int i = 0; i < (int)a.size(); i++)
    {
        cout << a[i];
    }
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    string nn;
    cin >> nn;
    vi n;
    for (int i = 0; i < (int)nn.size(); i++)
    {
        n.push_back((int)(nn[i]) - 48);
    }

    vi m = int_to_vi(1);
    vi tot = n * (n + m) / 2LL; // lưu ý: chia số lớn cho 2 cần hàm operator/ riêng
    vi sum = tot * tot - tot;
    xuat(sum);
    return 0;
}
```

> Ghi chú: bản C++ đầy đủ (bao gồm cả `operator/`) có trong tài liệu gốc trang 51–53; do Python hỗ trợ số nguyên lớn (big integer) sẵn có nên không cần tự cài bignum.

```python
def giai(n_str):
    n = int(n_str)
    tot = n * (n + 1) // 2
    s = tot * tot - tot
    return s


if __name__ == "__main__":
    n_str = input().strip()
    print(giai(n_str))
```

</details>

<details>
<summary><b>Bài 28. GOL1 – Giả thuyết Goldbach</b></summary>

**Đề bài:** Với mỗi số chẵn k, tìm cặp số nguyên tố có tổng bằng k, ưu tiên cặp có hiệu tuyệt đối lớn nhất.

**Kỹ thuật cốt lõi:** Sàng nguyên tố sinh danh sách số nguyên tố ≤ 10^6, duyệt từ số nguyên tố nhỏ nhất lên, kiểm tra `k - p` có là số nguyên tố không.

**Chương chính:** P5 – Số học (sàng nguyên tố)
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;
long long k, n, d;
long long t;
int a[1000005], p[1000005];

void sang(int maxx)
{
    p[0] = 1;
    p[1] = 1;
    for (int i = 2; i * i <= maxx; i++)
    {
        if (p[i] == 0)
        {
            for (int j = i * i; j <= maxx; j += i)
            {
                p[j] = 1;
            }
        }
    }
    d = 0;
    for (int i = 1; i <= maxx; i++)
    {
        if (p[i] == 0)
        {
            d++;
            a[d] = i;
        }
    }
}

bool ktnt(long long x)
{
    if (x < 2)
    {
        return false;
    }
    for (int i = 2; i <= (int)sqrt(x); i++)
    {
        if (x % i == 0)
        {
            return false;
        }
    }
    return true;
}

void giai()
{
    cin >> n;
    for (int i = 1; i <= d; i++)
    {
        if (ktnt(n - a[i]) == true)
        {
            cout << a[i] << " " << n - a[i] << endl;
            return;
        }
    }
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    sang((int)1e6);
    int t2;
    cin >> t2;
    for (int i = 1; i <= t2; i++)
    {
        giai();
    }
    return 0;
}
```

```python
import math


def sang(maxx):
    p = [False] * (maxx + 1)
    p[0] = p[1] = True
    i = 2
    while i * i <= maxx:
        if not p[i]:
            for j in range(i * i, maxx + 1, i):
                p[j] = True
        i += 1
    ds_nguyen_to = [i for i in range(2, maxx + 1) if not p[i]]
    return ds_nguyen_to


def ktnt(x):
    if x < 2:
        return False
    for i in range(2, int(math.isqrt(x)) + 1):
        if x % i == 0:
            return False
    return True


def giai(n, ds_nguyen_to):
    for p in ds_nguyen_to:
        if p > n:
            break
        if ktnt(n - p):
            return p, n - p
    return None


if __name__ == "__main__":
    ds_nguyen_to = sang(10 ** 6)
    t2 = int(input())
    for _ in range(t2):
        n = int(input())
        cap = giai(n, ds_nguyen_to)
        if cap:
            print(cap[0], cap[1])
```

</details>

<details>
<summary><b>Bài 29. DS – Dãy số</b></summary>

**Đề bài:** Xử lý Q thao tác trên dãy A: (1) tăng đoạn `[p, m]` thêm x, (2) truy vấn tổng đoạn `[u, v]`.

**Kỹ thuật cốt lõi:** Segment Tree với Lazy Propagation (cây phân đoạn có cập nhật khoảng).

**Chương chính:** P6 – Cấu trúc dữ liệu nâng cao (Segment Tree)
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = (int)1e5 + 5;
int n, a[N], q;

long long st[4 * N];
long long lz[4 * N];

void build(int id, int l, int r)
{
    if (l == r)
    {
        st[id] = a[l];
        return;
    }
    int mid = (l + r) / 2;
    build(id * 2, l, mid);
    build(id * 2 + 1, mid + 1, r);
    st[id] = st[id * 2] + st[id * 2 + 1];
}

void push(int id, int l, int r)
{
    if (lz[id] == 0)
    {
        return;
    }
    int mid = (l + r) / 2;
    st[id * 2] += lz[id] * (mid - l + 1);
    st[id * 2 + 1] += lz[id] * (r - mid);
    lz[id * 2] += lz[id];
    lz[id * 2 + 1] += lz[id];
    lz[id] = 0;
}

void add(int u, int v, int val, int id, int l, int r)
{
    if (l > v || r < u)
    {
        return;
    }
    if (u <= l && r <= v)
    {
        st[id] += (long long)val * (r - l + 1);
        lz[id] += val;
        return;
    }
    push(id, l, r);
    int mid = (l + r) / 2;
    add(u, v, val, id * 2, l, mid);
    add(u, v, val, id * 2 + 1, mid + 1, r);
    st[id] = st[id * 2] + st[id * 2 + 1];
}

long long query(int u, int v, int id, int l, int r)
{
    if (l > v || r < u)
    {
        return 0;
    }
    if (u <= l && r <= v)
    {
        return st[id];
    }
    push(id, l, r);
    int mid = (l + r) / 2;
    return query(u, v, id * 2, l, mid) + query(u, v, id * 2 + 1, mid + 1, r);
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> q;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    build(1, 1, n);

    while (q--)
    {
        int t, l, r;
        cin >> t >> l >> r;
        if (t == 1)
        {
            int x;
            cin >> x;
            add(l, r, x, 1, 1, n);
        }
        else
        {
            cout << query(l, r, 1, 1, n) << endl;
        }
    }
    return 0;
}
```

```python
import sys


class SegmentTree:
    def __init__(self, a):
        # a: 1-indexed list, a[0] không dùng
        self.n = len(a) - 1
        self.st = [0] * (4 * (self.n + 5))
        self.lz = [0] * (4 * (self.n + 5))
        self.a = a
        self.build(1, 1, self.n)

    def build(self, id_, l, r):
        if l == r:
            self.st[id_] = self.a[l]
            return
        mid = (l + r) // 2
        self.build(id_ * 2, l, mid)
        self.build(id_ * 2 + 1, mid + 1, r)
        self.st[id_] = self.st[id_ * 2] + self.st[id_ * 2 + 1]

    def push(self, id_, l, r):
        if self.lz[id_] == 0:
            return
        mid = (l + r) // 2
        self.st[id_ * 2] += self.lz[id_] * (mid - l + 1)
        self.st[id_ * 2 + 1] += self.lz[id_] * (r - mid)
        self.lz[id_ * 2] += self.lz[id_]
        self.lz[id_ * 2 + 1] += self.lz[id_]
        self.lz[id_] = 0

    def add(self, u, v, val, id_=1, l=None, r=None):
        if l is None:
            l, r = 1, self.n
        if l > v or r < u:
            return
        if u <= l and r <= v:
            self.st[id_] += val * (r - l + 1)
            self.lz[id_] += val
            return
        self.push(id_, l, r)
        mid = (l + r) // 2
        self.add(u, v, val, id_ * 2, l, mid)
        self.add(u, v, val, id_ * 2 + 1, mid + 1, r)
        self.st[id_] = self.st[id_ * 2] + self.st[id_ * 2 + 1]

    def query(self, u, v, id_=1, l=None, r=None):
        if l is None:
            l, r = 1, self.n
        if l > v or r < u:
            return 0
        if u <= l and r <= v:
            return self.st[id_]
        self.push(id_, l, r)
        mid = (l + r) // 2
        return self.query(u, v, id_ * 2, l, mid) + self.query(u, v, id_ * 2 + 1, mid + 1, r)


def main():
    data = sys.stdin.read().split()
    idx = 0
    n = int(data[idx]); idx += 1
    q = int(data[idx]); idx += 1
    a = [0] * (n + 1)
    for i in range(1, n + 1):
        a[i] = int(data[idx]); idx += 1

    seg = SegmentTree(a)
    ket_qua = []
    for _ in range(q):
        t = int(data[idx]); idx += 1
        l = int(data[idx]); idx += 1
        r = int(data[idx]); idx += 1
        if t == 1:
            x = int(data[idx]); idx += 1
            seg.add(l, r, x)
        else:
            ket_qua.append(str(seg.query(l, r)))
    print("\n".join(ket_qua))


if __name__ == "__main__":
    main()
```

</details>

<details>
<summary><b>Bài 30. CTNT – Chương trình nghệ thuật</b></summary>

**Đề bài:** Thuê nghệ sĩ biểu diễn liên tục trong n giờ với chi phí nhỏ nhất, sao cho mọi thời điểm có ít nhất 1 người diễn.

**Kỹ thuật cốt lõi:** Quy hoạch động trên trục thời gian (giống bài toán "Job scheduling to cover a line").

**Chương chính:** P3 – Quy hoạch động cơ bản
**Chương phụ trợ:** —

**Ý tưởng:** `dp[i]` = chi phí nhỏ nhất để đã "phủ" tới thời điểm i. `dp[0] = 0`. Với mỗi i đã có lời giải tối ưu, duyệt các nghệ sĩ j có `l[j] <= i`, cập nhật `dp[r[j]] = min(dp[r[j]], dp[i] + c[j])`.

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = (int)1e2 + 5;
int l[N], r[N], n, m, dp[N], c[N];

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> m;
    memset(dp, 0x3f, sizeof dp);
    for (int i = 1; i <= m; i++)
    {
        cin >> l[i] >> r[i] >> c[i];
    }

    dp[0] = 0;
    for (int i = 0; i < n; i++)
    {
        if (dp[i] < (int)1e9)
        {
            for (int j = 1; j <= m; j++)
            {
                if (l[j] <= i)
                {
                    if (dp[r[j]] >= dp[i] + c[j])
                    {
                        dp[r[j]] = dp[i] + c[j];
                    }
                }
            }
        }
    }
    cout << dp[n];
    return 0;
}
```

```python
def giai(n, m, nghe_si):
    # nghe_si: list các tuple (l, r, c), 0-indexed trong list nhưng thời gian tính từ 0
    INF = float("inf")
    dp = [INF] * (n + 1)
    dp[0] = 0

    for i in range(0, n):
        if dp[i] < INF:
            for (l, r, c) in nghe_si:
                if l <= i:
                    if dp[r] > dp[i] + c:
                        dp[r] = dp[i] + c

    return dp[n]


if __name__ == "__main__":
    n, m = map(int, input().split())
    nghe_si = []
    for _ in range(m):
        li, ri, ci = map(int, input().split())
        nghe_si.append((li, ri, ci))
    print(giai(n, m, nghe_si))
```

</details>

---

## 9. 2023 HSG9 NGHEAN

<details>
<summary><b>Bài 31. NTCN – Cặp số nguyên tố cùng nhau</b></summary>

**Đề bài:** Đếm số lượng x < n sao cho `gcd(x, n) = 1`.

**Kỹ thuật cốt lõi:** Hàm phi Euler `φ(n) = n * Π(1 - 1/p)` với p là các ước nguyên tố của n.

**Chương chính:** P5 – Số học (hàm Euler)
**Chương phụ trợ:** P1 – Nhập môn (duyệt trực tiếp cho subtask nhỏ)

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;

int n;

void sub1()
{
    int ans = 0;
    for (int i = 1; i < n; i++)
    {
        if (__gcd(i, n) == 1)
        {
            ans++;
        }
    }
    cout << ans;
}

void sub2()
{
    ll ans = n;
    ll ans1 = 1;
    ll i = 2;
    ll m = n;
    while (i * i <= m)
    {
        if (m % i == 0)
        {
            ans1 *= i;
            ans *= (i - 1);
            while (m % i == 0)
            {
                m /= i;
            }
        }
        i++;
    }
    if (m != 1)
    {
        ans1 *= m;
        ans *= (m - 1);
    }
    cout << ans / ans1;
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> n;
    if (n <= (int)2e6)
    {
        sub1();
    }
    else
    {
        sub2();
    }
    return 0;
}
```

```python
import math


def phi_euler(n):
    ans = n
    m = n
    i = 2
    while i * i <= m:
        if m % i == 0:
            ans -= ans // i
            while m % i == 0:
                m //= i
        i += 1
    if m > 1:
        ans -= ans // m
    return ans


def giai_truc_tiep(n):
    ans = 0
    for i in range(1, n):
        if math.gcd(i, n) == 1:
            ans += 1
    return ans


if __name__ == "__main__":
    n = int(input())
    if n <= 2 * 10 ** 6:
        print(giai_truc_tiep(n))
    else:
        print(phi_euler(n))
```

</details>

<details>
<summary><b>Bài 32. HANGCAY – Hàng cây sân trường</b></summary>

**Đề bài:** M vòi nước, mỗi vòi tưới đoạn `[Xi - Ri, Xi + Ri]`. Đếm số cây được tưới.

**Kỹ thuật cốt lõi:** Mảng đánh dấu (difference array đơn giản hóa thành mảng bool).

**Chương chính:** P2 – Prefix sum / Difference array
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;

const int ar = (int)1e6 + 9;
int n, m, x, r, res = 0;
bool vs[ar];

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> n >> m;
    while (m--)
    {
        cin >> x >> r;
        for (int j = x; j <= x + r; ++j)
        {
            vs[j] = 1;
        }
        for (int j = x; j >= max(1, x - r); --j)
        {
            vs[j] = 1;
        }
    }

    for (int i = 1; i <= n; ++i)
    {
        res += vs[i];
    }
    cout << res;
    return 0;
}
```

```python
def giai(n, m, vecto):
    # vecto: list các tuple (x, r)
    vs = [False] * (n + 2)
    for (x, r) in vecto:
        lo = max(1, x - r)
        hi = min(n, x + r)
        for j in range(lo, hi + 1):
            vs[j] = True

    return sum(vs[1:n + 1])


if __name__ == "__main__":
    n, m = map(int, input().split())
    vecto = []
    for _ in range(m):
        x, r = map(int, input().split())
        vecto.append((x, r))
    print(giai(n, m, vecto))
```

> Ghi chú: với dữ liệu lớn (n, r tới 10^6, m tới n) nên dùng kỹ thuật **difference array** (`diff[l]++; diff[r+1]--;` rồi lấy tổng tiền tố) để đạt O(n + m) thay vì O(n·m) như bản trên.

</details>

<details>
<summary><b>Bài 33. CHONBONG – Trò chơi chọn bóng</b></summary>

**Đề bài:** Lấy từng cặp bóng khác màu ra khỏi rổ cho tới khi không còn cặp khác màu. Tối đa hóa số bóng lấy được.

**Kỹ thuật cốt lõi:** Đếm tần suất theo màu, sort, xét 2 trường hợp: màu chiếm đa số ≥ nửa hay < nửa.

**Chương chính:** P8 – Greedy
**Chương phụ trợ:** P2 – Sort/Mảng đếm

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, k, a[200005], s[200005];

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    for (int i = 1; i <= n; i++)
    {
        s[a[i]]++;
    }
    sort(s + 1, s + k + 1);

    if (s[k] * 2 < n)
    {
        cout << (n / 2) * 2;
    }
    else
    {
        cout << (n - s[k]) * 2;
    }
    return 0;
}
```

```python
def giai(n, k, mau):
    # mau: 1-indexed list mã màu
    dem = [0] * (k + 1)
    for i in range(1, n + 1):
        dem[mau[i]] += 1
    dem_sorted = sorted(dem[1:k + 1])
    s_k = dem_sorted[-1]  # số lần xuất hiện nhiều nhất

    if s_k * 2 < n:
        return (n // 2) * 2
    else:
        return (n - s_k) * 2


if __name__ == "__main__":
    n, k = map(int, input().split())
    mau = [0] + list(map(int, input().split()))
    print(giai(n, k, mau))
```

</details>

<details>
<summary><b>Bài 34. MATDO – Mật độ xuất hiện cao</b></summary>

**Đề bài:** Tìm đoạn con dài nhất của xâu S mà có 1 ký tự xuất hiện nhiều hơn tổng các ký tự còn lại.

**Kỹ thuật cốt lõi:** Với mỗi ký tự c, đổi bài toán thành dãy `+1/-1`, tìm đoạn con dài nhất có tổng dương, dùng mảng tiền tố nhỏ nhất (`prefix min`) và tìm kiếm nhị phân.

**Chương chính:** P6 – Cấu trúc dữ liệu nâng cao / kỹ thuật xử lý mảng nâng cao
**Chương phụ trợ:** P2 – Prefix sum, Binary search

```cpp
#include <bits/stdc++.h>
using namespace std;

const int maxn = 200005;
string s;
int n, res;
int sum_arr[maxn];
int f[maxn];

void sub4()
{
    for (char c = 'a'; c <= 'z'; ++c)
    {
        memset(sum_arr, 0, sizeof sum_arr);
        memset(f, 0x3f, sizeof f);

        for (int i = 1; i <= n; ++i)
        {
            sum_arr[i] = sum_arr[i - 1] + (c == s[i] ? 1 : -1);
            f[i] = min(f[i - 1], sum_arr[i]);
        }

        for (int i = 1; i <= n; ++i)
        {
            int l = 1, r = i, pos = i;
            while (l <= r)
            {
                int mid = (l + r) / 2;
                if (sum_arr[i] - f[mid - 1] > 0)
                {
                    pos = mid;
                    r = mid - 1;
                }
                else
                {
                    l = mid + 1;
                }
            }
            res = max(res, i - pos + 1);
        }
    }
    cout << res;
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> s;
    n = (int)s.size();
    s = " " + s;
    sub4();
    return 0;
}
```

```python
def giai(s):
    n = len(s)
    s = " " + s  # 1-indexed
    res = 0

    for code in range(ord("a"), ord("z") + 1):
        c = chr(code)
        sum_arr = [0] * (n + 1)
        f = [float("inf")] * (n + 1)

        for i in range(1, n + 1):
            sum_arr[i] = sum_arr[i - 1] + (1 if s[i] == c else -1)
            f[i] = min(f[i - 1], sum_arr[i])

        for i in range(1, n + 1):
            l, r, pos = 1, i, i
            while l <= r:
                mid = (l + r) // 2
                if sum_arr[i] - f[mid - 1] > 0:
                    pos = mid
                    r = mid - 1
                else:
                    l = mid + 1
            res = max(res, i - pos + 1)

    return res


if __name__ == "__main__":
    s = input()
    print(giai(s))
```

</details>

---

## 10. 2023 HSG9 QUANGTRI

<details>
<summary><b>Bài 35. TANCUNG – Chữ số tận cùng</b></summary>

**Đề bài:** Tìm chữ số tận cùng của A^N.

**Kỹ thuật cốt lõi:** Lũy thừa nhanh theo modulo 10 (chia để trị).

**Chương chính:** P5 – Số học (lũy thừa nhanh)
**Chương phụ trợ:** P3 – Đệ quy chia đôi

```cpp
#include <bits/stdc++.h>
using namespace std;

int pw(int a, int n)
{
    if (n == 0)
    {
        return 1;
    }
    if (n == 1)
    {
        return a;
    }
    int v = pw(a, n / 2);
    v = v * v % 10;
    if (n & 1)
    {
        return v * a % 10;
    }
    return v;
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    int a, n;
    cin >> a >> n;
    cout << pw(a % 10, n);
    return 0;
}
```

```python
def giai(a, n):
    return pow(a % 10, n, 10)


if __name__ == "__main__":
    a, n = map(int, input().split())
    print(giai(a, n))
```

</details>

<details>
<summary><b>Bài 36. TRONGSO – Trọng số của xâu</b></summary>

**Đề bài:** Tìm xâu có trọng số (trung bình cộng chữ số) lớn nhất trong N xâu.

**Kỹ thuật cốt lõi:** Duyệt từng xâu, tính tổng chữ số và số lượng chữ số, so sánh trung bình cộng.

**Chương chính:** P7 – Xử lý xâu cơ bản
**Chương phụ trợ:** P2 – Mảng cơ bản

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    int t;
    cin >> t;
    string s;
    getline(cin, s);

    double res = 0;
    string ans = "";
    for (int i = 1; i <= t; i++)
    {
        double sum = 0;
        int cnt = 0;
        getline(cin, s);
        for (int j = 0; j < (int)s.size(); j++)
        {
            char c = s[j];
            if ('0' <= c && c <= '9')
            {
                sum += c - '0';
                cnt++;
            }
        }
        if (cnt == 0)
        {
            continue;
        }
        sum /= cnt;
        if (sum >= res)
        {
            res = sum;
            ans = s;
        }
    }

    if (res != 0)
    {
        cout << ans;
    }
    else
    {
        cout << 0;
    }
    return 0;
}
```

```python
def giai(danh_sach_xau):
    res = 0.0
    ans = None

    for s in danh_sach_xau:
        tong = 0
        dem = 0
        for c in s:
            if c.isdigit():
                tong += int(c)
                dem += 1
        if dem == 0:
            continue
        trung_binh = tong / dem
        if trung_binh >= res:
            res = trung_binh
            ans = s

    if res != 0:
        return ans
    else:
        return "0"


if __name__ == "__main__":
    n = int(input())
    danh_sach_xau = [input() for _ in range(n)]
    print(giai(danh_sach_xau))
```

</details>

<details>
<summary><b>Bài 37. SODB – Số đặc biệt</b></summary>

**Đề bài:** Đếm số đặc biệt trong dãy: là số nguyên tố và có số lượng chữ số chẵn khác số lượng chữ số lẻ.

**Kỹ thuật cốt lõi:** Sàng nguyên tố cho phạm vi nhỏ, kiểm tra trực tiếp cho phạm vi lớn.

**Chương chính:** P5 – Số học (sàng nguyên tố + kiểm tra nguyên tố)
**Chương phụ trợ:** P2 – Xử lý từng chữ số

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long

const ll N = (ll)2e6 + 5;
bool f[N];
ll g[N], m = 0;

bool ck(ll x)
{
    if (x < N)
    {
        return !f[x];
    }
    for (ll i = 1; i <= m && g[i] <= (ll)sqrt((double)x); ++i)
    {
        if (x % g[i] == 0)
        {
            return false;
        }
    }
    return true;
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    f[0] = f[1] = 1;
    for (ll i = 2; i < N; ++i)
    {
        if (!f[i])
        {
            g[++m] = i;
            for (ll j = i * i; j < N; j += i)
            {
                f[j] = 1;
            }
        }
    }

    ll res = 0;
    ll n;
    cin >> n;
    for (ll i = 1; i <= n; i++)
    {
        ll x;
        cin >> x;
        if (x <= 1)
        {
            continue;
        }
        if (ck(x))
        {
            ll c1 = 0, c2 = 0;
            while (x)
            {
                if (x & 1)
                {
                    c1++;
                }
                else
                {
                    c2++;
                }
                x /= 10;
            }
            if (c1 != c2)
            {
                res++;
            }
        }
    }
    cout << res;
    return 0;
}
```

> Lưu ý: điều kiện `x & 1` trong đoạn đếm chẵn/lẻ **chữ số** ở bản gốc thực ra đang xét chữ số cuối cùng của `x` (biến `x` đang bị chia dần cho 10) — đây là cách đếm số lượng chữ số lẻ (`c1`) và chữ số chẵn (`c2`) của toàn bộ số ban đầu.

```python
import math


def sang(gioi_han):
    f = [False] * (gioi_han + 1)
    f[0] = f[1] = True
    g = []
    i = 2
    while i < gioi_han:
        if not f[i]:
            g.append(i)
            j = i * i
            while j < gioi_han:
                f[j] = True
                j += i
        i += 1
    return f, g


def ck(x, N, f, g):
    if x < N:
        return not f[x]
    gioi_han = int(math.isqrt(x))
    for p in g:
        if p > gioi_han:
            break
        if x % p == 0:
            return False
    return True


def dem_chan_le_chu_so(x):
    c_le = 0
    c_chan = 0
    while x > 0:
        chu_so = x % 10
        if chu_so % 2 == 1:
            c_le += 1
        else:
            c_chan += 1
        x //= 10
    return c_le, c_chan


def giai(n, day_a):
    N = 2 * 10 ** 6 + 5
    f, g = sang(N)
    res = 0
    for x in day_a:
        if x <= 1:
            continue
        if ck(x, N, f, g):
            c_le, c_chan = dem_chan_le_chu_so(x)
            if c_le != c_chan:
                res += 1
    return res


if __name__ == "__main__":
    n = int(input())
    day_a = list(map(int, input().split()))
    print(giai(n, day_a))
```

</details>

<details>
<summary><b>Bài 38. TONGBANG – Tổng bảng</b></summary>

**Đề bài:** Bảng số M×N: ô `(i,j)` ghi `(i-1)*N+j` nếu `i+j` chẵn, ghi 0 nếu lẻ. Tính tổng bảng mod 1532023.

**Kỹ thuật cốt lõi:** Công thức toán học tổng dãy số lẻ (nếu N lẻ) hoặc điều chỉnh cho các hàng chẵn (nếu N chẵn), dùng công thức Gauss.

**Chương chính:** P5 – Toán học (công thức tổng dãy số)
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
const ll mod = 1532023;

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    ll m, n;
    cin >> m >> n;

    ll sc = m * n;
    if (sc % 2 == 0)
    {
        sc--;
    }
    ll ssh = (sc - 1) / 2 + 1;
    ll s = (((1 + sc) / 2 % mod) * (ssh % mod)) % mod;

    if (n % 2 == 1)
    {
        cout << s;
    }
    else
    {
        cout << (s + ((m / 2) * (n / 2) % mod)) % mod;
    }
    return 0;
}
```

```python
MOD = 1532023


def giai(m, n):
    sc = m * n
    if sc % 2 == 0:
        sc -= 1
    ssh = (sc - 1) // 2 + 1
    s = (((1 + sc) // 2 % MOD) * (ssh % MOD)) % MOD

    if n % 2 == 1:
        return s
    else:
        return (s + ((m // 2) * (n // 2) % MOD)) % MOD


if __name__ == "__main__":
    m, n = map(int, input().split())
    print(giai(m, n))
```

</details>

---

## 11. 2023 HSG9 QUANGNINH

<details>
<summary><b>Bài 39. DISP – Màn hình</b></summary>

**Đề bài:** Tìm 2 số a, b sao cho `a*b = n`, `a <= b`, `b - a` nhỏ nhất.

**Kỹ thuật cốt lõi:** Duyệt ước từ `sqrt(n)` giảm dần, gặp ước hợp lệ đầu tiên là đáp án.

**Chương chính:** P5 – Số học (ước số)
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);

    int n;
    cin >> n;
    for (int a = (int)sqrt(n); a > 0; a--)
    {
        if (n % a == 0)
        {
            cout << a << " " << n / a;
            break;
        }
    }
    return 0;
}
```

```python
import math


def giai(n):
    a = int(math.isqrt(n))
    while a > 0:
        if n % a == 0:
            return a, n // a
        a -= 1
    return None


if __name__ == "__main__":
    n = int(input())
    a, b = giai(n)
    print(a, b)
```

</details>

<details>
<summary><b>Bài 40. INCR – Dãy số tăng</b></summary>

**Đề bài:** Cộng thêm d vào các phần tử để dãy trở thành dãy tăng ngặt. Tìm số thao tác ít nhất.

**Kỹ thuật cốt lõi:** Tham lam, duyệt từ trái sang phải, tính số lần cộng cần thiết cho từng phần tử.

**Chương chính:** P8 – Greedy
**Chương phụ trợ:** P1 – Nhập môn

```cpp
#include <bits/stdc++.h>
using namespace std;

long long a[1000000];

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);

    long long n, d;
    cin >> n >> d;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }

    long long res = 0;
    for (int i = 2; i <= n; i++)
    {
        if (a[i] <= a[i - 1])
        {
            long long x = a[i - 1] - a[i];
            res = res + x / d + 1;
            a[i] = a[i] + d * (x / d + 1);
        }
    }
    cout << res;
    return 0;
}
```

```python
def giai(n, d, a):
    # a: 1-indexed list
    res = 0
    for i in range(2, n + 1):
        if a[i] <= a[i - 1]:
            x = a[i - 1] - a[i]
            so_lan = x // d + 1
            res += so_lan
            a[i] += d * so_lan
    return res


if __name__ == "__main__":
    n, d = map(int, input().split())
    a = [0] + list(map(int, input().split()))
    print(giai(n, d, a))
```

</details>

<details>
<summary><b>Bài 41. DONU – Bánh mì và bánh rán</b></summary>

**Đề bài:** Chọn đoạn K ký tự liên tiếp, đổi hết '0' trong đoạn thành '1'. Tìm dãy '1' liên tiếp dài nhất có thể đạt được.

**Kỹ thuật cốt lõi:** Mảng tiền tố (đếm dãy '1' liên tiếp từ trái) và mảng hậu tố (đếm dãy '1' liên tiếp từ phải), kết hợp tại từng vị trí chọn đoạn.

**Chương chính:** P2 – Prefix sum / Suffix array đơn giản
**Chương phụ trợ:** —

```cpp
#include <bits/stdc++.h>
using namespace std;

const int A = 1000005;
int n, k;
string s;
int f[A], g[A];

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);

    cin >> n >> k >> s;
    s = " " + s;

    for (int i = 1; i <= n; i++)
    {
        if (s[i] == '1')
        {
            f[i] = f[i - 1] + 1;
        }
    }
    for (int i = n; i >= 1; i--)
    {
        if (s[i] == '1')
        {
            g[i] = g[i + 1] + 1;
        }
    }

    int res = 0;
    for (int i = 1; i <= n - k + 1; i++)
    {
        res = max(res, f[i - 1] + k + g[i + k]);
    }
    cout << res;
    return 0;
}
```

```python
def giai(n, k, s):
    s = " " + s  # 1-indexed
    f = [0] * (n + 2)
    g = [0] * (n + 2)

    for i in range(1, n + 1):
        if s[i] == "1":
            f[i] = f[i - 1] + 1

    for i in range(n, 0, -1):
        if s[i] == "1":
            g[i] = g[i + 1] + 1

    res = 0
    for i in range(1, n - k + 2):
        res = max(res, f[i - 1] + k + g[i + k])

    return res


if __name__ == "__main__":
    n, k = map(int, input().split())
    s = input()
    print(giai(n, k, s))
```

</details>

<details>
<summary><b>Bài 42. PHYS – Giờ học giáo dục thể chất</b></summary>

**Đề bài:** Với mỗi cặp học sinh (i, j), tính khoảng cách lớn nhất có thể đạt được sau khi sắp xếp đúng 1 đoạn `[l, r]` tăng dần (l ≤ i < j ≤ r). Tính tổng tất cả các cặp.

**Kỹ thuật cốt lõi:** Với mỗi cặp `(i, j)`, đáp án là `max` của 3 trường hợp: (1) đoạn sort chỉ gồm đúng `[i, j]`; (2) đoạn sort mở rộng về bên trái tới `i` (dùng số phần tử ≤ `p[i]` trong đoạn `[1, i]`); (3) đoạn sort mở rộng về bên phải tới `j`. Tính trước 2 mảng phụ trợ `f[i]` (số phần tử ≤ `p[i]` trong `[1, i]`) và `g[j]` (số phần tử ≥ `p[j]` trong `[j, n]`) bằng O(N²) rồi cộng dồn.

**Chương chính:** P3 / P6 – Kỹ thuật xử lý mảng nâng cao (đếm cặp bằng tiền tố hai chiều)
**Chương phụ trợ:** P2 – Mảng đếm/prefix cơ bản

```cpp
#include <bits/stdc++.h>
using namespace std;

const int A = 3005;
int n;
long long p[A], f[A], g[A];

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);

    cin >> n;
    for (int i = 1; i <= n; ++i)
    {
        cin >> p[i];
    }

    for (int i = 1; i <= n; ++i)
    {
        for (int j = 1; j <= i; ++j)
        {
            if (p[j] <= p[i])
            {
                ++f[i]; // số phần tử trong đoạn [1, i] có giá trị <= p[i]
            }
        }
        g[i] = n + 1;
        for (int j = i; j <= n; ++j)
        {
            if (p[j] >= p[i])
            {
                --g[i]; // (n+1) - số phần tử trong đoạn [i, n] có giá trị >= p[i]
            }
        }
    }

    long long res = 0;
    for (int i = 1; i < n; ++i)
    {
        for (int j = i + 1; j <= n; ++j)
        {
            res += max({abs(p[i] - p[j]), (long long)(j - f[i]), g[j] - (long long)i});
        }
    }
    cout << res;
    return 0;
}
```

```python
def giai(n, p):
    # p: 1-indexed list
    f = [0] * (n + 1)
    g = [0] * (n + 1)

    for i in range(1, n + 1):
        for j in range(1, i + 1):
            if p[j] <= p[i]:
                f[i] += 1
        g[i] = n + 1
        for j in range(i, n + 1):
            if p[j] >= p[i]:
                g[i] -= 1

    res = 0
    for i in range(1, n):
        for j in range(i + 1, n + 1):
            truong_hop_1 = abs(p[i] - p[j])
            truong_hop_2 = j - f[i]
            truong_hop_3 = g[j] - i
            res += max(truong_hop_1, truong_hop_2, truong_hop_3)

    return res


if __name__ == "__main__":
    n = int(input())
    p = [0] + list(map(int, input().split()))
    print(giai(n, p))
```

> Lưu ý: cả bản C++ và Python trên đều O(N²) — phù hợp với ràng buộc N ≤ 3000 của đề.

</details>

---

## Bảng tổng hợp nhanh (kỹ thuật → bài)

| Kỹ thuật cốt lõi | Các bài |
|---|---|
| Số học (ước số, nguyên tố, sàng, lũy thừa nhanh, Euler) | 1, 4, 6, 7, 9, 25, 28, 31, 35, 37, 38, 39 |
| Mảng đếm / xử lý xâu cơ bản | 5, 10, 13, 17, 36 |
| Prefix sum / Difference array / Sliding window | 20, 24, 32, 41 |
| Sort + Binary search | 2, 14, 18 |
| Quy hoạch động (DP) | 2, 3, 30 |
| Backtracking (vét cạn quay lui) | 19, 21 |
| Greedy (tham lam) | 18, 26, 33, 40 |
| Cấu trúc dữ liệu nâng cao (Segment Tree, chia căn) | 16, 29 |
| Kỹ thuật nâng cao / kết hợp nhiều kỹ thuật | 15, 22, 27, 34, 42 |
| Mô phỏng cơ bản | 8, 12, 23 |

---

*Tài liệu tổng hợp phục vụ tra cứu cá nhân — mỗi lời giải giữ nguyên tư tưởng thuật toán gốc từ tài liệu HSG Tin học 9 năm 2022–2023, được viết lại tường minh (không gộp lệnh) và bổ sung bản dịch Python song song với bản C++.*
