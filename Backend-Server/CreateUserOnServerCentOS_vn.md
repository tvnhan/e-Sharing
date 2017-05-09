# Hướng dẫn tạo một user trên server amazon và kết nối thông qua ssh key

** Bài viết được minh hoạ trên máy client MacOS 10.11.6 và  server VPS dịch vụ của amazon
## 1. Những thông tin cần phải lưu ý:
* [server_ip]: ip của server amazon
* [Public DNS]: DNS public của VPS server của bạn
* [user_name_ec2]: user_name mặc định ban đầu mà amazon cung cấp cho bạn, ở đây thông thường sẽ là ec2-user (Với CentOS hoặc Linux)
* [pem_key]: tập tin .pem này là key dùng để xác thực lúc bạn kết nối tới server thông qua [user_name_ec2]
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
