# Đặt vấn đề 
- <img src="https://i.imgur.com/PoT02k9.jpg">
- Trên server A cài webserver, ở đây sẽ dùng Nginx, yêu cầu sẽ là đứng từ server B để tải file (dùng wget) để download một 
file từ server A về với max speed là 2MB/s
# Giải quyết
- Đầu tiền ta sẽ cài đặt webserver 
  + **yum install nginx -y**
  + Sau đó start : **systemctl restart nginx**
  + Tạo path : **/urs/share/nginx/html/download/file_download** (ở đây tôi sẽ dùng VMware-Workstation-Full-15.5.0-14665864.x86_64.bundle)
  + Sau đó vào path : **/urs/share/nginx/html/index.html** 
  để tạo một thẻ **href** để add /download/VMware-Workstation-Full-15.5.0-14665864.x86_64.bundle
    => <img src="https://i.imgur.com/ZE4lu9g.png">
    => <img src="https://i.imgur.com/zEKiRhX.png">
- Sau đó sẽ vào trình duyệt để test
  + http://trongnd.vcc.com (add địa chỉ server vào file vim /etc/hosts)
  + <img src="https://i.imgur.com/GbRDr2r.png">
- Vậy làm sao để limit bandwidth đây?
  + Vào **vim /etc/nginx/nginx.conf**
  + <img src="https://i.imgur.com/57cIQ71.png">
    ==> <img src="https://i.imgur.com/S3EdtXf.png">
  + Điều này có nghĩa là : nếu request của client có /download thì nginx sẽ redirect đến context location /download và sẽ 
  bị chi phối bới các directives trong này, ở đây có directives: **limit_rate 2024k;** => giới hạn bandwidth của người dùng khi download x
  + Sau đó ta sẽ download file trong thư mục download bằng wget
  + <img src="https://i.imgur.com/k6X9aNh.png">
- Kết quả là : 
  + <img src="https://i.imgur.com/EqtRUcs.png">

### Như vậy là đã giải quyết xong vấn đề rồi
