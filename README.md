# Đề cương tổng hợp: Giáo trình Luyện thi HSG/Olympic Tin học
*(Tổng hợp cấu trúc từ: Giải thuật và Lập trình + Kỹ thuật lập trình nâng cao — Lê Minh Hoàng; Competitive Programmer's Handbook — Laaksonen; Competitive Programming — Halim; CLRS)*

---

## PHẦN I — NHẬP MÔN & NỀN TẢNG
**Chương 1. Nhập môn lập trình thi đấu**
1.1. Cấu trúc một bài toán và quy tắc đọc đề
1.2. I/O nhanh, quản lý thời gian/bộ nhớ
1.3. Kỹ năng debug và test biên

**Chương 2. Độ phức tạp thuật toán**
2.1. Ký hiệu Big-O và bảng ước lượng theo giới hạn dữ liệu
2.2. Master Theorem
2.3. Phân tích khấu hao (Amortized Analysis)

**Chương 3. Cấu trúc dữ liệu cơ bản (STL/Built-in)**
3.1. Vector, mảng động
3.2. Stack, Queue, Deque
3.3. Set, Map (cây cân bằng) và Unordered Set/Map (hash table)
3.4. Pair, Tuple, Priority Queue/Heap

---

## PHẦN II — KỸ THUẬT XỬ LÝ MẢNG & CHUỖI CƠ BẢN
**Chương 4. Sắp xếp & Tìm kiếm nhị phân**
4.1. Sắp xếp với comparator tuỳ chỉnh
4.2. Tìm kiếm nhị phân cổ điển
4.3. Tìm kiếm nhị phân trên đáp án (Binary Search on Answer)
4.4. Tìm kiếm tam phân (Ternary Search)

**Chương 5. Mảng cộng dồn & Mảng hiệu**
5.1. Prefix Sum 1D
5.2. Difference Array 1D
5.3. Prefix Sum 2D
5.4. Prefix XOR và các biến thể

**Chương 6. Hai con trỏ & Cửa sổ trượt**
6.1. Two Pointers (đối xứng 2 đầu)
6.2. Sliding Window kích thước cố định
6.3. Sliding Window kích thước thay đổi
6.4. Monotonic Stack (ngăn xếp đơn điệu)
6.5. Monotonic Queue/Deque (hàng đợi đơn điệu)

**Chương 7. Đệ quy & Quay lui (Backtracking)**
7.1. Tư duy đệ quy và cây trạng thái
7.2. Sinh hoán vị, tổ hợp, tập con
7.3. Kỹ thuật cắt tỉa (Pruning)
7.4. Chia để trị (Divide and Conquer) cơ bản

---

## PHẦN III — QUY HOẠCH ĐỘNG
**Chương 8. Quy hoạch động cơ bản**
8.1. Tư duy 4 bước xây dựng DP
8.2. DP 1 chiều: LIS, Coin Change
8.3. DP 2 chiều: Knapsack, LCS, Edit Distance
8.4. DP trên khoảng (Interval DP)

**Chương 9. Quy hoạch động trên cấu trúc đặc biệt**
9.1. Tree DP (DP trên cây)
9.2. Rerooting Technique (DP đổi gốc)
9.3. Bitmask DP
9.4. DP theo trạng thái kết hợp đồ thị (TSP)

**Chương 10. Quy hoạch động nâng cao**
10.1. DP tối ưu bằng Monotonic Queue (Sliding Window Optimization)
10.2. Convex Hull Trick
10.3. Divide and Conquer Optimization
10.4. Digit DP

---

## PHẦN IV — LÝ THUYẾT ĐỒ THỊ
**Chương 11. Nền tảng đồ thị**
11.1. Biểu diễn đồ thị (ma trận kề, danh sách kề)
11.2. DFS, BFS và các ứng dụng
11.3. Kiểm tra chu trình, tô màu 2 phía
11.4. Sắp xếp tô-pô (Topological Sort)

**Chương 12. Đường đi ngắn nhất**
12.1. Dijkstra
12.2. Bellman-Ford và phát hiện chu trình âm
12.3. Floyd-Warshall
12.4. 0-1 BFS

**Chương 13. Cây khung & Tập hợp rời rạc**
13.1. Disjoint Set Union (DSU)
13.2. Kruskal's Algorithm
13.3. Prim's Algorithm
13.4. DSU nâng cao (Union by Rank/Size, Path Compression, Weighted DSU)

**Chương 14. Đồ thị hai phía & Ghép cặp**
14.1. Kiểm tra đồ thị hai phía (Bipartite Check)
14.2. Ghép cặp cực đại (Bipartite Matching)
14.3. Thuật toán Hungarian (giới thiệu)

**Chương 15. Luồng trên mạng (Network Flow)**
15.1. Bài toán luồng cực đại
15.2. Ford-Fulkerson / Edmonds-Karp
15.3. Lát cắt cực tiểu (Min-Cut)
15.4. Ứng dụng luồng vào bài toán ghép cặp/lập lịch

**Chương 16. Cấu trúc nâng cao trên đồ thị**
16.1. Thành phần liên thông mạnh (Tarjan/Kosaraju)
16.2. Cầu và khớp (Bridges & Articulation Points)
16.3. LCA (Lowest Common Ancestor) — Binary Lifting
16.4. Heavy-Light Decomposition (giới thiệu)

---

## PHẦN V — TOÁN HỌC & TỔ HỢP
**Chương 17. Số học cơ bản**
17.1. Sàng số nguyên tố (Sieve of Eratosthenes)
17.2. UCLN/BCNN, thuật toán Euclid
17.3. Lũy thừa nhanh (Fast Exponentiation)
17.4. Số học modulo

**Chương 18. Tổ hợp**
18.1. Công thức tổ hợp C(n,k) mod p
18.2. Nguyên lý bù trừ (Inclusion-Exclusion)
18.3. Hoán vị có ràng buộc

**Chương 19. Toán học nâng cao**
19.1. Thuật toán Euclid mở rộng & Nghịch đảo modulo tổng quát
19.2. Định lý số dư Trung Hoa (CRT)
19.3. Ma trận và lũy thừa ma trận
19.4. Xác suất & Kỳ vọng trong lập trình thi đấu
19.5. Hàm Euler (Euler's Totient Function)

---

## PHẦN VI — CẤU TRÚC DỮ LIỆU NÂNG CAO
**Chương 20. Cấu trúc truy vấn khoảng**
20.1. Fenwick Tree (Binary Indexed Tree)
20.2. Segment Tree (Range Sum/Min/Max)
20.3. Lazy Propagation
20.4. Sparse Table (Range Minimum Query)

**Chương 21. Kỹ thuật hỗ trợ**
21.1. Dồn tọa độ (Coordinate Compression)
21.2. Offline Query & Sắp xếp truy vấn
21.3. Sweep Line (Quét dòng)
21.4. Mo's Algorithm

**Chương 22. Cấu trúc dữ liệu chuyên biệt (nâng cao)**
22.1. Persistent Segment Tree (giới thiệu)
22.2. Segment Tree trên cây (Euler Tour + Segment Tree)
22.3. Trie (Cây tiền tố)
22.4. Trie nhị phân cho bài toán XOR

---

## PHẦN VII — XỬ LÝ XÂU
**Chương 23. Tìm kiếm mẫu**
23.1. KMP (Knuth-Morris-Pratt)
23.2. Z-function
23.3. So khớp nhiều mẫu (giới thiệu Aho-Corasick)

**Chương 24. Hashing & Cấu trúc xâu nâng cao**
24.1. Rolling Hash / Polynomial Hash
24.2. Double Hashing chống va chạm
24.3. Manacher's Algorithm (Palindrome)
24.4. Suffix Array & LCP Array (giới thiệu)

---

## PHẦN VIII — KỸ THUẬT ĐẶC BIỆT
**Chương 25. Thuật toán tham lam (Greedy)**
25.1. Exchange Argument — chứng minh tính đúng đắn
25.2. Bài toán chọn hoạt động, lịch trình
25.3. Mã hoá Huffman

**Chương 26. Chia để trị nâng cao**
26.1. Đếm nghịch thế bằng Merge Sort
26.2. Closest Pair of Points
26.3. CDQ Divide and Conquer (giới thiệu)

**Chương 27. Kỹ thuật tìm kiếm không gian trạng thái**
27.1. Meet in the Middle
27.2. Branch and Bound
27.3. IDA* (giới thiệu, cho bài toán tối ưu hoá)

---

## PHẦN IX — KỸ NĂNG THI ĐẤU THỰC CHIẾN
**Chương 28. Chiến thuật thi đấu**
28.1. Quản lý thời gian trong contest
28.2. Làm việc nhóm (thi đội ICPC)
28.3. Stress Testing & sinh test ngẫu nhiên
28.4. Template thi đấu chuẩn

**Chương 29. Tổng ôn tập**
29.1. Bản đồ tư duy toàn bộ kỹ thuật
29.2. Quy trình tiếp cận bài toán chưa quen
29.3. Thi thử tổng hợp theo cấu trúc đề thật

---
