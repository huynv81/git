# Git theo First Principles

## 1. Bắt đầu từ bài toán gốc

Nếu phân tích Git theo **First Principles**, đừng bắt đầu từ:

```bash
git add
git commit
git push
```

Hãy bắt đầu từ câu hỏi:

> Git thực sự phải giải quyết bài toán gì?

Git cần giải quyết 4 việc cốt lõi:

1. **Lưu nội dung**
2. **Lưu trạng thái dự án**
3. **Lưu lịch sử**
4. **Cho nhiều nhánh lịch sử cùng tồn tại**

Từ đó, Git có thể phân rã thành 7 thành phần nền tảng:

```text
Git
│
├── 1. Blob
│     └── Nội dung file
│
├── 2. Tree
│     └── Cấu trúc thư mục + mapping tên → object
│
├── 3. Commit
│     └── Snapshot của project + parent + metadata
│
├── 4. DAG
│     └── Quan hệ giữa các commit
│
├── 5. Ref / Branch / Tag
│     └── Tên dễ nhớ trỏ tới commit
│
├── 6. HEAD
│     └── "Tôi đang đứng ở đâu?"
│
└── 7. Working Tree + Index
      ├── Working Tree = thứ đang sửa
      └── Index = snapshot chuẩn bị commit
```

Điểm quan trọng nhất là:

> **Git về bản chất không phải hệ thống lưu diff. Git là content-addressable object database lưu snapshot.**

---

# 2. Blob — Git cần lưu nội dung

Giả sử có file:

```text
hello.txt

Hello Git
```

Câu hỏi đầu tiên:

> Git phải lưu `"Hello Git"` như thế nào?

Git tạo một object loại **blob**.

```text
"Hello Git"
     │
     ▼
   SHA hash
     │
     ▼
Blob object
```

Ví dụ về mặt ý tưởng:

```text
blob abc123
content = "Hello Git"
```

Blob **không biết**:

```text
filename = hello.txt
folder = src/
owner = Huy
created_at = ...
```

Nó chỉ biết:

```text
bytes
```

Đây là một thiết kế cực kỳ quan trọng.

Nếu hai file có cùng content:

```text
a.txt → "hello"
b.txt → "hello"
```

Git có thể chỉ cần một blob:

```text
            ┌── a.txt
blob X ─────┤
            └── b.txt
```

Vì object được nhận dạng bằng content.

## First Principle

Ta cần lưu:

```text
CONTENT
```

→ **Blob ra đời.**

---

# 3. Tree — nhưng Blob không biết filename

Nếu chỉ có Blob thì Git gặp vấn đề:

```text
blob abc123 = "Hello Git"
```

Nhưng:

> Blob này tên file là gì?

Git cần một object khác để biểu diễn:

```text
directory structure
```

Đó là **Tree**.

Ví dụ project:

```text
project/
├── README.md
└── src/
    └── Main.java
```

Git biểu diễn gần như:

```text
Tree ROOT
│
├── README.md → Blob A
│
└── src       → Tree B
                    │
                    └── Main.java → Blob C
```

Tree không chứa nội dung file trực tiếp.

Nó chứa mapping:

```text
name → object
```

Ví dụ:

```text
100644 blob a123 README.md
040000 tree b456 src
```

Vậy:

```text
Blob = content

Tree = filesystem structure
```

## First Principle

Ta đã lưu được content.

Nhưng cần biết:

```text
content nào
ở file nào
trong folder nào
```

→ **Tree ra đời.**

---

# 4. Commit — cần lưu một phiên bản của project

Blob + Tree cho ta một snapshot filesystem.

Nhưng vẫn thiếu:

> Snapshot này là phiên bản nào?  
> Ai tạo?  
> Khi nào?  
> Snapshot trước đó là gì?

Git tạo object thứ ba:

```text
Commit
```

Commit chứa đại khái:

```text
commit C3
│
├── tree    → T3
├── parent  → C2
├── author
├── committer
└── message
```

Quan trọng nhất:

```text
commit
   │
   ▼
 tree
   │
   ├── file A
   ├── file B
   └── directory
```

Commit về bản chất là:

> Một object trỏ đến root tree của toàn bộ project.

Ví dụ:

```text
Commit C1
   │
   ▼
Tree T1
├── README → Blob A
└── Main   → Blob B
```

Sau khi sửa `Main`:

```text
Commit C2
   │
   ▼
Tree T2
├── README → Blob A
└── Main   → Blob C
```

Bạn thấy một điều rất hay:

```text
README không đổi
```

nên Git reuse:

```text
Blob A
```

Git không phải duplicate toàn bộ dữ liệu.

---

# 5. DAG — history thực chất là graph

Commit có:

```text
parent
```

Ví dụ:

```text
C1 ← C2 ← C3
```

Đây đã là history.

Nhưng khi branch:

```text
       C3
      /
C1 ← C2
      \
       C4
```

Và khi merge:

```text
       C3 ─────┐
      /         \
C1 ← C2         C5
      \         /
       C4 ─────┘
```

C5 có hai parent:

```text
parent = C3
parent = C4
```

Vậy Git history không phải:

```text
list
```

mà là:

```text
DAG
Directed Acyclic Graph
```

## Directed

Commit trỏ về parent:

```text
new → old
```

## Acyclic

Không thể:

```text
C1 → C2 → C3 → C1
```

vì commit mới chứa hash của parent đã tồn tại.

Đây là nền tảng của:

```text
branch
merge
rebase
log
bisect
```

---

# 6. Branch thực chất chỉ là pointer

Đây là chỗ nhiều người hiểu sai nhất.

Nhiều người tưởng branch là:

```text
một copy của source code
```

Không phải.

Branch chỉ là một file nhỏ chứa commit hash.

Ví dụ:

```text
main → C5
```

Thực tế gần giống:

```text
.git/refs/heads/main

abc123...
```

Nếu commit mới C6:

```text
C5 ← C6
```

thì branch chỉ move:

```text
main
 ↓
C6
```

Tức là:

```text
before:

main → C5

after commit:

C5 ← C6
      ↑
     main
```

Đó là lý do branch trong Git rất nhẹ.

## First Principle

Commit hash như:

```text
a8f913bc...
```

khó nhớ.

Con người muốn tên:

```text
main
develop
feature/login
```

→ **Ref ra đời.**

Branch đơn giản là:

```text
mutable ref
```

Còn tag thường là:

```text
stable ref
```

---

# 7. HEAD — Git cần biết bạn đang đứng ở đâu

Giả sử có:

```text
main → C5
feature → C8
```

Git phải biết:

> Hiện tại tôi đang làm trên branch nào?

Git dùng:

```text
HEAD
```

Thông thường:

```text
HEAD → main → C5
```

Nghĩa là:

```text
HEAD
 ↓
main
 ↓
C5
```

Khi:

```bash
git switch feature
```

thì:

```text
HEAD → feature → C8
```

Một trường hợp đặc biệt:

```text
HEAD → C5
```

không đi qua branch.

Đây là:

```text
detached HEAD
```

Vậy về bản chất:

```text
Branch = commit nào branch đang trỏ tới?

HEAD = tôi đang dùng branch/ref/commit nào?
```

---

# 8. Working Tree + Index

Đây là phần khiến Git khác nhiều VCS đơn giản.

Git có thực chất **3 trạng thái**:

```text
HEAD
Index
Working Tree
```

Ví dụ:

```text
HEAD
│
│ snapshot đã commit
│
▼

Index
│
│ snapshot chuẩn bị commit
│
▼

Working Tree
│
│ file bạn đang sửa
▼
```

## 8.1 Working Tree

Là filesystem thực tế bạn nhìn thấy:

```text
src/
README.md
pom.xml
```

Bạn sửa file ở đây.

Ví dụ:

```text
Main.java

v1 → v2
```

Git chưa coi nó là commit.

---

## 8.2 Index

Khi chạy:

```bash
git add Main.java
```

Git không đơn giản ghi:

```text
"Main.java staged"
```

Về bản chất nó:

```text
1. hash content
2. tạo blob
3. cập nhật index
```

Index là:

> Snapshot đang được chuẩn bị cho commit tiếp theo.

Ví dụ:

```text
HEAD:
A
B
C

Index:
A
B'
C

Working Tree:
A
B''
C
```

Ở đây:

```text
HEAD       = B
Index      = B'
Working    = B''
```

Đây chính là lý do một file có thể đồng thời xuất hiện ở:

```bash
Changes to be committed
```

và:

```bash
Changes not staged for commit
```

Ví dụ:

```bash
edit file
git add file
edit file again
```

Lúc đó:

```text
HEAD     = version 1
Index    = version 2
Working  = version 3
```

---

# 9. Ghép tất cả lại

Giờ toàn bộ Git có thể nhìn như:

```text
                        HEAD
                         │
                         ▼
                       main
                         │
                         ▼
Commit C3 ──────────── parent
│                        │
│ tree                   ▼
▼                       C2
Tree T3
│
├── README.md ──────► Blob A
│
├── pom.xml ────────► Blob B
│
└── src ────────────► Tree T4
                         │
                         └── Main.java
                                │
                                ▼
                              Blob C
```

Và bên ngoài object database:

```text
Working Tree
     │
 git add
     ▼
   Index
     │
 git commit
     ▼
   Commit
```

Toàn bộ mental model:

```text
                    Object Database
              ┌───────────────────────┐
              │                       │
              │ Blob                  │
              │ Tree                  │
              │ Commit                │
              │                       │
              └───────────────────────┘
                         ▲
                         │
                       hash


Working Tree ──git add──> Index ──git commit──> Commit
                                                 │
                                                 ▼
                                                DAG
                                                 ▲
                                                 │
                                          Branch / Ref
                                                 ▲
                                                 │
                                                HEAD
```

---

# 10. Nếu ép Git về 5 nguyên lý nhỏ nhất

Có thể nhớ Git bằng 5 câu hỏi:

```text
1. Blob
   = WHAT
   Nội dung là gì?

2. Tree
   = WHERE
   Nội dung nằm ở đâu?

3. Commit
   = STATE
   Project tại một thời điểm là gì?

4. Parent/DAG
   = HISTORY
   State này đến từ state nào?

5. Ref + HEAD + Index + Working Tree
   = NAVIGATION + EDITING
   Tôi đang ở đâu và chuẩn bị thay đổi gì?
```

---

# 11. Mental model quan trọng nhất

Câu quan trọng nhất để hiểu sâu Git:

> **Git không quản lý file theo nghĩa truyền thống. Git quản lý một graph các immutable objects được định danh bằng nội dung, rồi dùng refs để đặt tên cho các điểm trong graph đó.**

Một khi hiểu câu này, các thao tác như:

```text
branch
merge
rebase
reset
checkout
switch
cherry-pick
```

gần như đều có thể suy ra từ nguyên lý gốc.
