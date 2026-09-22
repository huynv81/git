Nếu phân tích Git theo First Principles, đừng bắt đầu từ git add, git commit, git push. Hãy bắt đầu từ câu hỏi:

Git thực sự phải giải quyết bài toán gì?

Git cần giải quyết 4 việc cốt lõi: lưu nội dung, lưu trạng thái dự án, lưu lịch sử, và cho nhiều nhánh lịch sử cùng tồn tại.

Từ đó, Git có thể phân rã thành 7 thành phần nền tảng:

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

Điểm quan trọng nhất là: Git về bản chất không phải hệ thống lưu diff. Git là content-addressable object database lưu snapshot.

1. Blob — Git cần lưu nội dung

Giả sử có file:

hello.txt

Hello Git

Câu hỏi đầu tiên:

Git phải lưu "Hello Git" như thế nào?

Git tạo một object loại blob.

"Hello Git"
     │
     ▼
   SHA hash
     │
     ▼
Blob object

Ví dụ về mặt ý tưởng:

blob abc123
content = "Hello Git"

Blob không biết:

filename = hello.txt
folder = src/
owner = Huy
created_at = ...

Nó chỉ biết:

bytes

Đây là một thiết kế cực kỳ quan trọng.

Nếu hai file có cùng content:

a.txt → "hello"
b.txt → "hello"

Git có thể chỉ cần một blob:

            ┌── a.txt
blob X ─────┤
            └── b.txt

Vì object được nhận dạng bằng content.

First Principle

Ta cần lưu:

CONTENT

→ Blob ra đời.

2. Tree — nhưng Blob không biết filename

Nếu chỉ có Blob thì Git gặp vấn đề:

blob abc123 = "Hello Git"

Nhưng:

Blob này tên file là gì?

Git cần một object khác để biểu diễn:

directory structure

Đó là Tree.

Ví dụ project:

project/
├── README.md
└── src/
    └── Main.java

Git biểu diễn gần như:

Tree ROOT
│
├── README.md → Blob A
│
└── src       → Tree B
                    │
                    └── Main.java → Blob C

Tree không chứa nội dung file trực tiếp.

Nó chứa mapping:

name → object

Ví dụ:

100644 blob a123 README.md
040000 tree b456 src

Vậy:

Blob = content

Tree = filesystem structure
First Principle

Ta đã lưu được content.

Nhưng cần biết:

content nào
ở file nào
trong folder nào

→ Tree ra đời.

3. Commit — cần lưu một phiên bản của project

Blob + Tree cho ta một snapshot filesystem.

Nhưng vẫn thiếu:

Snapshot này là phiên bản nào?

Ai tạo?

Khi nào?

Snapshot trước đó là gì?

Git tạo object thứ ba:

Commit

Commit chứa đại khái:

commit C3
│
├── tree    → T3
├── parent  → C2
├── author
├── committer
└── message

Quan trọng nhất:

commit
   │
   ▼
 tree
   │
   ├── file A
   ├── file B
   └── directory

Commit về bản chất là:

Một object trỏ đến root tree của toàn bộ project.

Ví dụ:

Commit C1
   │
   ▼
Tree T1
├── README → Blob A
└── Main   → Blob B

Sau khi sửa Main:

Commit C2
   │
   ▼
Tree T2
├── README → Blob A
└── Main   → Blob C

Bạn thấy một điều rất hay:

README không đổi

nên Git reuse:

Blob A

Git không phải duplicate toàn bộ dữ liệu.

4. DAG — history thực chất là graph

Commit có:

parent

Ví dụ:

C1 ← C2 ← C3

Đây đã là history.

Nhưng khi branch:

       C3
      /
C1 ← C2
      \
       C4

Và khi merge:

       C3 ─────┐
      /         \
C1 ← C2         C5
      \         /
       C4 ─────┘

C5 có hai parent:

parent = C3
parent = C4

Vậy Git history không phải:

list

mà là:

DAG
Directed Acyclic Graph
Directed

Commit trỏ về parent:

new → old
Acyclic

Không thể:

C1 → C2 → C3 → C1

vì commit mới chứa hash của parent đã tồn tại.

Đây là nền tảng của:

branch
merge
rebase
log
bisect
5. Branch thực chất chỉ là pointer

Đây là chỗ nhiều người hiểu sai nhất.

Nhiều người tưởng branch là:

một copy của source code

Không phải.

Branch chỉ là một file nhỏ chứa commit hash.

Ví dụ:

main → C5

Thực tế gần giống:

.git/refs/heads/main

abc123...

Nếu commit mới C6:

C5 ← C6

thì branch chỉ move:

main
 ↓
C6

Tức là:

before:

main → C5

after commit:

C5 ← C6
      ↑
     main

Đó là lý do branch trong Git rất nhẹ.

First Principle

Commit hash như:

a8f913bc...

khó nhớ.

Con người muốn tên:

main
develop
feature/login

→ Ref ra đời.

Branch đơn giản là:

mutable ref

Còn tag thường là:

stable ref
6. HEAD — Git cần biết bạn đang đứng ở đâu

Giả sử có:

main → C5
feature → C8

Git phải biết:

Hiện tại tôi đang làm trên branch nào?

Git dùng:

HEAD

Thông thường:

HEAD → main → C5

Nghĩa là:

HEAD
 ↓
main
 ↓
C5

Khi:

git switch feature

thì:

HEAD → feature → C8

Một trường hợp đặc biệt:

HEAD → C5

không đi qua branch.

Đây là:

detached HEAD

Vậy về bản chất:

Branch = commit nào branch đang trỏ tới?

HEAD = tôi đang dùng branch/ref/commit nào?
7. Working Tree + Index

Đây là phần khiến Git khác nhiều VCS đơn giản.

Git có thực chất 3 trạng thái:

HEAD
Index
Working Tree

Ví dụ:

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
Working Tree

Là filesystem thực tế bạn nhìn thấy:

src/
README.md
pom.xml

Bạn sửa file ở đây.

Ví dụ:

Main.java

v1 → v2

Git chưa coi nó là commit.

Index

Khi chạy:

git add Main.java

Git không đơn giản ghi:

"Main.java staged"

Về bản chất nó:

1. hash content
2. tạo blob
3. cập nhật index

Index là:

Snapshot đang được chuẩn bị cho commit tiếp theo.

Ví dụ:

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

Ở đây:

HEAD       = B
Index      = B'
Working    = B''

Đây chính là lý do một file có thể đồng thời xuất hiện ở:

Changes to be committed

và:

Changes not staged for commit

Ví dụ:

edit file
git add file
edit file again

Lúc đó:

HEAD     = version 1
Index    = version 2
Working  = version 3
Ghép tất cả lại

Giờ toàn bộ Git có thể nhìn như:

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

Và bên ngoài object database:

Working Tree
     │
 git add
     ▼
   Index
     │
 git commit
     ▼
   Commit

Toàn bộ mental model:

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

Nếu ép Git về 5 nguyên lý nhỏ nhất, tôi sẽ nhớ thế này:

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

Và câu quan trọng nhất để hiểu sâu Git là:

Git không quản lý file theo nghĩa truyền thống. Git quản lý một graph các immutable objects được định danh bằng nội dung, rồi dùng refs để đặt tên cho các điểm trong graph đó.

Một khi hiểu câu này, branch, merge, rebase, reset, checkout, cherry-pick gần như đều có thể suy ra từ nguyên lý gốc.
