# Git từ nguyên lý đầu tiên

> Mục tiêu của tài liệu này không phải là giúp bạn thuộc nhiều lệnh Git. Mục tiêu là xây dựng một mô hình tinh thần đủ chính xác để bạn có thể **tự suy luận** Git đang làm gì, vì sao một lệnh có tác dụng như vậy, dữ liệu đang nằm ở đâu, và phải phục hồi thế nào khi có sự cố.

## Cách học nhanh nhất với tài liệu này

Đọc theo ba lượt:

1. **Lượt 1 — Nắm khung:** đọc phần 1–6 để hiểu object, commit graph, ref, `HEAD`, index và working tree.
2. **Lượt 2 — Suy luận thao tác:** đọc phần 7–13 và luôn tự vẽ đồ thị trước khi nhìn kết quả.
3. **Lượt 3 — Làm thí nghiệm:** tự gõ các bài lab ở phần 16. Đừng chỉ đọc output.

Nếu chỉ nhớ một câu, hãy nhớ:

> Git là cơ sở dữ liệu object bất biến, định danh bằng nội dung; lịch sử là đồ thị commit; branch/tag/HEAD là các con trỏ; phần lớn lệnh Git chỉ tạo object mới, di chuyển con trỏ, hoặc đồng bộ ba vùng trạng thái.

---

## 1. Bắt đầu từ bài toán gốc

Giả sử chưa tồn tại Git. Ta cần xây một hệ thống có thể:

- lưu nhiều phiên bản của một dự án;
- biết ai tạo phiên bản nào, khi nào và vì sao;
- chuyển nhanh giữa các phiên bản;
- cho nhiều người làm việc độc lập;
- hợp nhất công việc;
- phát hiện nội dung bị thay đổi hoặc hỏng;
- không lưu lặp vô ích;
- hoạt động được khi không có mạng.

Một giải pháp ngây thơ là sao chép thư mục:

```text
project-final/
project-final-v2/
project-final-v2-really-final/
```

Cách này lưu được phiên bản nhưng thiếu quan hệ lịch sử, tốn dung lượng, khó hợp nhất và khó xác minh tính toàn vẹn.

Ta có thể cải tiến bằng cách lưu từng thay đổi dạng patch. Nhưng nếu muốn lấy phiên bản thứ 10.000, hệ thống có thể phải phát lại hàng nghìn patch. Patch cũng không tự trả lời rõ ràng “toàn bộ dự án ở thời điểm này trông như thế nào?”.

Git chọn một nền tảng khác:

1. Mỗi phiên bản được mô hình hóa như một **snapshot** của cây thư mục.
2. Nội dung được lưu thành các **object bất biến**.
3. Object được định danh bằng **hash của chính nội dung**.
4. Commit liên kết với commit cha, tạo thành đồ thị lịch sử.
5. Những cái tên thân thiện như branch và tag chỉ là con trỏ tới object.

Đây là các nguyên lý tạo ra gần như toàn bộ hành vi của Git.

---

## 2. Git không bắt đầu từ file; Git bắt đầu từ nội dung

Trong working directory, bạn nhìn thấy đường dẫn và file. Trong object database, Git chủ yếu nhìn thấy **nội dung**.

Nếu hai file có cùng nội dung, nội dung đó có thể được biểu diễn bởi cùng một blob. Nếu đổi tên file mà không đổi nội dung, blob không đổi. Git không lưu thuộc tính “file này được rename từ file kia” bên trong blob hay commit. Rename thường được suy luận sau đó bằng độ tương đồng nội dung.

Điểm này giải thích nhiều điều:

- Git có thể deduplicate nội dung giống nhau.
- Rename không phải một loại object đặc biệt.
- Quyền executable được lưu trong tree, nhưng phần lớn metadata của filesystem không được Git theo dõi.
- Thư mục rỗng không tồn tại trong mô hình dữ liệu Git vì tree chỉ tồn tại qua các entry trỏ tới nội dung.

### Content-addressed storage

Ý tưởng khái niệm:

```text
object_id = HASH(header + content)
```

Với Git hiện đại, thuật toán hash có thể phụ thuộc định dạng repository; SHA-1 là định dạng truyền thống, còn SHA-256 được hỗ trợ trong repository được khởi tạo tương ứng. Điều quan trọng không phải tên thuật toán mà là tính chất:

> ID được suy ra từ dữ liệu, thay vì được cấp phát độc lập như số thứ tự trong database.

Hệ quả:

- Cùng loại object và cùng nội dung → cùng ID.
- Nội dung thay đổi dù rất nhỏ → ID khác.
- Một object đã tồn tại không được “sửa tại chỗ”; Git tạo object mới.
- Khi commit cha đổi, commit con cũng có ID mới vì ID cha nằm trong nội dung commit con.

Hash giúp phát hiện dữ liệu vô tình bị thay đổi và làm các object liên kết thành một cấu trúc có thể kiểm chứng. Hash không có nghĩa repository tự động đáng tin về tác giả hay chống được mọi tấn công; xác thực danh tính và chữ ký là lớp vấn đề khác.

---

## 3. Bốn loại object tạo nên Git

Git có bốn loại object cốt lõi:

### 3.1 Blob: nội dung file

Blob lưu bytes của nội dung, không lưu tên file.

```text
blob B1 = "hello\n"
```

Tên và vị trí file được tree quản lý. Vì vậy cùng blob có thể xuất hiện ở nhiều đường dẫn.

### 3.2 Tree: một thư mục

Tree chứa danh sách entry, mỗi entry có:

- mode;
- tên;
- object ID trỏ tới blob hoặc tree con.

Ví dụ khái niệm:

```text
tree T1
├── 100644 README.md → blob B1
├── 100755 build.sh  → blob B2
└── 040000 src       → tree T2
    └── 100644 app.js → blob B3
```

Tree là lý do Git có thể biểu diễn snapshot của toàn bộ cấu trúc thư mục mà vẫn tái sử dụng những phần không đổi.

Ví dụ chỉ sửa `src/app.js`:

- blob của `README.md` được tái sử dụng;
- blob của `build.sh` được tái sử dụng;
- tạo blob mới cho `app.js`;
- tạo tree mới cho `src` vì entry con đổi;
- tạo root tree mới vì ID của `src` đổi.

### 3.3 Commit: snapshot có ngữ cảnh lịch sử

Commit chứa đại ý:

```text
tree <root-tree-id>
parent <parent-commit-id>     # không có ở commit đầu tiên
author <identity + time>
committer <identity + time>

<commit message>
```

Commit **không chứa branch name**. Nó cũng không trực tiếp chứa “diff”. Diff là kết quả tính toán khi so sánh snapshot của hai commit.

Phân biệt:

- **author**: người tạo thay đổi ban đầu;
- **committer**: người đưa phiên bản thay đổi đó vào lịch sử tại thời điểm hiện tại.

Rebase/cherry-pick thường giữ author nhưng làm thay đổi committer và parent, do đó tạo commit ID mới.

### 3.4 Annotated tag: nhãn bất biến có metadata

Annotated tag là một object chứa tên tag, thông điệp, người tạo, thời gian và tham chiếu tới object khác; nó có thể được ký. Lightweight tag chỉ là một ref trực tiếp, không tạo tag object riêng.

### Quan sát object thật

```bash
git cat-file -t <object-id>   # loại object
git cat-file -p <object-id>   # nội dung dễ đọc
git ls-tree <commit-or-tree>
git rev-parse HEAD
```

---

## 4. Snapshot không có nghĩa Git luôn tốn nguyên một bản sao

Ở tầng mô hình, mỗi commit là snapshot hoàn chỉnh. Ở tầng lưu trữ, Git tái sử dụng object giống nhau và có thể nén object thành packfile, trong đó delta compression được dùng để tiết kiệm dung lượng.

Hai mệnh đề cùng đúng:

- **Mô hình logic:** Git lưu snapshot.
- **Cài đặt vật lý:** Git có thể nén và lưu chênh lệch giữa object trong packfile.

Không nên dùng chi tiết tối ưu hóa vật lý để suy luận hành vi lịch sử. Khi nghĩ về commit, hãy nghĩ snapshot.

---

## 5. Lịch sử Git là một DAG

Mỗi commit trỏ về parent của nó:

```text
A ← B ← C
```

Mũi tên ở đây nghĩa là “commit bên phải chứa ID của parent bên trái”. Ta thường đọc lịch sử theo chiều ngược lại, từ commit hiện tại về tổ tiên.

Khi phân nhánh:

```text
      D ← E
     /
A ← B ← C
```

Khi merge:

```text
      D ← E
     /     \
A ← B ← C ← M
```

`M` có hai parent: thường parent thứ nhất là nhánh đang checkout trước khi merge, parent thứ hai là commit được merge vào.

Đồ thị này là DAG — directed acyclic graph:

- directed: commit trỏ theo một hướng về parent;
- acyclic: commit mới không thể là tổ tiên của chính nó;
- graph: có thể phân nhánh và hội tụ, không chỉ là một danh sách.

### Quan hệ quan trọng hơn thời gian

Timestamp có thể sai, bị sửa hoặc không cùng múi giờ. Quan hệ tổ tiên do parent tạo ra mới là cấu trúc lịch sử chính xác.

Các câu hỏi Git thường trả lời trên graph:

- Commit X có phải tổ tiên của Y không?
- Tổ tiên chung tốt nhất của X và Y là gì?
- Những commit nào reachable từ branch A nhưng không reachable từ B?
- Ref nào đang làm một commit còn reachable?

### Reachability là khái niệm sống còn

Một object “reachable” nếu có thể đi từ một ref đã biết qua các liên kết object để tới nó.

```text
ref → commit → parent/tree → commit/blob/tree ...
```

Object không còn reachable chưa chắc bị xóa ngay. Reflog và cơ chế garbage collection có thể giữ nó một thời gian. Đây là nền tảng của khả năng cứu dữ liệu sau nhiều thao tác tưởng như nguy hiểm.

---

## 6. Ref, branch, tag và HEAD chỉ là con trỏ

### Branch

Branch là một ref có thể di chuyển, thường nằm dưới:

```text
.git/refs/heads/main
```

Nội dung về mặt khái niệm chỉ là:

```text
<commit-id>
```

Khi commit trên `main`:

```text
trước: A ← B    main → B
sau:  A ← B ← C    main → C
```

Git tạo commit `C`, rồi di chuyển `main`. Git không “thêm commit vào bên trong branch”; branch chỉ là cái tên trỏ tới đầu một chuỗi lịch sử.

### Tag

Tag thường được dùng như con trỏ không di chuyển, chẳng hạn `v1.0.0`. Về kỹ thuật tag vẫn có thể bị thay đổi, nhưng quy ước xã hội coi release tag đã công bố là ổn định.

### HEAD

Thông thường `HEAD` là symbolic ref:

```text
HEAD → refs/heads/main → commit C
```

Khi commit, branch mà `HEAD` đang trỏ tới sẽ di chuyển.

Trong detached HEAD:

```text
HEAD → commit B
```

Bạn vẫn có thể tạo commit mới, nhưng không branch nào tự động trỏ tới commit đó. Muốn giữ nó lâu dài, hãy tạo branch hoặc tag:

```bash
git switch -c saved-work
```

### Tên chỉ là lớp tiện ích

`HEAD`, `main`, `v1.0.0`, `origin/main` cuối cùng đều giúp Git tìm object ID. Nhiều cú pháp revision cũng chỉ là phép duyệt graph:

```text
HEAD^    parent thứ nhất của HEAD
HEAD^2   parent thứ hai của một merge commit
HEAD~3   đi theo parent thứ nhất ba lần
A..B     reachable từ B nhưng không reachable từ A
A...B    khác biệt đối xứng quanh merge base
```

Lưu ý: ý nghĩa cụ thể của `...` phụ thuộc lệnh; ví dụ `git diff A...B` dùng merge base theo cách khác với tập commit của `git log A...B`.

---

## 7. Ba vùng trạng thái: lý do Git có staging area

Git phải biểu diễn ba câu hỏi khác nhau:

1. Commit hiện tại chứa snapshot nào?
2. Commit tiếp theo sẽ chứa snapshot nào?
3. File trên ổ đĩa hiện đang trông như thế nào?

Ba vùng tương ứng:

```text
HEAD commit       Index / staging area       Working tree
(đã commit)       (snapshot kế tiếp)          (đang chỉnh sửa)
```

Luồng phổ biến:

```text
HEAD  --checkout/restore-->  Index  --> Working tree
HEAD  <--commit------------- Index <-- add -- Working tree
```

### Index thực sự là gì?

Index không chỉ là “danh sách file đã add”. Nó gần với một bản mô tả tree dự kiến cho commit tiếp theo: đường dẫn, mode, object ID và một số metadata hỗ trợ.

`git add file.txt` có nghĩa chính xác hơn là:

1. đọc nội dung hiện tại của `file.txt`;
2. tạo/tái sử dụng blob;
3. cập nhật entry của file đó trong index.

Nếu sửa file thêm sau `git add`, bạn tạo ra hai phiên bản đồng thời:

- phiên bản trong index sẽ được commit;
- phiên bản mới hơn trong working tree chưa được stage.

### Hai loại diff quan trọng

```bash
git diff
```

So sánh working tree với index: phần **chưa stage**.

```bash
git diff --staged
```

So sánh index với `HEAD`: phần **sắp được commit**.

Một file có thể đồng thời xuất hiện ở cả hai nhóm.

### `git status` là phép so sánh, không phải trạng thái bí ẩn

Đơn giản hóa:

```text
HEAD vs Index         → changes to be committed
Index vs Working tree → changes not staged
Không có trong Index  → untracked
```

---

## 8. Commit là một transaction tạo snapshot mới

Khi chạy `git commit`, về mặt khái niệm Git:

1. tạo tree từ index;
2. tạo commit trỏ tới tree đó;
3. đặt parent là commit hiện tại của `HEAD`;
4. ghi metadata và message;
5. di chuyển branch hiện tại tới commit mới.

Working tree không phải nguồn trực tiếp của commit; **index mới là nguồn**.

Vì vậy:

- thay đổi chưa `add` không đi vào commit;
- `git commit -a` chỉ tự stage các file đã được theo dõi, không tự thêm untracked file;
- commit có thể chỉ chứa một phần thay đổi đang tồn tại trên ổ đĩa;
- commit tốt nên đại diện một thay đổi logic hoàn chỉnh, không nhất thiết toàn bộ công việc trong ngày.

### Commit ID thay đổi khi nào?

ID commit phụ thuộc vào toàn bộ nội dung commit, gồm tree, parent, author, committer, timestamp, message. Thay một thành phần là tạo commit khác.

Do đó `git commit --amend` không sửa commit cũ. Nó tạo commit mới rồi di chuyển branch sang commit mới.

---

## 9. Checkout, switch, restore và reset dưới một mô hình chung

Các lệnh này gây nhầm vì chúng có thể tác động lên những vùng khác nhau. Đừng học thuộc bằng tên; hãy hỏi:

- Ref/`HEAD` có di chuyển không?
- Index có bị ghi lại không?
- Working tree có bị ghi lại không?

### `git switch`

Mục tiêu chính: chuyển branch hoặc tạo branch.

```bash
git switch feature
git switch -c new-feature
```

Thông thường Git cập nhật `HEAD`, index và working tree để phản ánh snapshot của branch đích, đồng thời từ chối nếu thay đổi local có nguy cơ bị ghi đè.

### `git restore`

Mục tiêu chính: khôi phục nội dung file.

```bash
git restore file.txt              # Index → Working tree
git restore --staged file.txt     # HEAD → Index
git restore --source=<C> file.txt # Commit C → Working tree
```

`restore` có thể ghi đè thay đổi chưa lưu. Luôn xem `git diff` trước.

### `git reset`

`reset` trước hết di chuyển branch hiện tại/`HEAD` tới commit đích, sau đó tùy mode mà đồng bộ thêm vùng khác:

| Lệnh | Ref/HEAD | Index | Working tree |
|---|---:|---:|---:|
| `git reset --soft C` | đổi | giữ | giữ |
| `git reset --mixed C` | đổi | đặt theo C | giữ |
| `git reset --hard C` | đổi | đặt theo C | đặt theo C |

`--mixed` là mặc định.

Mô hình này giải thích:

- `--soft`: “bỏ commit nhưng giữ mọi thứ staged”.
- `--mixed`: “bỏ commit và unstage, nhưng giữ nội dung đang sửa”.
- `--hard`: “đưa cả ba lớp về snapshot đích”.

`reset --hard` nguy hiểm với thay đổi chưa được lưu thành object/commit vì working tree bị ghi đè.

### Reset một path không giống reset cả repository

```bash
git reset HEAD -- file.txt
```

Không di chuyển branch. Nó cập nhật entry của path trong index theo nguồn chỉ định; về tác dụng thường dùng, đó là unstage file. Cú pháp mới rõ ý hơn:

```bash
git restore --staged file.txt
```

---

## 10. Merge từ nguyên lý ba chiều

Giả sử:

```text
      C ← D   feature
     /
A ← B ← E ← F   main
```

Để merge `feature` vào `main`, Git cần:

- **ours**: `F`, đầu nhánh hiện tại;
- **theirs**: `D`, đầu nhánh được nhập;
- **base**: `B`, tổ tiên chung tốt nhất.

Git tính hai tập thay đổi:

```text
B → F
B → D
```

Rồi kết hợp chúng.

### Vì sao cần base?

Nếu chỉ so `F` với `D`, Git không biết một dòng khác nhau là:

- phía F đã thêm;
- phía D đã xóa;
- hay cả hai cùng sửa từ nội dung ban đầu.

Base cung cấp trạng thái gốc để phân biệt ý nghĩa thay đổi.

### Fast-forward

Nếu `main` vẫn là tổ tiên của `feature`:

```text
A ← B ← C ← D
    main      feature
```

Merge không cần tạo snapshot kết hợp. Chỉ cần di chuyển `main` tới `D`:

```text
A ← B ← C ← D
              ↑
         main, feature
```

Đó là fast-forward: một phép di chuyển ref.

### Three-way merge

Nếu cả hai phía đã tiến lên, Git tạo kết quả merge và thường tạo commit có hai parent:

```text
      C ← D ──┐
     /         ↓
A ← B ← E ← F ← M
```

### Conflict thực chất là thiếu thông tin về ý định

Conflict xảy ra khi Git không thể kết hợp chắc chắn hai thay đổi từ cùng base. Đây không chỉ là vấn đề “cùng sửa một dòng”; rename/delete, add/add, binary file và nhiều cấu trúc khác cũng có thể conflict.

Trong conflict, index có thể chứa nhiều stage cho cùng một path:

- stage 1: base;
- stage 2: ours;
- stage 3: theirs.

Bạn giải conflict bằng cách tạo ra nội dung đúng trong working tree, sau đó `git add` để ghi **kết quả đã giải quyết** vào index.

```bash
git ls-files -u          # xem entry conflict trong index
git diff                 # xem conflict chưa giải quyết
git add <file>           # xác nhận kết quả
git commit               # hoàn tất merge
```

Đừng hiểu máy móc “ours luôn là branch của tôi”. Nó là phía tương ứng với ngữ cảnh thao tác hiện tại; trong một số thao tác như rebase, trực giác tên gọi có thể gây nhầm. Luôn đọc tài liệu/lệnh và quan sát graph.

---

## 11. Rebase là phát lại thay đổi để tạo commit mới

Ban đầu:

```text
      D ← E   feature
     /
A ← B ← C     main
```

`git rebase main` khi đang ở `feature` khái niệm hóa như sau:

1. tìm merge base `B`;
2. xác định thay đổi riêng của `D`, rồi `E`;
3. tạm đặt feature trên `C`;
4. áp dụng lại từng thay đổi, tạo `D'`, `E'`.

```text
A ← B ← C ← D' ← E'
         main       feature

commit cũ: D ← E    (không còn được feature trỏ tới)
```

`D'` không phải `D` được “di chuyển”. Parent đổi nên commit ID bắt buộc đổi. `E'` cũng đổi theo.

### Merge và rebase giải hai bài toán trình bày khác nhau

- **Merge** giữ topology thật của lịch sử và tạo điểm hội tụ.
- **Rebase** viết lại chuỗi commit để đặt nó lên nền mới, tạo lịch sử tuyến tính hơn.

Không có lựa chọn đúng tuyệt đối. Nguyên tắc an toàn:

> Tránh rebase một lịch sử công khai mà người khác có thể đã dùng làm nền, trừ khi nhóm đã thống nhất việc viết lại và cách đồng bộ.

### Interactive rebase

Interactive rebase cho phép phát lại có kiểm soát:

- đổi thứ tự commit;
- gộp commit;
- sửa message;
- dừng để chỉnh nội dung;
- bỏ commit.

Mỗi thao tác vẫn quy về: tạo một chuỗi commit mới và di chuyển ref.

---

## 12. Cherry-pick và revert: cùng tạo commit mới, khác mục tiêu

### Cherry-pick

`git cherry-pick X` lấy thay đổi do commit `X` tạo ra so với parent của nó, rồi áp dụng lên `HEAD` hiện tại để tạo commit mới.

```text
A ← B ← C        main
     \
      D ← X      other

Sau cherry-pick X lên main:
A ← B ← C ← X'   main
```

`X'` thường có patch tương tự `X`, nhưng parent và ID khác.

### Revert

`git revert X` tạo commit mới có thay đổi đảo ngược hiệu ứng của `X`.

```text
A ← B ← X ← C ← R
```

`X` vẫn còn trong lịch sử; `R` công khai việc hoàn tác. Đây thường là lựa chọn an toàn cho lịch sử đã chia sẻ.

### Reset và revert khác bản chất

- `reset`: di chuyển ref, có thể làm commit không còn nằm trên nhánh.
- `revert`: thêm commit mới, giữ nguyên lịch sử cũ.

Quy tắc thực dụng:

- lịch sử local/chưa chia sẻ: reset hoặc rebase có thể phù hợp;
- lịch sử đã chia sẻ: ưu tiên revert nếu không có thỏa thuận viết lại.

---

## 13. Distributed Git: remote chỉ là repository khác

Git không cần một server trung tâm về mặt mô hình. Mỗi clone thông thường có:

- object database;
- phần lớn hoặc toàn bộ lịch sử;
- local branches;
- remote-tracking refs;
- working tree và index riêng.

`origin` chỉ là tên mặc định thường dùng cho một remote URL.

### Ba cái tên dễ nhầm

```text
main                 local branch, do bạn di chuyển
origin/main          remote-tracking ref trong máy bạn
main trên server     ref thật trong repository remote
```

`origin/main` không phải kết nối trực tiếp thời gian thực. Nó là **ký ức local** về trạng thái `main` trên `origin` sau lần fetch gần nhất.

### Fetch

`git fetch origin`:

1. trao đổi thông tin object/ref với remote;
2. tải object còn thiếu;
3. cập nhật remote-tracking refs như `origin/main`.

Thông thường nó không thay đổi local branch, index hay working tree của bạn.

### Pull

`git pull` không phải primitive bí ẩn. Thông thường nó là:

```text
fetch + merge
```

hoặc khi cấu hình/chỉ định:

```text
fetch + rebase
```

Tách thành `fetch` rồi quan sát graph thường giúp học và kiểm soát tốt hơn.

### Push

Push gửi object cần thiết và đề nghị remote cập nhật ref:

```text
"Hãy đổi refs/heads/main từ old-id sang new-id."
```

Remote thường từ chối non-fast-forward vì cập nhật đó có thể làm lịch sử hiện tại của remote không còn reachable từ branch.

### Force push và lease

Force push cho phép ghi đè ref dù không fast-forward. Nếu thật sự cần viết lại branch đã publish, `--force-with-lease` an toàn hơn `--force`: nó chỉ cập nhật nếu remote ref vẫn ở trạng thái bạn kỳ vọng, giảm nguy cơ ghi đè công việc mới của người khác.

```bash
git push --force-with-lease
```

Nó giảm rủi ro, không biến việc viết lại lịch sử thành vô hại.

---

## 14. Khi dữ liệu “mất”: reflog, reachability và garbage collection

Giả sử:

```text
A ← B ← C   main
```

Sau:

```bash
git reset --hard A
```

Ta có:

```text
A             main
 \
  B ← C       không còn reachable từ main
```

Commit `B`, `C` thường vẫn tồn tại trong object database một thời gian. Reflog ghi lại các giá trị trước đây của ref/`HEAD` trong repository local:

```bash
git reflog
git branch rescue <old-commit-id>
```

### Reflog không phải lịch sử chia sẻ

- Reflog thường chỉ có ở local.
- Nó có chính sách hết hạn.
- Object unreachable cuối cùng có thể bị garbage collection xóa.
- Dữ liệu chưa từng được `add`, stash hoặc commit có thể không tồn tại dưới dạng Git object có thể cứu.

Thứ tự phản ứng khi gặp sự cố:

1. dừng các thao tác dọn dẹp/ghi đè;
2. chạy `git status`;
3. xem `git reflog`;
4. tạo branch cứu hộ ngay khi tìm thấy commit;
5. chỉ sau đó mới chỉnh lại lịch sử.

Ví dụ:

```bash
git reflog --date=local
git show <candidate-id>
git branch rescue/lost-work <candidate-id>
```

---

## 15. Mô hình suy luận thống nhất cho mọi lệnh

Trước một lệnh Git, hãy trả lời năm câu hỏi:

1. `HEAD` đang symbolic tới branch hay detached tại commit?
2. Các ref liên quan đang trỏ tới commit nào?
3. Đồ thị parent hiện ra sao?
4. Index khác `HEAD` ở đâu?
5. Working tree khác index ở đâu?

Sau đó hỏi lệnh sẽ làm gì trong bốn nhóm:

```text
1. Tạo object mới?
2. Di chuyển ref/HEAD?
3. Ghi lại index?
4. Ghi lại working tree?
```

Bảng suy luận nhanh:

| Lệnh | Tạo object/commit | Di chuyển ref | Index | Working tree |
|---|---|---|---|---|
| `add` | blob nếu cần | không | cập nhật | giữ |
| `commit` | tree + commit | branch tiến lên | thường giữ | giữ |
| `switch` | không | đổi HEAD | cập nhật | cập nhật |
| `reset --soft` | không | có | giữ | giữ |
| `reset --mixed` | không | có | cập nhật | giữ |
| `reset --hard` | không | có | cập nhật | cập nhật |
| `merge` | có thể tạo commit | có thể tiến lên | cập nhật | cập nhật |
| `rebase` | tạo commit mới | có | cập nhật | cập nhật |
| `cherry-pick` | tạo commit mới | có | cập nhật | cập nhật |
| `revert` | tạo commit mới | có | cập nhật | cập nhật |
| `fetch` | tải object | remote-tracking refs | giữ | giữ |

Bảng là mô hình giản lược; dirty working tree, conflict, hook, submodule và tùy chọn cụ thể có thể thay đổi chi tiết. Nhưng nó đủ mạnh để dự đoán phần lớn hành vi thường ngày.

---

## 16. Lab thực hành: nhìn xuyên qua porcelain

Các lệnh thân thiện như `add`, `commit`, `switch` được gọi là porcelain; các lệnh cấp thấp thường được gọi là plumbing. Lab này dùng cả hai để thấy mô hình thật.

> Hãy chạy trong một thư mục thử nghiệm mới, không chạy trên dự án quan trọng.

### Lab 1 — Tự tạo blob, tree và commit

```bash
mkdir git-lab
cd git-lab
git init
printf 'hello\n' > hello.txt
git hash-object hello.txt
git hash-object -w hello.txt
```

Hai lần `hash-object` cho cùng ID; `-w` yêu cầu ghi object vào database.

```bash
git add hello.txt
git ls-files --stage
git write-tree
```

Quan sát ID blob trong index và ID tree. Sau đó:

```bash
git commit -m 'first snapshot'
git cat-file -p HEAD
git cat-file -p HEAD^{tree}
git cat-file -p HEAD:hello.txt
```

Câu hỏi tự kiểm tra:

- Commit có chứa diff không?
- Tên `hello.txt` nằm trong blob hay tree?
- `HEAD^{tree}` nghĩa là gì?

### Lab 2 — Chứng minh index là phiên bản độc lập

```bash
printf 'version 1\n' > note.txt
git add note.txt
printf 'version 2\n' > note.txt
git status --short
git diff --staged
git diff
```

Bạn sẽ thấy `version 1` trong staged diff và `version 2` là thay đổi tiếp theo trong working tree.

```bash
git commit -m 'commit staged version'
git show HEAD:note.txt
printf 'working tree: '
cat note.txt
```

Commit chứa version 1; working tree vẫn là version 2.

### Lab 3 — Branch chỉ là ref

```bash
git switch -c experiment
git rev-parse HEAD
git rev-parse experiment
git symbolic-ref HEAD
```

Tạo commit:

```bash
printf 'experiment\n' > feature.txt
git add feature.txt
git commit -m 'experiment'
git log --oneline --graph --decorate --all
```

Quan sát `experiment` di chuyển còn branch cũ đứng yên.

### Lab 4 — Fast-forward và merge commit

Tạo một branch từ `main`, commit khi `main` chưa đổi, rồi merge để thấy fast-forward. Sau đó tạo thay đổi độc lập ở cả hai branch và merge lại để thấy commit hai parent.

Sau merge commit:

```bash
git cat-file -p HEAD
git show --no-patch --pretty=raw HEAD
```

Tìm hai dòng `parent`.

### Lab 5 — Cứu commit bằng reflog

```bash
git branch before-reset
printf 'temporary\n' > temporary.txt
git add temporary.txt
git commit -m 'commit to recover'
git rev-parse HEAD
git reset --hard HEAD~1
git reflog -5
```

Lấy ID commit vừa mất khỏi branch rồi:

```bash
git branch recovered <commit-id>
git log --oneline --graph --decorate --all
```

Commit chưa “biến mất”; chỉ con trỏ đã rời khỏi nó.

### Lab 6 — Rebase tạo ID mới

1. Tạo branch `topic` và hai commit.
2. Trở lại `main`, tạo một commit khác.
3. Ghi lại ID hai commit trên `topic`.
4. `git switch topic && git rebase main`.
5. So sánh ID trước và sau.

Nội dung cuối có thể tương đương nhưng commit identity khác vì parent khác.

---

## 17. Quy trình debug Git thay vì đoán lệnh

Khi không chắc, đừng ngay lập tức chạy thêm `reset`, `checkout` hay `pull`. Thu thập trạng thái:

```bash
git status
git branch -vv
git log --oneline --graph --decorate --all -n 30
git diff
git diff --staged
git reflog -n 20
```

Sau đó viết ra bốn dòng:

```text
HEAD = ?
local branch = ?
remote-tracking branch = ?
thay đổi chỉ nằm ở working tree/index/commit = ?
```

### Tình huống: “Tôi commit nhầm branch”

Suy luận:

1. Commit là object hợp lệ.
2. Chỉ ref đang trỏ chưa đúng.
3. Tạo branch đúng tại commit đó.
4. Đưa branch cũ về vị trí trước, nếu lịch sử chưa chia sẻ.

Ví dụ khái niệm:

```bash
git branch correct-branch HEAD
git reset --hard HEAD~1
```

Không chạy máy móc nếu working tree có dữ liệu cần giữ hoặc commit đã push.

### Tình huống: “Tôi muốn bỏ commit nhưng giữ thay đổi”

- muốn giữ staged: `git reset --soft HEAD~1`;
- muốn giữ nhưng unstage: `git reset HEAD~1`;
- commit đã chia sẻ: cân nhắc `git revert`.

### Tình huống: “Pull tạo lịch sử khó hiểu”

Tách phép toán:

```bash
git fetch
git log --oneline --graph --decorate --all
```

Rồi chủ động chọn merge hay rebase dựa vào lịch sử và quy ước nhóm.

### Tình huống: “Git bảo branch diverged”

Điều đó chỉ có nghĩa:

- local branch có commit remote không có;
- remote-tracking branch có commit local không có.

Vẽ graph, tìm merge base, rồi chọn:

- merge để giữ cả hai tuyến;
- rebase commit local lên remote nếu được phép viết lại local history;
- reset nếu xác định một phía phải bị loại bỏ và dữ liệu đã được bảo vệ.

---

## 18. Những hiểu lầm nên loại bỏ

### “Git lưu diff giữa các commit”

Sai ở mô hình logic. Commit trỏ tới snapshot tree. Diff được tính khi so hai snapshot. Packfile có thể dùng delta compression nhưng đó là tối ưu hóa lưu trữ.

### “Branch chứa commit”

Branch chỉ trỏ tới một commit. Các commit được xem là thuộc lịch sử branch nhờ reachability.

### “Commit thuộc về đúng một branch”

Commit không biết branch nào trỏ tới nó. Một commit có thể reachable từ nhiều branch hoặc không branch nào.

### “Merge đưa commit của branch kia vào branch tôi”

Các object thường đã có trong repository. Merge chủ yếu tạo quan hệ lịch sử/kết quả snapshot mới hoặc chỉ fast-forward ref.

### “Rebase chuyển commit”

Rebase tạo commit mới từ các thay đổi cũ. Commit bất biến không bị chuyển hoặc chỉnh tại chỗ.

### “`origin/main` chính là branch trên server”

Nó là remote-tracking ref local, chỉ phản ánh lần fetch gần nhất.

### “Đã xóa branch là mất commit ngay”

Xóa branch chỉ xóa ref. Commit có thể còn reachable từ ref khác hoặc còn trong reflog trước khi bị dọn.

### “Staging area chỉ dùng để chọn file”

Index chọn snapshot theo từng path, thậm chí theo từng hunk qua `git add -p`. Nó cho phép thiết kế commit độc lập với trạng thái hỗn độn của working tree.

---

## 19. Nguyên tắc làm việc giúp lịch sử có giá trị

### Commit theo đơn vị ý nghĩa

Một commit tốt:

- giải quyết một ý định logic;
- có thể review độc lập;
- giữ repository ở trạng thái hợp lý;
- có message giải thích **vì sao**, không chỉ lặp lại “đã sửa gì”.

### Stage có chủ đích

```bash
git diff
git add -p
git diff --staged
git commit
```

Hãy review staged diff như thể đó là patch sắp gửi cho người khác.

### Fetch trước, quyết định sau

```bash
git fetch --prune
git log --oneline --graph --decorate --all
```

Biết graph thật rồi mới chọn merge/rebase/reset.

### Bảo vệ trước khi viết lại

Nếu sắp thực hiện thao tác phức tạp:

```bash
git branch backup/before-operation
```

Một ref phụ rất rẻ và làm commit tiếp tục reachable.

### Không xem Git là hệ thống backup hoàn chỉnh

Git bảo vệ tốt dữ liệu đã trở thành object và còn được giữ. Nó không thay thế backup cho:

- file untracked chưa add;
- thay đổi working tree bị ghi đè;
- secret hoặc binary asset ngoài repo;
- mất toàn bộ ổ đĩa khi chưa push/backup nơi khác.

---

## 20. Security và trust từ first principles

Hash trả lời gần với câu hỏi:

> Nội dung mà tôi đang thấy có đúng là nội dung được định danh bởi ID này không?

Hash không tự trả lời:

> Người ghi trong trường author có thật sự là người đó không?

Tên và email author có thể được cấu hình tùy ý. Muốn tăng mức xác thực, cần chữ ký commit/tag và một mô hình tin cậy cho khóa/chứng thư. Ngoài ra:

- secret đã commit vẫn có thể tồn tại trong lịch sử dù file hiện tại đã xóa;
- `.gitignore` không loại secret đã được track;
- viết lại lịch sử không đảm bảo mọi clone và cache bên ngoài đã mất dữ liệu;
- branch protection và code review thuộc lớp quản trị cộng tác, không phải bản chất object database.

Nguyên tắc: secret bị commit phải được xem là đã lộ và cần rotate, không chỉ xóa khỏi commit mới nhất.

---

## 21. Các chi tiết nâng cao nối trực tiếp từ mô hình lõi

### Stash

Stash không phải vùng ma thuật. Nó lưu trạng thái bằng các commit đặc biệt và cập nhật ref/reflog liên quan. Vì vậy stash cũng dựa trên object, tree và commit.

### Worktree

`git worktree` cho phép nhiều working tree chia sẻ một object database. Mỗi worktree cần ngữ cảnh `HEAD` riêng, còn objects và refs được quản lý có phối hợp.

### Submodule

Superproject không lưu toàn bộ nội dung repository con trong tree của mình. Nó lưu một entry đặc biệt trỏ tới **commit ID** của repository con. Vì vậy clone/checkout superproject và lấy nội dung submodule là hai lớp riêng.

### Shallow clone

Shallow clone cố ý cắt lịch sử tại một độ sâu. Một số phép duyệt tổ tiên, merge base hoặc thao tác cần lịch sử đầy đủ có thể bị hạn chế cho tới khi fetch thêm.

### Garbage collection

Git có thể đóng gói, nén và cuối cùng loại object unreachable theo chính sách. Điều này không thay đổi mô hình logic, nhưng nhắc ta rằng “có thể cứu bằng reflog” là cửa sổ tạm thời, không phải bảo đảm vĩnh viễn.

---

## 22. Cheat sheet theo ý nghĩa, không theo trí nhớ

### Quan sát

```bash
git status
git log --oneline --graph --decorate --all
git diff
git diff --staged
git show <revision>
git reflog
git branch -vv
```

### Thiết kế snapshot tiếp theo

```bash
git add <path>
git add -p
git restore --staged <path>
git diff --staged
git commit
```

### Di chuyển trong graph

```bash
git switch <branch>
git switch -c <new-branch>
git merge <branch>
git rebase <new-base>
```

### Trao đổi với repository khác

```bash
git fetch <remote>
git push <remote> <branch>
git remote -v
```

### Phục hồi

```bash
git reflog
git show <old-id>
git branch rescue/<name> <old-id>
git restore <path>
git revert <commit>
```

### Nhìn object và ref

```bash
git rev-parse HEAD
git symbolic-ref HEAD
git cat-file -t <id>
git cat-file -p <id>
git ls-tree -r HEAD
git ls-files --stage
```

---

## 23. Bài kiểm tra: nếu trả lời được, bạn đã hiểu bản chất

1. Vì sao sửa commit message làm commit ID đổi?
2. Vì sao đổi parent nhưng giữ nguyên file vẫn tạo commit ID mới?
3. Vì sao một commit có thể đồng thời nằm trên nhiều branch?
4. Vì sao xóa branch thường chưa xóa object ngay?
5. `git diff` và `git diff --staged` so sánh hai cặp trạng thái nào?
6. Vì sao file có thể vừa staged vừa unstaged?
7. Fast-forward merge thực sự thay đổi cái gì?
8. Vì sao rebase gây khó khăn nếu người khác đã dựa trên commit cũ?
9. `origin/main` khác `main` trên server thế nào?
10. Vì sao revert phù hợp hơn reset cho lịch sử đã chia sẻ?
11. Git lấy đâu ra ba phiên bản khi giải merge conflict?
12. Vì sao Git không theo dõi thư mục rỗng?
13. Vì sao rename thường là kết quả suy luận thay vì metadata cố định?
14. Dữ liệu mới chỉ nằm trong working tree có được reflog bảo vệ không?
15. Khi thấy một lệnh nguy hiểm, bốn nhóm trạng thái nào cần kiểm tra?

Đáp án ngắn:

1. Message là một phần nội dung commit được hash.
2. Parent ID cũng nằm trong nội dung commit.
3. Branch chỉ là ref; nhiều ref có thể dẫn tới cùng commit qua reachability.
4. Xóa ref không lập tức xóa object; reflog/chính sách GC còn giữ nó.
5. Working tree–index và index–`HEAD`.
6. Index giữ bản cũ đã add, working tree có sửa đổi mới hơn.
7. Di chuyển ref tới commit hậu duệ.
8. Rebase tạo ID mới, làm hai chuỗi lịch sử không còn cùng identity.
9. `origin/main` là bản ghi local từ lần fetch; server có ref riêng có thể đã đổi.
10. Revert thêm lịch sử đảo ngược thay vì làm commit cũ biến khỏi nhánh.
11. Base, ours và theirs.
12. Tree chỉ chứa entry; không có nội dung thì không có object cần tham chiếu.
13. Blob không chứa tên/nguồn gốc path; Git so độ tương đồng giữa snapshot.
14. Không nhất thiết; nếu chưa tạo object/commit/stash, Git có thể không cứu được.
15. Object mới, ref/`HEAD`, index và working tree.

---

## 24. Kết tinh cuối cùng

Hãy nén toàn bộ Git thành năm tiên đề:

### Tiên đề 1: Object là bất biến và định danh bằng nội dung

Thay đổi dữ liệu nghĩa là tạo object mới, không sửa object cũ.

### Tiên đề 2: Commit trỏ tới snapshot và parent

Snapshot tạo trạng thái; parent tạo lịch sử. Lịch sử vì thế là DAG.

### Tiên đề 3: Branch và tag chỉ là ref

Tạo branch rẻ vì chỉ tạo con trỏ. Commit “biến mất” khỏi branch thường vì ref di chuyển, không phải object bị xóa ngay.

### Tiên đề 4: Index là snapshot dự kiến

Commit lấy dữ liệu từ index, không trực tiếp lấy mọi thứ trong working tree.

### Tiên đề 5: Cộng tác là trao đổi object và cập nhật ref

Fetch, push, merge và rebase đều có thể được giải thích bằng object graph cùng các con trỏ.

Từ năm tiên đề đó:

```text
commit      = tạo snapshot + node mới + di chuyển ref
branch      = ref di chuyển
tag         = ref ổn định hoặc tag object
merge       = kết hợp từ merge base, có thể tạo node nhiều parent
rebase      = phát lại thay đổi để tạo chuỗi node mới
reset       = di chuyển ref, tùy chọn đồng bộ index/working tree
revert      = tạo node mới đảo hiệu ứng node cũ
fetch       = nhận object + cập nhật remote-tracking refs
push        = gửi object + yêu cầu remote cập nhật ref
reflog      = nhật ký local về chuyển động của ref
```

Khi gặp một tình huống Git mới, đừng hỏi trước tiên “lệnh nào sửa được?”. Hãy hỏi:

> Object nào đang tồn tại, graph trông ra sao, ref nào đang trỏ ở đâu, và phiên bản mong muốn hiện nằm trong HEAD, index hay working tree?

Trả lời được câu đó, lệnh thường trở thành hệ quả hiển nhiên.
