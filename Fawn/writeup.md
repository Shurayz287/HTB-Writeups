# Fawn


### The Scenario
**Nhiệm vụ:** Kiểm tra server.  
**Mục tiêu:** Máy chủ là "Fawn".   
**Cốt truyện:** Máy chũ củ này hiện không biết có nhiệm vụ gì, công ty tắt thì sợ ảnh hưởng đến hệ thống, nhiệm vụ của bạn là đi kiểm tra xem tính năng nó làm gì và có vấn đề gì hay không để giúp công ty nâng cấp hệ thống cơ sở hạ tầng, và cũng không có thông tin gì về con máy chủ đó từ công ty!   

### Process

> *Task 1:* FTP stand for **File Transfer Protocol**, the Protocol for transfer file data.

> *Task 2:* FTP service usually use **Port 21** 

> *Task 3:* The later protocol usually make more **Secure**, in FTP is **SFTP**, in HTTP is **HTTPS** with S stand for **Secure**.

> *Task 4:* **ping** use for ICMP echo request, testing now `ping <TARGET IP>`, the server is working for we testing.

> *Task 5:* Scan with nmap now!! `nmap <TARGET IP> --top-ports 100` okay kết quả là có `21/tcp open  ftp`, để tìm được version, ta dùng option `-sV` để tìm được version của service ftp này! Scan with `nmap <TARGE IP> -p 21 -sV` nhận kết quả `vsftpd 3.0.3`

> *Task 6:* The result after scan info us `Service Info: OS: Unix` 

> *Task 7:* The tool to use service ftp is ftp, we can use option `-h` to display help menu, so the command we see is `ftp -?`, the `-h` is not a option of ftp command!!

> *Task 8:* Quay lại bối cảnh ban đầu, giờ chúng ta đã biết server này có service dùng là FTP, khả năng cao để chứa file trên server hoặc share file giữa nhiều phòng ban trong công ty, tất nhiên trong thực tế sẽ phải scan hết tất cả các port!!! 

Giờ mục tiêu là thử vào server kia để xem sao, để kết nối bằng FTP thì cần ftp tool, port và  host đã có! `ftp <TARGET IP> 21` vào và yêu cầu login, chúng ta cần tìm hiểu thêm về ftp để biết default hoặc admin có username và password là gì? Đọc thêm ở đây [FTP](https://www.geeksforgeeks.org/computer-science-fundamentals/file-transfer-protocol-ftp/).  

Đọc 1 hồi thì thấy mục **Anonymous FTP** có nói có thể username là `anonymous`, riêng password thì vẫn còn là dấu hỏi nhưng có thể thử với rỗng xem sao.

Giờ chúng ta đã kết nối được với server, nếu chưa quá rõ về ftp thì có thể gõ help để biết ftp có commend gì, và tìm hiểu thêm nhé!

> *Task 9:* Dễ thấy, login và successful thì thường là **230**, và login thành công cũng nhận được tín hiệu vậy!

> *Task 10:* Try `help` to know command can use in ftp, you can see `ls` this is very famous command ussually to list content.

Now we play with the FTP server!!  Bạn có thể dùng `get` command và kéo file từ server về local, từ đó lấy flag, nhưng như vậy thì quá buồn chán, chúng ta thử lòng vòng quanh server xem sao!!! 

### Extend

Lòng vòng trên server 1 hồi, tôi không tìm được gì quá hay ho!!! Tôi quyết định đổi hướng!!!

Tôi có tìm kiếm 1 vòng về `vsftpd 3.0.3 vulnerabilities` và thấy 1 code DoS trên exploit database, tôi vào thử và config lại code, chỉnh xuống chỉ 5 thread để đảm bảo an toàn, lưu ý là cho dù bạn join HTB Labs VIP+ với 1 machine riêng cũng không nên thử tạo quá nheieuf request 1 lúc, điều này có ảnh hưởng trực tiếp tới server HTB và có thể bị coi là cố tình tấn công!!! Okay tôi thử với 5 request bằng code python và sau đó vào lại ftp của server thì nhận được thông báo `421 There are too many connections from your internet address.
` tức đã DoS thành công!!! Lưu ý đây chỉ là các test vui vẻ, và tôi có Labs VIP+ nên machine này chỉ có mỗi mình tôi vào, chính vì vậy nên tôi không gây ảnh hưởng tới ai và tôi cũng chỉ giới hạn 5 request nhỏ để xem thử!! Còn trong bài lab mình sẽ config và có mẹo làm thử mà ko cần code python để DoS nhé! 


### Summary

Qua Lab Fawn này tôi biết thêm về FTP server, đọc kết quả *Scan* và phân tích, tìm tư liệu trên mạng để thử, học tư duy thứ sai, tìm hiểu thêm về lỗ hổng khi biết version, biết cách thức chính xác khi DoS 1 server!! 

