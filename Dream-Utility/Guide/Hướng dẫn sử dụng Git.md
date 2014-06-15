## Hướng dẫn sử dụng Git
---------------------------------------------------------------------------------------------------------
Tác giả: Dynamo Trần Nam (dynamo.foss@gmail.com)
Git là công cụ quản lý mã nguồn do Linus Torvald viết để có thể nhiều người cùng hoạt động trên 1 mã nguồn, dễ theo dõi và quản lý và có tính phân tán.
### Các khái niệm cơ bản
Git hoạt động trên mỗi repository theo mô hình local - remote. Nên nhớ rằng chỉnh sửa ở repository này mà không thực hiện các thao tác liên quan đến git thì repository không bị ảnh hưởng.
Ở phía local, git hoạt động theo 3 mức:

* Thư mục làm việc (working directory)
* Bộ đệm (staging area)
* Thư mục git (git directory)

Cơ chế hoạt động của git là:

> Khi người dùng "add" các file, các thay đổi sẽ được lưu vào bộ đệm
> Khi người dùng "commit" các file, thay đổi đó sẽ được chuyển giao vào thư mục git (.git trong thư mục làm việc)
> Khi người dùng "checkout" một lần "commit" nào đó, tất cả các trạng thái của file tính tại thời điểm ngay sau khi "commit" sẽ được hoán trả về thư mục làm việc

![stateofgit](http://git-scm.com/figures/18333fig0106-tn.png)
### Các thao tác cơ bản

*Bắt đầu từ chỗ này, các lệnh sẽ được chú thích với cặp dấu đóng ngoặc vuông [] để biểu thị bắt buộc, dấu ngoặc nhọn <> để biểu thị tùy chọn có hoặc không*

#### I. Khởi tạo một repository với thư mục mã nguồn đã tồn tại chưa được quản lý:

```bash
cd source
git init
```
Lúc này thư mục *source* đã được khởi tạo là 1 thư mục được quản lý bởi git. Tất nhiên repository ở đây là local. Lúc này mức stage và mức git vẫn chưa có gì, ta phải
Thêm những file/thư mục mà ta muốn được quản lý vào stage, chẳng hạn tôi cần thêm các file README.md, thư mục xxx:

```bash
git add README.md
git add xxx
```
Cuối cùng chuyển giao các thay đổi vào mức git:

```bash
git commit -m "Khởi tạo project với nguồn có sẵn" # sau -m là thông điệp ta muốn gửi cho người khác để tiện lợi khi sửa code
```
Lúc này, local đã hoàn thành khởi tạo.

#### II. Tạo bản sao ở một repository khác

```bash
git clone [URL repository]
```
Ở đây project mới sẽ được sao chép từ remote về local tại thư mục mới daemon. Nếu muốn tạo bản sao vào thư mục khác:

```bash
git clone [URL repository] [đường dẫn thư mục]
```
Chú ý với phiên bản trên GitLab, có 2 giao thức được cho phép cho git là SSH và HTTPS, SSH phải được [thêm key](No1._Add_SSH_Key) nhận dạng.
#### III. Các thao tác cơ bản với file
Các file trong thư mục làm việc sẽ được quản lý theo 2 trạng thái: được theo dõi (tracked) và chưa được theo dõi (untracked). File được theo dõi với 3 trạng thái con: chưa thay đổi (unmodified), thay đổi (modified) và được lưu đệm (staged). Unmodified là trạng thái mặc định khi được clone về hoặc trong trạng thái bình thường (không được tạo mới, thay đổi hay xóa)
File chưa được theo dõi là bất kỳ file nào không có trong commit cuối cùng và chưa được lưu vào đệm. Vòng đời của file được miêu tả như hình dưới:
![filecycle](http://git-scm.com/figures/18333fig0201-tn.png)

*Chú ý* Thư mục và file là tương đương nhau.

**1. Kiểm tra trạng thái của file**

```bash
git status
```
Lệnh này sẽ kiểm tra trạng thái của file và đưa ra lời khuyên hợp lý.

**2. Đưa 1 file mới vào theo dõi / Lưu đệm file được chỉnh sửa**

```bash
git add [đường dẫn file]
```
*Chú ý*: Có những file ta muốn bỏ qua không theo dõi cũng như chuyển giao lên remote, tuy nhiên mỗi lần kiểm tra trạng thái đều xuất hiện file ấy. Việc này gây giới hạn màn hình hiển thị và mất thời gian. Để tránh tình trạng này, hãy đọc phần [Phụ lục](git_guide/#ix-ph-l-c).

**3. Xem các thay đổi**

Chẳng hạn file index.php đã được theo dõi với nội dung như sau

```php
<?php
echo "I'm here";
?>
```
Sau đó thay đổi nội dung của file này thành

```php
<? echo "I'm here";
?>
```
Ta sẽ xem các thay đổi bằng câu lệnh

```bash
git diff
```
Màn hình hiển thị ra như sau

```diff
diff --git a/index.php b/index.php
index 3b6c087..4b9fee1 100644
--- a/index.php
+++ b/index.php
@@ -1 +1,2 @@
-<?php echo "I'm here";?>
+<?php echo "I'm here";
+?>
```
Điều đó cho thấy file đã được sửa đổi bằng cách xóa dòng cũ và thêm 2 dòng mới. Mặc dù về cú pháp ta thấy là tương đương, tuy nhiên **git quản lý code theo dòng** nên hãy hết sức chú ý, đặc biệt là không nên thêm vào project các file binary được biên dịch tự động. Để tránh tình trạng về các file binary, hãy đọc phần [Phụ lục](git_guide/#ix-ph-l-c).
Để xem lại thay đổi mà ta thực hiện `git diff` gần nhất, ta dùng

```bash
git diff --cached
```
**4. Chuyển giao thay đổi**

```bash
git commit <đường dẫn file nếu chỉ chuyển giao 1 file>
```
Câu lệnh trên sẽ hiển thị toàn bộ các thay đổi được đưa vào bộ đệm. Dòng đầu tiên để trống chính là nơi chúng ta đưa thông điệp chuyển giao. Nếu để trống, chuyển giao bị bỏ qua. Các dòng tiếp theo nếu không bắt đầu bằng # thì sẽ là detail của commit.
Mỗi lần chuyển giao sẽ có một dãy số thập lục phân xác định phân biệt với lần chuyển giao khác. Đây chính là số hiệu của nó.
***Chú ý***: Hãy thật cẩn thận khi chuyển giao vì nếu đã chuyển giao rồi bạn sẽ không thể xóa lần chuyển giao nó đi và nó vẫn còn dấu vết trên cây. Và không nên dùng tùy chọn -m để gửi thông điệp ngắn làm cho khó nhìn trên cây các nhánh.
#### IV. Xem lịch sử chuyển giao

```bash
git log
```
Nếu muốn hữu dụng hơn, ta phải sử dụng tùy chọn của lệnh này. Chẳng hạn, muốn xem so sánh các thay đổi qua các lần chuyển giao, ta sử dụng tùy chọn `-p`, hay giới hạn số lượng 2 lần commit gần nhất, ta sử dụng tùy chọn `-2`. Kết hợp 2 tùy chọn này, ta sẽ xem được các thay đổi trong 2 lần commit gần nhất

```bash
git log -p -2
```

Một câu lệnh khác khá hữu dụng là xem lịch sử chuyển giao và thay đổi qua lần chuyển giao chỉ định, nếu không chỉ định thì là lần chuyển giao gần nhất

```bash
git show <số hiệu lần chuyển giao>
```
Chi tiết về lệnh này, vui lòng gõ lệnh `man git-log`
#### V. Hoàn tác
**1. Thay đổi thông điệp chuyển giao gần nhất**

```bash
git commit --amend
```
**2. Xóa khỏi bộ đệm một file:**

Lệnh này hữu dụng khi ta theo dõi nhầm một file hoặc lưu vào đệm nhầm

```bash
git reset HEAD <đường dẫn file nếu có> # nếu không sẽ xóa hoàn toàn bộ đệm
```
Lưu ý là lệnh này chỉ xóa khỏi bộ đệm còn các thay đổi trên thư mục làm việc vẫn được giữ nguyên

**3. Phục hồi lại file trước khi sửa đổi**

```bash
git checkout -- [đường dẫn file] # chú ý sau dấu -- là dấu cách và phải xóa nó khỏi bộ đệm trước khi phục hồi nếu có
```
**4. Xóa toàn bộ các thay đổi khỏi thư mục làm việc**

```bash
git reset --hard
```
#### VI. Làm việc với remote repository
**1. Hiển thị thông tin remote hiện tại**

```bash
git remote -v
```
**2. Thêm remote repository**

```bash
git remote add [tên remote] [URL remote]
```
*Chú ý:* Mặc định sẽ là thao tác với remote repository tên là origin. Các lệnh có 1 tham số mà để trống thì sẽ thao tác trên repo này
**3. Lấy mã nguồn từ remote**

```bash
git fetch <tên remote>
```
Lệnh trên sẽ lấy các thay đổi mã nguồn từ server về, tuy nhiên vẫn giữ mã nguồn hiện tại ở local

```bash
git pull <tên remote> <tên branch ở remote>
```
Lệnh này sẽ lấy các thay đổi từ server về và cố gắng tự động hợp nhất vào mã nguồn hiện tại ở local

**4. Đưa mã nguồn lên remote**

```bash
git push <tên remote> <tên branch ở remote>
```
*Chú ý:* Kiểm tra cẩn thận trước khi đưa mã nguồn lên remote

**5. Hiển thị thông tin chi tiết về quản lý nhánh**

```bash
git remote show [tên remote]
```
#### VII. Làm việc với nhánh
Hãy tưởng tượng Git là một cấu trúc cây, khi đó nhánh giống như những nhánh cây mà các nút trên thân cây là các commit. Tiến độ của nhánh hiện tại giống như một con trỏ trỏ tới ngọn của cây đó hoặc các nút trên thân cây.
Nhánh được đề xuất ra để có thể phát triển tính năng mới, sửa lỗi... mã nguồn cũ mà không làm ảnh hưởng mã nguồn đã hoạt động tốt và có thể hợp nhất lại với bản cũ bất cứ khi nào. Điều này làm giảm nguy cơ sai sót, chồng lấn, trùng lặp... khi nhiều người cùng code một mã nguồn.

**1. Tạo nhánh mới hoặc rẽ nhánh (tách ra từ nhánh hiện tại)**

```bash
git branch [tên branch]
```
**2. Chuyển nhánh hiện tại**

Đang làm việc trên nhánh này, ta muốn chuyển sang làm việc trên nhánh khác. Khi đó toàn bộ mã nguồn trong thư mục sẽ được chuyển sang mã nguồn mới từ ảnh của nhánh đích

```bash
git checkout [tên branch]
```
Muốn tạo nhánh mới và chuyển luôn sang nhánh mới này, hãy dùng câu lệnh hiệu quả hơn là

```bash
git checkout -b [tên branch mới]
```
**3. Xóa nhánh**

Xóa toàn bộ tất cả các commit của một nhánh, hãy dùng lệnh

```bash
git branch -d [tên branch]
```
**4. Hợp nhất các nhánh**

Đến một lúc nào đó, ta cần hợp nhất mã nguồn 1 nhánh vào nhánh hiện tại

```bash
git merge [tên nhánh]
```
Việc hợp nhất mã nguồn sẽ được thực thi một cách tự động. Tuy nhiên, đa số trong thực tế, việc hợp nhất sẽ gây ra xung đột mã nguồn. Khi thực hiện lệnh trên, màn hình sẽ hiển thị các file xung đột và thông tin về các dòng xung đột, việc của chúng ta là sửa lại các file xung đột đó bằng tay. Git cũng cung cấp các công cụ để merge bằng tool qua lệnh

```bash
git mergetool # mặc định trên server dev dùng tool của git
```
tuy nhiên hơi phức tạp với nguời mới dùng. Bạn có thể tìm hiểu thêm bằng lệnh `man git-mergetool`
Lưu ý rằng việc hợp nhất mã nguồn sẽ được chuyển giao luôn lên remote. Nếu không muốn chuyển giao và xem trước, hãy thực hiện lệnh

```bash
git merge --no-commit --no-ff [tên nhánh]
```
**5. Đưa nhánh lên remote**

```bash
git push [tên remote] [tên nhánh local]:[tên nhánh remote]
# Phần đằng sau dấu : có thể bỏ đi nếu tên 2 nhánh là như nhau
```
**6. Lấy nhánh từ remote**

Cần lấy thông tin từ remote trước khi thực hiện lệnh này

```bash
git checkout [tên nhánh local] [tên remote]/[tên nhánh remote]
```
Việc này sẽ map tên nhánh ở local với tên nhánh ở remote, khi đưa lên bằng lệnh `git push` sẽ mặc định lên tên nhánh remote đã map
**7. Xóa nhánh trên remote**

```bash
git push [tên remote] :[tên nhánh remote]
```
Ta cảm thấy nó tương tự như lệnh đưa nhánh lên remote. Ý nghĩa của nó là: "Đưa lên remote nhánh abc với nội dung là không có gì"

**8. Xem nhánh trên remote**

```bash
git branch -r
```
**9. So sánh sự thay đổi trên 2 nhánh**

```bash
git diff <tên nhánh 1>...<tên nhánh 2>
```
Lưu ý là thứ tự của 2 nhánh là quan trọng. Việc so sánh này chỉ dựa trên những commit đã thay đổi từ khi nhánh 2 được rẽ nhánh so với thời điểm của nhánh 1. Vì vậy, kết quả nếu đảo thứ tự là khác nhau. Nó không hẳn là kết quả của việc tổng hợp các thay đổi ở trên cả 2 nhánh và cũng không hẳn là kết quả của việc kiểm tra xung đột trước khi hợp nhất nhánh.
#### VIII. Các thao tác hay dùng khác
**1. Lưu giữ trạng thái**

Đôi khi, ta đang sửa đổi mã nguồn ở nhánh này, tuy nhiên công việc mới cần ta chuyển sang nhánh khác. Tuy nhiên, ta lại không muốn chuyển giao nó vào nhánh hiện tại. Nếu chuyển luôn sang nhánh khác sẽ gặp thông báo cần commit. Ta có thể khắc phục điều này dễ dàng bởi stash (khu lưu giữ). Hãy tưởng tượng nó là một thùng rác được sắp xếp. Khi nào ta cần thì ta lại lục thùng rác để lấy nó ra:

```bash
git stash
```
Câu lệnh trên sẽ đẩy toàn bộ các file chưa được theo dõi (untracked) và sửa đổi (modified) vào stash.
Khi có nhiều lần stash, ta nên xem danh sách các stash được lưu giữ

```bash
git stash list
```
Nó sẽ hiển thị tương tự như sau:

```bash
stash@{0}: WIP on master: 6837f85 New Picture
stash@{1}: WIP on master: 29c5f42 Them font powerline github
```

Ở đây `stash@{0}` chính là id của các stash
Vậy đến lúc cần lấy nó ra? Hãy dùng câu lệnh sau

```bash
git stash apply <id của stash> # nếu không có id, lệnh này sẽ lấy stash gần nhất (id thấp nhất)
```
Xóa lưu giữ

```bash
git stash drop <id của stash> # nếu không có id, lệnh này sẽ lấy stash gần nhất (id cao nhất)
```
Để tự động lấy stash gần nhất và xóa nó

```bash
git stash pop
```
Vậy lúc lấy nó ra rồi lại muốn trở về trạng thái cũ?

```bash
git stash show -p <id của stash> | git apply -R # nếu không có id, lệnh này sẽ lấy stash gần nhất (id cao nhất)
```
Tạo brach mới từ stash gần nhất

```bash
git stash branch [tên nhánh]
```
**2. Đổi thứ tự các lần chuyển giao**

Đổi thứ tự các lần chuyển giao là điều cần thiết trong dự án thực tế. Ở đây ta xét trường hợp đơn giản nhất là đưa một lần chuyển giao xác định lên đầu nhánh.

```bash
git revert <số hiệu của lần chuyển giao>
```
Lưu ý là câu lệnh này chỉ đưa lần chuyển giao xác định này lên đầu của một nhánh (ngọn) mà không xóa các lần chuyển giao từ nó trở đi.

**3. (sẽ tiếp tục bổ sung)**

>Tham khảo Git qua sách "Version Control with Git" - Jon Loeliger & Matthew McCullough

#### IX. Phụ lục
**1. Bỏ qua file không cho theo dõi**

Phần này khá cần thiết khi muốn bỏ qua những file mà không muốn được quản lý bởi git nhưng vẫn xuất hiện trong thư mục làm việc, chẳng hạn các file binary, các file cache sinh ra khi sử dụng editor, các file .bak.... Ta cần theo các bước sau đây
Tạo file .gitignore trong thư mục làm việc gốc bằng vim

```bash
vim .gitignore
```
Chẳng hạn, ta tạo file với nội dung sau

```bash
# Ngưng theo dõi các file .bak, .exe, .bat, .sh và các file ẩn
*.bak
*.exe
*.bat
*.sh
.*
# Nhưng theo dõi các file sau: dynamo.sh, .dynamoconfig
!dynamo.sh
!.dynamoconfig
# Ngưng theo dõi các file README ở thư mục gốc, nhưng cái file README ở thư mục con vẫn theo dõi
/README
# Ngưng theo dõi thư mục dynamo
dynamo/
# Ngưng theo dõi các file .txt trong thư mục hasunoha, ngoại trừ trong thư mục con của nó
hasunoha/*.txt
# Ngưng theo dõi tất cả các file .txt trong thư mục trên
hasunoha/**/*.txt
