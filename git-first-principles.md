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

### Cách đọc tài liệu

Tài liệu có hai tầng nhưng đi cùng một đường suy luận:

- Đọc lần đầu, tập trung vào sơ đồ và các câu bắt đầu bằng “suy ra”.
- Đọc lần hai, tự chạy lệnh quan sát để kiểm chứng object, ref và Index thật.

Mỗi khi gặp một lệnh mới, đừng ghi nhớ cú pháp ngay. Hãy dự đoán lệnh đó sẽ tác động lên bốn thứ nào rồi mới kiểm tra kết quả.

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

### Object thật được tạo ra thế nào?

Ở mức khái niệm, Git không hash riêng phần nội dung. Nó tạo dữ liệu dạng:

```text
<type><space><size><null-byte><content>
```

rồi hash toàn bộ dữ liệu đó. Ví dụ cùng chuỗi bytes nhưng khác loại object sẽ không mặc nhiên có cùng ID, vì `type` cũng tham gia phép hash.

Sau khi nén, loose object thường được đặt theo ID:

```text
.git/objects/ab/cdef...
```

Hai ký tự đầu là thư mục, phần còn lại là tên file. Khi repository lớn, Git gom object vào packfile và có thể delta-compress để tiết kiệm dung lượng.

Điểm cần tách bạch:

```text
Mô hình logic:  mỗi commit là một snapshot
Lưu trữ vật lý: object có thể được nén/delta trong packfile
```

Ta suy luận hành vi Git bằng mô hình snapshot; packfile chỉ là tối ưu hóa bên dưới.

### Vì sao sửa một file không nhân đôi cả repository?

Giả sử snapshot đầu là:

```text
root T1
├── README.md → blob R
└── src       → tree S1
    ├── a.js  → blob A
    └── b.js  → blob B1
```

Chỉ sửa `b.js`. Snapshot mới có thể tái sử dụng `R` và `A`:

```text
root T2
├── README.md → blob R       # dùng lại
└── src       → tree S2
    ├── a.js  → blob A       # dùng lại
    └── b.js  → blob B2      # mới
```

Git tạo blob `B2`, tree `S2` và root tree `T2`; object không đổi được dùng lại. Vì vậy “snapshot hoàn chỉnh” không đồng nghĩa “copy toàn bộ bytes”.

### Vì sao Git không thật sự lưu rename?

Blob không biết tên file. Tree cũ có:

```text
old.txt → blob X
```

Tree mới có:

```text
new.txt → blob X
```

Trong dữ liệu chỉ có một path biến mất và một path xuất hiện. Khi hiển thị diff, Git suy luận rename dựa trên độ tương đồng. Vì thế ngưỡng phát hiện rename có thể làm cùng thay đổi được trình bày khác nhau, dù snapshot không đổi.

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

### Identity và equality

Hai commit có tree giống hệt nhau vẫn có thể là hai commit khác nhau:

```text
commit X: tree T, parent A, message "save"
commit Y: tree T, parent B, message "save"
```

Snapshot bằng nhau nhưng parent khác nên ID khác. Điều này cho thấy:

```text
trạng thái dự án ≠ danh tính lịch sử
```

Commit không chỉ nói “nội dung là gì”, mà còn nói “nội dung này nằm ở đâu trong lịch sử”.

### Reachability: một định luật quan trọng

Nếu một ref trỏ tới `C`:

```text
main → C → B → A
```

thì `C`, `B`, `A` đều reachable từ `main`. Khi hỏi “commit nào thuộc branch main?”, câu trả lời thực tế thường là “commit nào reachable từ ref `main`?”.

Từ đó suy ra:

- Một commit có thể reachable từ nhiều branch.
- Xóa branch chỉ xóa một điểm bắt đầu duyệt graph.
- Commit chưa bị xóa chỉ vì không còn hiện trong `git log main`.
- Garbage collection chỉ có thể thu gom object không còn được giữ bởi ref/reflog hay cơ chế bảo vệ khác.

### Thời gian không quyết định topology

Timestamp của commit có thể sai hoặc bị chỉnh. Parent mới quyết định quan hệ tổ tiên. Git trả lời “A có trước B trong graph không?” bằng đường parent, không bằng cách đơn thuần so timestamp.

```bash
git merge-base --is-ancestor A B
```

Lệnh trên kiểm tra có đường đi từ `B` ngược theo parent tới `A` hay không.

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

### Ref thật nằm ở đâu?

Local branch thường có tên đầy đủ:

```text
refs/heads/main
refs/heads/feature
```

Remote-tracking ref thường là:

```text
refs/remotes/origin/main
```

Tag thường là:

```text
refs/tags/v1.0.0
```

`main`, `origin/main` và `v1.0.0` là tên rút gọn Git phân giải thành ref đầy đủ. Ref có thể nằm trong file riêng hoặc được đóng gói; vì vậy nên quan sát bằng lệnh Git thay vì phụ thuộc cấu trúc file:

```bash
git show-ref
git rev-parse main
git symbolic-ref HEAD
```

### Branch name không nằm trong commit

Commit object không chứa dòng “branch: main”. Điều này giải thích đồng thời:

- đổi tên branch không đổi commit ID;
- xóa branch không sửa commit;
- push cùng commit dưới tên branch khác không tạo lại commit;
- không thể hỏi một commit “branch gốc của mày là gì?” một cách tuyệt đối.

### Tag khác branch ở đâu?

Về cốt lõi cả hai đều giúp tìm object. Khác biệt chính là ý định:

- branch được kỳ vọng di chuyển khi có commit mới;
- tag được kỳ vọng giữ nguyên để đánh dấu một mốc.

Lightweight tag gần như một ref trực tiếp. Annotated tag tạo thêm tag object chứa message, tagger và có thể có chữ ký.

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

### Index không phải một “hộp chứa thay đổi”

Cách nói “đưa thay đổi vào staging area” tiện nhưng dễ gây hiểu sai. Index không chủ yếu lưu một danh sách patch. Nó giữ entry theo path, gồm object ID, mode và metadata hỗ trợ, đủ để Git viết ra tree của commit kế tiếp.

Ví dụ:

```text
100644 blob-A README.md
100644 blob-B src/app.js
```

Do đó sau `git add src/app.js`, Git đã biết chính xác blob nào sẽ xuất hiện tại path đó trong snapshot tiếp theo.

Quan sát trực tiếp:

```bash
git ls-files --stage
git write-tree
```

`git write-tree` tạo tree từ trạng thái Index và trả về tree ID. Nó làm lộ bước mà `git commit` thường thực hiện giúp bạn.

### Untracked, tracked và ignored được suy ra thế nào?

- **tracked**: path có mặt trong Index.
- **untracked**: path có trong Working tree nhưng không có trong Index.
- **ignored**: untracked path khớp quy tắc ignore, nên Git thường không đề nghị add.

`.gitignore` không tác động lên file đã tracked, vì file đó đã có entry trong Index. Muốn ngừng track nhưng giữ file trên ổ đĩa, phải thay đổi Index, ví dụ với `git rm --cached`.

### Index khi conflict

Bình thường mỗi path có một entry stage 0. Trong merge conflict, Index có thể giữ ba phiên bản:

```text
stage 1 = merge base
stage 2 = ours
stage 3 = theirs
```

```bash
git ls-files -u
```

Khi bạn sửa xong và chạy `git add`, Git thay ba entry conflict bằng một entry stage 0: kết quả bạn đã chọn. Đây là nghĩa chính xác của “mark conflict as resolved”.

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

### Tự dựng một commit mà không dùng `git add` và `git commit`

Đây là cách ngắn nhất để nhìn xuyên lớp giao diện của Git. Giả sử repository đã được `git init` và đã cấu hình identity.

Tạo blob từ stdin:

```bash
blob=$(printf 'hello\n' | git hash-object -w --stdin)
```

Tạo một tree bằng plumbing command:

```bash
tree=$(printf '100644 blob %s\thello.txt\n' "$blob" | git mktree)
```

Tạo commit không có parent:

```bash
commit=$(printf 'first commit\n' | git commit-tree "$tree")
```

Tạo/cập nhật branch bằng một phép cập nhật ref có kiểm tra:

```bash
git update-ref refs/heads/main "$commit"
git symbolic-ref HEAD refs/heads/main
git reset --hard main
```

Chuỗi thao tác thật là:

```text
bytes
  ↓ hash-object
blob
  ↓ mktree
tree
  ↓ commit-tree
commit
  ↓ update-ref
branch
  ↓ reset/checkout
Index + Working tree
```

`git add` và `git commit` chỉ tự động hóa chuỗi này cùng nhiều kiểm tra an toàn, hook và metadata.

### Cập nhật ref là compare-and-swap

Khi nhiều tiến trình có thể cùng cập nhật branch, việc “ghi ID mới” đơn thuần có thể làm mất cập nhật. `update-ref` có thể nhận cả giá trị cũ dự kiến:

```text
update main từ OLD sang NEW, nhưng chỉ nếu main vẫn là OLD
```

Nếu ref đã đổi, thao tác thất bại thay vì ghi đè mù quáng. Đây cùng ý tưởng nền tảng với kiểm tra non-fast-forward và `--force-with-lease`: cập nhật chỉ hợp lệ khi trạng thái hiện tại đúng như người gọi kỳ vọng.

### Amend được suy ra chính xác

Giả sử:

```text
A ← B ← C ← main
```

`git commit --amend` lấy parent của `C` là `B`, tạo commit mới `C'` từ Index/message/metadata hiện tại, rồi đổi `main`:

```text
        C    # cũ, không còn được main trỏ tới
       /
A ← B ← C' ← main
```

Không có commit nào bị chỉnh tại chỗ.

### Revert

`git revert C` không xóa `C`. Nó tạo commit mới đảo hiệu ứng của `C`:

```text
A ← B ← C ← D ← R
```

Do giữ lịch sử cũ, revert phù hợp với branch đã chia sẻ.

### Cherry-pick

Commit `X` không phải một patch được lưu sẵn, nhưng Git có thể tính patch của nó:

```text
patch(X) = tree(X) - tree(parent(X))
```

Cherry-pick áp dụng hiệu ứng đó lên `HEAD` và tạo commit mới:

```text
X ở nhánh khác  →  tính effect  →  áp dụng lên HEAD  →  X'
```

`X'` thường có tree effect tương tự `X`, nhưng parent mới nên ID khác. Điều này cũng giải thích vì sao cherry-pick có thể conflict: effect được tạo trong một context nhưng đang được áp dụng vào context khác.

### Stash không phải chiếc túi ngoài lịch sử

Stash dùng commit/tree để lưu trạng thái Working tree và Index, rồi làm sạch môi trường làm việc. Nó không phải vùng lưu trữ thứ tư độc lập với object database. Suy ra stash cũng có reflog, có thể hết hạn và đôi khi có thể được cứu như commit khác.

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

### Merge-base được tìm theo graph, không theo ngày

Merge-base là tổ tiên chung “tốt nhất”: một tổ tiên chung không phải tổ tiên của một tổ tiên chung tốt hơn khác. Với graph đơn giản chỉ có một base. Graph phức tạp có thể có nhiều merge-base; chiến lược merge hiện đại có thể tạo một base tổng hợp để thực hiện merge ba chiều.

```bash
git merge-base main feature
```

Vì thuật toán dựa vào quan hệ parent, commit có timestamp mới hơn không nhất thiết “đi sau” commit khác trong topology.

### Merge theo từng path được suy luận ra sao?

Với mỗi path, hãy gọi nội dung ở base/ours/theirs là `B/O/T`:

| Quan hệ | Kết luận hợp lý |
|---|---|
| `O = B`, `T ≠ B` | lấy `T`: chỉ theirs đổi |
| `T = B`, `O ≠ B` | lấy `O`: chỉ ours đổi |
| `O = T` | lấy nội dung chung |
| cả `O` và `T` khác `B` | cần merge nội dung hoặc báo conflict |

Ví dụ delete/modify:

```text
base:   file tồn tại
ours:   file bị xóa
theirs: file được sửa
```

Git không thể biết ý định là “xóa bất kể sửa đổi” hay “giữ phiên bản đã sửa”, nên báo conflict.

Ví dụ add/add:

```text
base:   không có file
ours:   thêm file với nội dung X
theirs: thêm cùng path với nội dung Y
```

Nếu `X ≠ Y`, Git cần con người chọn kết quả.

### Conflict marker chỉ là biểu diễn ở Working tree

Các marker:

```text
<<<<<<< HEAD
ours
=======
theirs
>>>>>>> feature
```

không phải nguồn dữ liệu duy nhất của conflict. Ba phiên bản chuẩn vẫn nằm trong Index stages. Vì vậy merge tool có thể đọc base/ours/theirs kể cả khi cách trình bày Working tree thay đổi.

### Merge commit ghi lại điều gì?

Merge commit không lưu riêng một danh sách “quyết định conflict”. Nó lưu:

- tree kết quả;
- hai hoặc nhiều parent;
- metadata/message.

Các quyết định được thể hiện gián tiếp trong snapshot kết quả. Muốn xem merge đã đưa vào gì, phải so snapshot của merge commit với các parent theo mục đích cụ thể.

### Vì sao revert một merge cần `-m`?

Commit thường có một parent nên effect có hướng rõ ràng. Merge commit có nhiều parent. Khi revert, Git cần biết parent nào là mainline — lịch sử chính cần giữ — để tính phần cần đảo:

```bash
git revert -m 1 <merge-commit>
```

`-m 1` không có nghĩa “revert parent 1”; nó chọn parent 1 làm mainline rồi đảo effect của merge so với parent đó.

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

### Rebase thực hiện bằng một sequencer

Về mặt khái niệm:

1. tìm merge-base của upstream và branch;
2. liệt kê các commit riêng cần phát lại theo thứ tự;
3. đưa `HEAD` tới nền mới;
4. cherry-pick lần lượt từng commit;
5. cập nhật branch tới chuỗi mới.

Nếu dừng ở conflict, Git cần nhớ:

- đang phát lại commit nào;
- các commit nào còn lại;
- nền và branch ban đầu;
- kết quả Index hiện tại.

Do đó:

```bash
git rebase --continue
```

không “thử lại toàn bộ”; nó tiếp tục state machine sau khi bạn đã đưa kết quả conflict vào Index.

```bash
git rebase --abort
```

đưa branch/Working tree trở về trạng thái trước khi rebase dựa trên metadata Git đã lưu cho operation.

### Vì sao cùng conflict có thể lặp lại?

Rebase phát lại từng commit, không áp dụng một patch tổng duy nhất. Nếu nhiều commit trong chuỗi lần lượt đụng cùng vùng đã đổi trên nền mới, bạn có thể phải giải conflict nhiều lần. `rerere` có thể ghi nhớ một cách giải conflict và tái sử dụng khi nhận ra cùng hình dạng conflict.

### Interactive rebase chỉ là sửa chương trình phát lại

Danh sách:

```text
pick A
pick B
pick C
```

là một chương trình nhỏ. Đổi thành `reword`, `edit`, `squash`, `fixup`, `drop` nghĩa là thay cách tạo chuỗi commit mới. Commit cũ vẫn không bị sửa; ref cuối cùng chỉ chuyển sang chuỗi mới.

### Patch-equivalence không phải commit identity

Hai commit khác ID có thể tạo ra cùng effect so với parent của chúng. Rebase có thể nhận ra một thay đổi tương đương đã có ở upstream và bỏ qua nó. Đây là khác biệt giữa:

```text
identity: commit ID có giống nhau không?
effect:   thay đổi do commit tạo ra có tương đương không?
```

Hiểu khác biệt này giúp giải thích vì sao log sau rebase không nhất thiết có số commit đúng như trước dù nội dung cuối vẫn đúng.

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

### Fast-forward là một điều kiện graph

Remote ref đang ở `OLD`, bạn muốn cập nhật tới `NEW`. Đây là fast-forward khi:

```text
OLD là tổ tiên của NEW
```

Khi đó mọi commit trước đây reachable từ `OLD` vẫn reachable từ `NEW`. Nếu không, cập nhật có thể làm một phần lịch sử biến khỏi branch.

### Push refspec thực chất nói gì?

Một refspec dạng:

```text
refs/heads/main:refs/heads/main
```

có nghĩa:

```text
lấy ref local bên trái
→ yêu cầu cập nhật ref remote bên phải
```

Object transfer chỉ gửi những object remote chưa có nhưng cần để làm đích mới reachable. Vì object định danh theo nội dung, hai bên có thể thương lượng tập thiếu thay vì gửi lại toàn bộ repository.

### Tracking configuration không nhập hai branch làm một

Khi local `main` track `origin/main`, Git chỉ lưu quan hệ cấu hình để biết upstream mặc định cho status, pull và push. Hai ref vẫn độc lập:

```text
main         thay đổi khi bạn commit/reset/rebase
origin/main  thay đổi khi fetch nhận trạng thái remote
```

Đó là lý do `git status` có thể nói “ahead 2, behind 1”: graph cho thấy hai commit chỉ reachable từ local và một commit chỉ reachable từ remote-tracking ref.

### Force-with-lease được suy ra từ compare-and-swap

Giả sử bạn tin remote đang ở `R1`, muốn thay bằng lịch sử `R2`. `--force` nói “cứ ghi `R2`”. `--force-with-lease` gần với:

```text
chỉ đổi remote từ R1 sang R2 nếu nó vẫn đang là R1
```

Nếu người khác vừa push thành `R3`, lease thất bại, tránh ghi đè công việc bạn chưa biết.

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

### Vòng đời của dữ liệu

Phân biệt bốn mức an toàn:

```text
1. chỉ ở Working tree       → Git có thể chưa biết nội dung
2. đã add                   → blob thường đã tồn tại, nhưng khó tìm nếu không có ref
3. đã commit                → commit/tree/blob tồn tại
4. có ref/reflog giữ         → dễ tìm và chưa đủ điều kiện thu gom
```

Một blob dangling đôi khi có thể tìm bằng `git fsck`, nhưng không còn tên path hay ngữ cảnh đầy đủ như commit. Đừng coi đó là chiến lược backup.

### Reflog ghi chuyển động của tên, không ghi mọi nội dung

Reflog có entry kiểu:

```text
OLD → NEW: reset: moving to HEAD~2
```

Nó giúp tìm commit từng được `HEAD` hoặc ref trỏ tới. Nếu bạn sửa file rồi `restore` mà chưa add/commit/stash, reflog không có phiên bản đó để cứu.

### Quy trình cứu hộ đúng thứ tự

1. Dừng tạo thêm thay đổi và tránh chạy garbage collection.
2. Xem `git status` để biết operation dang dở hay không.
3. Xem `git reflog --date=local`.
4. Dùng `git show <id>` xác minh candidate.
5. Tạo `git branch rescue/... <id>` trước khi sửa graph tiếp.

Tạo branch cứu hộ biến một ID mong manh trong reflog thành lịch sử reachable rõ ràng.

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
