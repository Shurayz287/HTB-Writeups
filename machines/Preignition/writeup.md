# Preignition


### The Scenario



### Process

> *Task 1:* Another name of Directory Brute-forcing techinque is **dir busting**, you can use to get request the web server with path and look for the respone signal to know what web page is exit.

> *Task 2:* Version detection option in nmap is `-sV`, i have used it in Fawn or Meow, ...

> *Task 3:* Use `nmap -p 80 <TARGET IP>` to get service in this port! `http` is the service in 80 port.

> *Task 4:* Run nmap with `-sV` for identify version name of service is `nginx 1.14.2`

> *Task 5:* Use `gobuster -h`, read the help menu and we can know dir command for directory!

> *Task 6:* Use `goubuster dir -h` and read the option `-x` this is the *file extension* specific to the file type!

> *Task 7:* Use `gobuster dir -u <TARGET IP> -w /usr/share/wordlists/dirb/small.txt -x php`, i have try to use without `-x php` and no result for me, so the specific file extension is so important to recon!

> *Task 8:* You can read the result, status code is 200, so we can go to admin.php

Now we have a admin console to login with admin account, i try to use `admin:admin` and successfull to login an get root account, but i think we should brute force to find the password with username is admin and get flag. 

### Extend


### Summary


