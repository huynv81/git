# Hiểu bản chất Git bằng First Principles

> Đừng học Git như một danh sách lệnh. Hãy học bốn sự thật nền tảng, rồi tự suy ra các lệnh từ chúng.

## 0. Câu hỏi gốc

Ta cần giải quyết bài toán:

> Làm sao lưu nhiều trạng thái của một dự án, biết trạng thái nào sinh ra từ trạng thái nào, và cho nhiều người cùng phát triển mà không làm mất công việc?

Git trả lời bằng bốn primitive:

```text
Object = dữ liệu bất biến
Commit = một snapshot có liên kết tới quá khứ
Ref    = một cái tên trỏ tới commit
Index  = snapshot dự kiến cho commit tiếp theo
```

Toàn bộ Git gần như được suy ra từ bốn thứ này.

---

## 1. Dữ liệu đã lưu là bất biến

Giả sử ta có nội dung:

```text
hello
```

Git đưa nội dung qua hàm hash để tạo ID:

```text
HASH("hello") → abc123
```

Có thể tưởng tượng Git lưu:

```text
abc123 → "hello"
```

Nếu nội dung đổi thành `hello world`, ID cũng đổi. Git không sửa object `abc123`; nó tạo object mới.

Từ đó suy ra:

1. Object cũ vẫn tồn tại sau khi tạo object mới.
2. Hai file có cùng nội dung có thể dùng chung một object.
3. Biết ID thì có thể kiểm tra nội dung có bị thay đổi không.
4. Tên file không cần nằm trong object chứa nội dung.

Git gọi object chứa bytes của file là **blob**:

```text
blob abc123 = "hello"
```

Tên file nằm trong **tree**:

```text
tree root
├── README.md → blob b1
└── src       → tree t2
    └── app.js → blob b2
```

Tree gốc biểu diễn snapshot của toàn bộ dự án.

### Suy luận quan trọng

Git không lưu “file đã thay đổi như thế nào” làm mô hình nền tảng. Git lưu trạng thái nội dung. Diff chỉ là kết quả so sánh hai trạng thái:

```text
diff = snapshot B - snapshot A
```

---

## 2. Lịch sử là các snapshot nối với nhau

Một snapshot riêng lẻ chưa phải lịch sử. Ta cần biết nó sinh ra từ đâu.

Git tạo một **commit**:

```text
commit C
├── snapshot → tree T
├── parent   → commit B
├── author/time
└── message
```

Ba commit liên tiếp tạo thành:

```text
A ← B ← C
```

Mũi tên nghĩa là commit bên phải chứa ID của parent bên trái.

ID commit được tính từ snapshot, parent, metadata và message. Đổi một phần là tạo ID mới. Vì vậy:

> Không có thao tác sửa commit cũ tại chỗ. `amend` và `rebase` tạo commit mới.

### Branching tự xuất hiện

Hai người cùng bắt đầu từ `B`:

```text
      C
     /
A ← B
     \
      D
```

Không cần một cấu trúc “branch” đặc biệt trong commit. Chỉ có hai commit cùng parent.

### Merge tự xuất hiện

Muốn lưu trạng thái kết hợp `C` và `D`, tạo commit có hai parent:

```text
      C ──┐
     /     ↓
A ← B      M
     \     ↑
      D ──┘
```

Lịch sử Git vì vậy là đồ thị:

```text
node = commit
edge = quan hệ parent
```

---

## 3. Branch chỉ là một con trỏ

Con người không muốn nhớ commit ID. Git cho ta đặt tên:

```text
main → C
```

`main` là một **ref**. Branch là ref được phép di chuyển.

Khi commit `D`:

```text
trước: A ← B ← C        main → C
sau:  A ← B ← C ← D    main → D
```

Commit thực chất gồm ba bước:

1. Tạo snapshot mới.
2. Tạo commit mới có parent là commit hiện tại.
3. Di chuyển branch hiện tại tới commit mới.

Branch không chứa commit. Nó chỉ trỏ tới commit cuối; Git đi theo parent để tìm lịch sử.

### Tạo branch

```bash
git branch feature
```

chỉ tạo thêm một ref:

```text
A ← B ← C
        ↑
   main, feature
```

Không copy thư mục hay lịch sử.

### HEAD

Git cần biết branch nào sẽ di chuyển khi commit:

```text
HEAD → main → C
```

Sau `git switch feature`:

```text
HEAD → feature → C
```

Nếu commit `D`, `feature` tiến lên còn `main` đứng yên:

```text
          D ← feature ← HEAD
         /
A ← B ← C ← main
```

Nếu `HEAD` trỏ thẳng vào commit, ta ở trạng thái detached HEAD. Vẫn commit được, nhưng không branch nào tự giữ commit mới.

### Kết luận

```text
commit = dữ liệu lịch sử
branch = tên trỏ tới dữ liệu
HEAD   = vị trí làm việc hiện tại
```

Một commit không thuộc riêng branch nào.

---

## 4. Index là snapshot dự kiến

Ta có ba trạng thái:

```text
HEAD commit → Index → Working tree
đã lưu        sắp lưu  đang sửa
```

Vì sao cần Index? Vì working tree có thể chứa nhiều thay đổi lẫn lộn, nhưng ta chỉ muốn commit một thay đổi logic.

`git add` có nghĩa chính xác là:

> Đưa phiên bản hiện tại của nội dung được chọn vào snapshot kế tiếp.

```text
git add:    Working tree → Index
git commit: Index → Commit mới
```

### Một file có thể có ba phiên bản

```text
HEAD:         version 1
Index:        version 2
Working tree: version 3
```

Điều này xảy ra khi bạn sửa thành version 2, `git add`, rồi sửa tiếp thành version 3.

```bash
git diff
```

so sánh Working tree với Index: phần chưa stage.

```bash
git diff --staged
```

so sánh Index với `HEAD`: phần sẽ được commit.

`git status` chủ yếu báo kết quả của chính hai phép so sánh đó.

---

## 5. Suy ra các lệnh từ bốn nguyên lý

Với mỗi lệnh, chỉ hỏi:

1. Nó tạo object/commit mới không?
2. Nó di chuyển ref hoặc `HEAD` không?
3. Nó thay đổi Index không?
4. Nó thay đổi Working tree không?

### Commit

```text
Index → tree mới
tree + parent + metadata → commit mới
branch hiện tại → commit mới
```

Working tree không phải nguồn trực tiếp của commit.

### Switch

```text
HEAD → branch khác
Index và Working tree → snapshot branch đó
```

Git thường từ chối nếu thao tác sẽ ghi đè thay đổi chưa lưu.

### Restore

```bash
git restore file.txt
```

```text
Index → Working tree
```

Nó bỏ thay đổi chưa stage của file.

```bash
git restore --staged file.txt
```

```text
HEAD → Index
```

Nó unstage nhưng giữ nội dung trong Working tree.

### Reset

Reset trước hết di chuyển branch hiện tại. Mode quyết định có đồng bộ thêm vùng khác không:

| Lệnh | Branch | Index | Working tree |
|---|---|---|---|
| `reset --soft C` | → C | giữ | giữ |
| `reset --mixed C` | → C | → C | giữ |
| `reset --hard C` | → C | → C | → C |

Suy ra:

- `--soft`: bỏ commit, giữ thay đổi staged.
- `--mixed`: bỏ commit, giữ nội dung nhưng unstage.
- `--hard`: đưa cả ba trạng thái về commit đích.

`--hard` nguy hiểm vì ghi đè Working tree.

### Revert

`git revert C` không xóa `C`. Nó tạo commit mới đảo hiệu ứng của `C`:

```text
A ← B ← C ← D ← R
```

Do giữ lịch sử cũ, revert phù hợp với branch đã chia sẻ.

---

## 6. Merge là phép so sánh ba chiều

Hai branch cùng đi ra từ `B`:

```text
      C ← D   feature
     /
A ← B ← E     main
```

Khi merge feature vào main, Git dùng:

```text
base   = B   trạng thái chung ban đầu
ours   = E   trạng thái branch hiện tại
theirs = D   trạng thái được nhập vào
```

Nó so sánh `B → E` và `B → D`, rồi kết hợp hai tập thay đổi.

Tại sao cần base? Nếu một dòng có trong `E` nhưng không có trong `D`, chỉ nhìn hai đầu thì không biết `E` đã thêm hay `D` đã xóa. Nhìn `B` mới biết.

### Conflict

Conflict nghĩa là Git không đủ thông tin để biết kết quả con người muốn. Máy không hỏng; nó chỉ từ chối đoán ý định.

Bạn sửa file thành kết quả đúng rồi chạy:

```bash
git add <file>
```

Tức là đưa kết quả đã giải quyết vào Index. Sau đó commit hoàn tất merge.

### Fast-forward

Nếu `main` là tổ tiên của `feature`:

```text
A ← B ← C ← D
    main      feature
```

Git chỉ cần di chuyển `main` tới `D`:

```text
A ← B ← C ← D
              ↑
         main, feature
```

Fast-forward chỉ là di chuyển ref.

---

## 7. Rebase là tạo lại commit trên nền mới

Ban đầu:

```text
      D ← E   feature
     /
A ← B ← C     main
```

Ta muốn feature bắt đầu từ `C`. Nhưng commit bất biến; không thể đổi parent của `D` mà vẫn giữ nguyên `D`.

Git phải:

1. lấy thay đổi `B → D`, áp dụng lên `C`, tạo `D'`;
2. lấy thay đổi `D → E`, áp dụng lên `D'`, tạo `E'`.

```text
A ← B ← C ← D' ← E'
         main       feature
```

`D'` và `E'` có ID mới vì parent mới.

> Rebase không di chuyển commit. Nó phát lại thay đổi để tạo commit mới.

Nếu người khác đã dựa trên `D` và `E`, còn bạn thay chúng bằng `D'` và `E'`, hai bên giờ có các commit khác nhau. Vì thế không tùy tiện rebase lịch sử đã chia sẻ.

- Merge giữ topology thật và thêm điểm hội tụ.
- Rebase tạo lại lịch sử cho tuyến tính hơn.

---

## 8. Remote chỉ là repository khác

Repository trên server cũng chỉ có object, commit graph và refs.

```text
main         = branch local
origin/main  = ghi nhớ local về main trên origin
remote main  = branch thật trên server
```

`origin/main` không cập nhật theo thời gian thực.

### Fetch

```text
tải object còn thiếu
+ cập nhật origin/main, origin/feature...
```

Thông thường fetch không đổi local branch, Index hay Working tree.

### Pull

Pull thường là:

```text
fetch + merge
```

hoặc `fetch + rebase`. Muốn dễ hiểu, hãy fetch, nhìn graph, rồi tự chọn.

### Push

Push:

1. gửi object còn thiếu;
2. đề nghị remote di chuyển ref.

Remote từ chối non-fast-forward vì thao tác có thể làm lịch sử hiện tại của remote không còn reachable từ ref mới.

---

## 9. Vì sao thường cứu được commit “đã mất”?

Ban đầu:

```text
A ← B ← C ← main
```

Sau `git reset --hard A`:

```text
A ← main

B ← C   không còn được main trỏ tới
```

Reset di chuyển ref; nó không lập tức xóa object `B`, `C`.

Reflog ghi lại ref từng trỏ tới đâu:

```bash
git reflog
git branch rescue <commit-id>
```

Nhưng reflog là local và có thể hết hạn. Nội dung chưa từng `add`, commit hoặc stash có thể chưa trở thành object để Git cứu.

---

## 10. Một ví dụ xuyên suốt

Ban đầu:

```text
A ← B
    ↑
   main ← HEAD
```

Tạo branch:

```bash
git switch -c feature
```

```text
A ← B
    ↑
   main, feature ← HEAD
```

Sửa `app.js`: chỉ Working tree đổi.

```bash
git add app.js
```

Nội dung được đưa từ Working tree vào Index.

```bash
git commit -m "Add feature"
```

Git tạo `C` từ Index rồi di chuyển feature:

```text
A ← B ← C
    ↑   ↑
   main feature ← HEAD
```

Sau đó main có commit `D`:

```text
      C   feature
     /
A ← B ← D   main
```

Merge tạo kết quả từ base `B`, ours `D`, theirs `C`:

```text
      C ──┐
     /     ↓
A ← B ← D ← M   main
```

Hoặc rebase feature lên main tạo `C'`:

```text
A ← B ← D ← C'
        ↑    ↑
       main feature
```

Mọi bước chỉ dùng bốn primitive: Object, Commit, Ref và Index.

---

## 11. Công thức xử lý mọi tình huống Git

Khi Git khó hiểu, đừng thử lệnh ngẫu nhiên. Vẽ:

```text
Commit graph: A ← B ← C
Refs:         main → C, feature → B
Index:        snapshot nào đang staged?
Working tree: khác Index ở đâu?
```

Rồi hỏi:

1. `HEAD` đang ở đâu?
2. Branch nào trỏ tới commit nào?
3. Lệnh sắp chạy di chuyển ref nào?
4. Nó có ghi đè Index không?
5. Nó có ghi đè Working tree không?
6. Nó tạo commit mới hay dùng commit cũ?

Sáu câu này giải được phần lớn vấn đề Git thường gặp.

---

## 12. Lab 15 phút

Chạy trong thư mục thử nghiệm:

```bash
mkdir git-first-principles-lab
cd git-first-principles-lab
git init
```

### Nhìn blob, tree và commit

```bash
printf 'one\n' > note.txt
git add note.txt
git ls-files --stage
git commit -m 'one'
git cat-file -p HEAD
git cat-file -p HEAD^{tree}
```

Bạn sẽ thấy Index trỏ tới blob, commit trỏ tới tree, tree gắn tên file với blob.

### Tạo ba phiên bản cùng lúc

```bash
printf 'two\n' > note.txt
git add note.txt
printf 'three\n' > note.txt
git diff --staged
git diff
```

Lúc này:

```text
HEAD         = one
Index        = two
Working tree = three
```

### Nhìn branch di chuyển

```bash
git commit -m 'two'
git branch feature
git switch feature
printf 'feature\n' > feature.txt
git add feature.txt
git commit -m 'feature'
git log --oneline --graph --decorate --all
```

Chỉ `feature` tiến lên.

### Cứu commit sau reset

```bash
git reset --hard HEAD~1
git reflog -5
git branch rescued <commit-id-trong-reflog>
```

Bạn vừa trực tiếp kiểm chứng Object, Commit, Ref, Index và reflog.

---

## 13. Bản đồ trí nhớ cuối cùng

```text
NỘI DUNG
  │
  ├─ blob: bytes của file
  └─ tree: tên file/thư mục → object
             │
             ▼
SNAPSHOT ── commit ── parent ── commit ── ...
             ▲
             │
REFS      main, feature, tag
             ▲
             │
HEAD      vị trí làm việc hiện tại

HEAD snapshot ←→ Index ←→ Working tree
```

Các thao tác chính:

```text
commit = Index → commit mới → di chuyển branch
branch = tạo ref mới
switch = đổi HEAD, đồng bộ Index và Working tree
merge  = kết hợp từ base, có thể tạo commit hai parent
rebase = phát lại thay đổi trên parent mới
reset  = di chuyển ref, có thể đồng bộ Index/Working tree
revert = tạo commit mới đảo hiệu ứng commit cũ
fetch  = lấy object và cập nhật remote-tracking refs
push   = gửi object và đề nghị remote di chuyển ref
```

Nếu chỉ giữ một thói quen, hãy giữ điều này:

> Trước khi chạy lệnh Git mà bạn chưa chắc chắn, hãy vẽ commit graph và ba trạng thái `HEAD–Index–Working tree`. Khi nhìn đúng trạng thái, lệnh cần dùng thường tự lộ ra.
