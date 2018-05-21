# Hướng dẫn Migrate data từ MongoDb (VPS-EC2) sang DynamoDb (sử dụng với Lambda)
#### <p align="center" style="color:red;"> <b> (TccHtnn - May 21st 2018) </b> </p>
#  

** Bài viết được minh hoạ trên:
  máy client MacOS 10.11.6 
  server VPS dịch vụ của amazon
  MongoDB shell version: 3.2.11-3.1
  DynamoDB dịch vụ amazon 
## 1. Những thông tin cần phải lưu ý:
* Trong bài viết này trong những câu lệnh, những phần được đặt trong {} thì bạn phải thay thế phù hợp với dữ liệu tương ứng của bạn.
* Trong phần ví dụ, câu lệnh thực hiện mẫu sẽ được gạch chân bên dưới với hai màu xanh dương (blue) và xanh lá (green): <br/>
*** xanh dương: phần cụm từ cú pháp, bạn không được phép thay đổi <br/>
*** xanh lá cây: phần thông số bạn có thể hoặc phải thay đổi tương ứng với trường hợp của bạn

## 2. Các bước thực hiện

### 2.1. Kết nối tới server thông qua tài khoản [user_name_ec2]
#### a) Lưu tập tin key .pem vào một thư mục bất kỳ trên máy
#### b) Di chuyển đến thư mục chứa tập tin .pem thông qua câu lệnh cd
```
cd {path/to/folder}
```
#### c) Cấp quyền truy cập cho tập tin .pem thông qua câu lệnh
```
chmod 400 {file_pem}
```
Ví dụ:
[![https://gyazo.com/187ef1f344e9b55a2f7646f81e2a52d6](https://i.gyazo.com/187ef1f344e9b55a2f7646f81e2a52d6.png)](https://gyazo.com/187ef1f344e9b55a2f7646f81e2a52d6)
#### d) Trong cùng thư mục chứa file .pem, ta thực hiện câu lệnh sau để kết nối tới server
```
ssh -i "{file_pem_name}" {user_name_ec2}@{publish_DNS} [-p {port}]
```
hoặc bạn cũng có thể thay đổi bằng địa chỉ ip server của bạn. 
```
ssh -i {file_pem_name} {user_name_ec2}@{publish_DNS} [-p {port}]
```
Ở đây nếu bạn không điền thông tin port thì việc kết nối sẽ thông qua port mặc định (22).  <br/>
Ví dụ kết nối qua địa chỉ ip với port mặc định:
[![https://gyazo.com/3dafa6c20d5bca21a3d0cec9fc5e3e34](https://i.gyazo.com/3dafa6c20d5bca21a3d0cec9fc5e3e34.png)](https://gyazo.com/3dafa6c20d5bca21a3d0cec9fc5e3e34)

Như vậy tới đây là bạn đã kết nối thành công tới server. <br/>
Bước tiếp theo, chúng ta sẽ tạo một tài khoản user mới trên server.

### 2.2 Tạo thêm một tài khoản new_user 
#### a) Chuyển sang quyền ```root``` <br/>
Câu lệnh:
```
sudo su
```
Ví dụ: <br/>
[![https://gyazo.com/f2be8321d21e648a844f5aa78242613c](https://i.gyazo.com/f2be8321d21e648a844f5aa78242613c.png)](https://gyazo.com/f2be8321d21e648a844f5aa78242613c)

#### b) Tạo và thêm user mới vào hệ thống
Ở đây ta sẽ tiến hành tạo và thêm một user vào hệ thống thông qua tên username. <br/>
Câu lệnh:
```
adduser {username}
```
Ví dụ ta sẽ tạo một user mới có tên định danh là ```tcchtnn```
[![https://gyazo.com/633e41de7780a39cd6f5a29c4bf686fb](https://i.gyazo.com/633e41de7780a39cd6f5a29c4bf686fb.png)](https://gyazo.com/633e41de7780a39cd6f5a29c4bf686fb)
#### c) Cài đặt password cho tài khoản vừa tạo
Câu lệnh: 
```
passwd {username}
``` 
Sau đó bạn nhập vào new password và confirm lại một lần nữa. <br/>
Ví dụ:
[![https://gyazo.com/44c793cf32a7129b88c82aed7da48a9a](https://i.gyazo.com/44c793cf32a7129b88c82aed7da48a9a.png)](https://gyazo.com/44c793cf32a7129b88c82aed7da48a9a)

#### d) Cấp quyền cho user vừa mới tạo vào ```wheel``` group
Câu lệnh:
```
usermod -aG wheel {username}
```
Theo chế độ mặc định trong CentOS, group ```wheel``` sẽ được cấp quyền ```sudo```
Ví dụ:
[![https://gyazo.com/de81f48f852da4a419abcd039d77d700](https://i.gyazo.com/de81f48f852da4a419abcd039d77d700.png)](https://gyazo.com/de81f48f852da4a419abcd039d77d700)

### 2.3 Tiến hành kết nối ssh key

#### a) Tạo ra cặp public key và private key ở client
Để tiến hành kết nối qua ssh key, bạn cần phải tạo một cặp public key và private key, sau đó lưu lại vào trong thư mục mặc định ```~/.ssh/```. <br/>
Ở đây, mình đã tạo ra một cặp key:
* public key: ```~/.ssh/id_rsa.pub```
* private key: ```~/.ssh/id_rsa``` <br/>

Nếu bạn là admin của server và muốn thêm một người vào hệ thống, bạn cần yêu cầu họ cung cấp file public key. <br/>
Lưu ý: File private key là thông tin bí mật của bạn nên không nên chia sẻ nó với bất kỳ một người nào khác.

#### b) Tạo thư mục ```.shh``` và file ```authorized_keys```
Sau khi bạn tạo một account, hệ thống sẽ tạo ra một folder có tên là username tương ứng trên thư mục ```home``` trên server. <br/>
Bạn chỉ quyền truy cập vào thư mục tương ứng của mình trên server, những thư mục khác thì bạn sẽ không có quyền truy cập hay sửa đổi. <br/>
Khi bạn thực hiện một kết nối ssh tới server, trong quá trình xác thực, server sẽ vào thư mục ```.ssh``` và đọc public key có trong file ```authorized_keys``` để kiểm tra với private key có trong máy tính client của bạn. <br/>
Bạn thực hiện thao tác này bạn cần ở quyền ```new_user```<br/>
Sau khi kết nối vào server với quyền ```ec2-user```, bạn cần chuyển sang quyền của ```new_user```. <br/>
Để làm việc này bạn cần phải chuyển qua quyền ```root``` sau đó chuyển sang quyền ```new_user```.
Câu lệnh:
```
sudo su
su {username}
```
Ví dụ:
[![https://gyazo.com/435cdff32bfecbda7479addfa24cfa00](https://i.gyazo.com/435cdff32bfecbda7479addfa24cfa00.png)](https://gyazo.com/435cdff32bfecbda7479addfa24cfa00)

Câu lệnh di chuyển đến thư mục của user:
```
cd /home/{username}
```
Câu lệnh tạo thư mục ```.ssh```:
```
mkdir ./.ssh/
```
Thư mục ```.ssh``` là một thư mục ẩn nên khi bạn dùng câu lệnh ```ls``` bạn không thể thấy, nhưng bạn vẫn có thể di chuyển đến thư mục đó. <br/>
Câu lệnh: 
```
cd ./.ssh/
```
Câu lệnh tạo ra file ```authorized_keys```:
```
touch authorized_keys
```
Ví dụ:
[![https://gyazo.com/92e3768452f9d998858d515534ce354c](https://i.gyazo.com/92e3768452f9d998858d515534ce354c.png)](https://gyazo.com/92e3768452f9d998858d515534ce354c)

#### c) Cấp quyền truy cập cho user vào thư mục ```.ssh``` và file ```authorized_keys```
Ở thư mục ```{username}```, bạn cấp quyền cho thư mục ```.ssh``` qua câu lệnh:
```
chmod 700 ~/.ssh
```
Bạn cấp quyền cho tập tin ```authorized_keys``` qua câu lệnh:
```
chmod 600 ~/.ssh/authorized_keys
```
Ví dụ:
[![https://gyazo.com/1fc6aac63d4f2f1d2188289719d80b16](https://i.gyazo.com/1fc6aac63d4f2f1d2188289719d80b16.png)](https://gyazo.com/1fc6aac63d4f2f1d2188289719d80b16)

#### d) Copy chuỗi public key của ```new_user``` vào trong file ```authorized_keys```
Ở trong ví dụ này, public key của new user ```tcchtnn``` được lưu trong tâp tin ```~/.ssh/id_rsa.pub```. Ta sẽ mở tập tin này dưới một trình đọc file txt và copy tất cả nội dung ở bên trong vào trong file ```authorized_keys``` <br/>
Ví dụ:
![](https://drive.google.com/uc?export=download&id=0B1TMTnucJTIPQ3NvUnRXWFRPZXc)

## DONE

Từ giờ bạn có thể connect tới server thông qua user vừa mới tạo.
Câu lệnh:
```
ssh {username}@{ip}
```
[![https://gyazo.com/5f56c58494fb7990e02a6fe17f031145](https://i.gyazo.com/5f56c58494fb7990e02a6fe17f031145.png)](https://gyazo.com/5f56c58494fb7990e02a6fe17f031145)

