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
  bị chi phối bới các directives trong này, ở đây có directives: **limit_rate 2024k;** => giới hạn bandwidth của người dùng khi download xuống tối đa speed là 2024k;
  
  + Sau đó ta sẽ download file trong thư mục download bằng wget
  + <img src="https://i.imgur.com/k6X9aNh.png">
- Kết quả là : 
  + <img src="https://i.imgur.com/EqtRUcs.png">
# Các giải quyết vấn đề trên là khi người quản trị đứng ở vị trí quản trị website service
# Còn bây giờ chúng ta sẽ giải quyết vấn đề khi chúng ta đứng ở vị trí quản trị thiết bị mạng, cụ thể ở đây là router 
- Đầu tiên ta sẽ cài đặt iptables
  + **yum install iptables-services**
  + **service start iptables**
  + **service enable iptables**
- Sau đó sẽ xóa tất cả các rules của interface ens38
  + **/sbin/tc qdisc del dev ens38 root**
- Turn on quy tắc hàng đợt
  + **/sbin/tc qdisc add dev ens38 root handle 1:0 htb default 10**
==> **qdisc** là khối mà ở đó tất cả các traffic control được tạo lập
- Tiếp theo chúng ta sẽ định nghĩa một class để giới hạn bandwidth của service http là 2024kbps
và burst bandwidth là 4048kbps; burst bandwidth ở đây là gì ? Nó như là lượng băng thông dự phòng, để giải quyết vấn đề trong
thời gian cao điểm hoặc để giải quyết vấn đề khi lượng băng thông đột nhiên tăng vọt lên :
  + **/sbin/tc class add dev ens38 parent 1:0 classid 1:10 htb rate 2024kbps ceil 4048kbps prio 0**
  + prio 0 : set mức độ ưu tiên, prio càng thấp thì độ ưu tiên càng cao.
- Vì TC không hỗ trợ trực tiếp việc limit bandwidth http, nên ta phải dựa vào iptables làm phụ trợ, mục đích là để mark class
  + **/sbin/iptables -A OUTPUT -t mangle -p tcp --sport 80 -j MARK --set-mark 10**
- Cuối cùng là áp nó vào qdisc
  + **tc filter add dev ens38 parent 1:0 prio 0 protocol ip handle 10 fw flowid 1:10**
- Toàn bộ cấu hình 
  + <img src="https://i.imgur.com/6qC15yi.png">
- Và kết quả sẽ là
  + <img src="https://i.imgur.com/40qJi3b.png">
### Như vậy là đã giải quyết xong vấn đề rồi
