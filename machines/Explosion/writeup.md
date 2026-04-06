# Explosion

### The Scenario

### Process

> *Task 1:* **RDP (Remote Desktop Protocol)**, which is developed by Microsoft, use to control the remote devices by GUI.

> *Task 2:* **CLI (Command Line Interface)**

> *Task 3:* **GUI (Graphical User Interface)** 

> *Task 4:* TCP Port 23, which is used by **telnet**, the old remote access tool

> *Task 5:* Scan `nmap <TARGET IP> -p 3389 -sV` so we get `3389/tcp open  ms-wbt-server Microsoft Terminal Services`

> *Task 6:* Search about *xfreerdp*, and then i use it `xfreerdp3 -h` i find `/v:` use for host

> *Task 7:* Okay, ở đây tôi bắt đầu thấy có phần giống Meow, thay vì tìm hiểu default như mọi lần, tôi thử tìm hiểu về quyền hạn cao nhất của Windows là gì và dễ thấy nó là `Administrator`, và thử vào thì gặp vấn đề về việc không có chỗ nhập username, tôi thử `-h` và tìm thì thấy dùng option `/u:` để đưa username vào và thử thì được!!

Okay, tới đây dễ dàng lấy flag rồi, không nói nữa!! Qua extend tìm hiểu xem liệu việc dùng RDP có gây ra nguy hiểm gì trong thực tế hay không?

### Extend



### Summary

