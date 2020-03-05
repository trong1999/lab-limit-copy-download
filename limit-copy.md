# Đặt vấn đề
- <img src="https://i.imgur.com/PoT02k9.jpg">
  + Đứng từ server B scp file từ server A với max speed là 1MB/s
- Ta sẽ thực hiện như sau: 
  + Để làm được điều này thì người quản trị sẽ đứng ở thiết bị mạng router để configure, vậy làm bằng cách nào ?
  + Ở đây chúng ta sẽ dùng Traffic Controll (TC) để giải quyết vấn đề này.
- Đầu tiên ta sẽ tạo một file script
  + **vim /etc/init.d/shaping**
  + Nội dung file như sau:
  + <img src="https://i.imgur.com/H4qW5bS.png">
  + <img src="https://i.imgur.com/hR10j03.png">
  + Save and exit
  + Phân quyền: chmod +x /etc/init.d/shaping
  + Start : /etc/init.d/shaping
- Kết quả sẽ là : 
  + <img src="https://i.imgur.com/LINYo2L.png">
## Như vậy là ok 
