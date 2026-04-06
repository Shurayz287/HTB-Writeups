# OpenSecret

> ## Very Easy

### 1. Overview 

After visiting the web page, I see the form, I try a simple request.

![](./img/image01.png)

The result is `No session token provided`. I open the network tab in devtool and send a new request. When I send click `Submit Ticket`, a request `submit-ticket` is called, you can read a source code of this page to know how to call this request?

![](./img/image02.png)
*Devtool in Network tab*

![](./img/image03.png)
*Source code*

When reading the source code, I focused on `SECRET_KEY` variable, I think it could be the flag. 

![](./img/image04.png)

More than just finding the flag, I want to read and analyze the source code.  

### 2. Enumeration

`generateJWT` function, which genarates cookie `session_token` with header, data and sign. You can read this source code to understand its behavior. 

![](./img/image05.png) 

The way it creates JWT token is very basic. But remember, you have `SECRET_KEY` and you can create your JWT token however you like. To make it simpler, you can use [jwt.io](https://www.jwt.io/) web site. 

Looking back, we received `No session token provided` when we were sending the `submit-ticket` request. The reason is that no cookies, or no `session_token` were sent, so the server didn't recvice `session_token` and your request wasn't processed.

But when I looked in the Application tag of devtool, in Cookies, this is empty, no cookies were set. Going back the source code, I see the error in `importKey` and in Console tag, I seen a error, which prevented the cookies from being set.

![](./img/image08.png)  
*Error in `importKey`*

![](./img/image07.png) 
*Error in Console tag*

### 3. Exploitation

Because we can create a `session_token`, so I have to create one, with header, data and secret key with following the source code. 

![](./img/image09.png)

Now, I am going to try a request with `session_token` with curl!

```bash
curl http://154.57.164.82:31597/submit-ticket -X POST -H 'Content-Type: application/json' -H 'Cookie: session_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6Imd1ZXN0XzEyMyJ9.yw5dHott7zRZ7_i01D550TexnpRrF3wKl9FUvnbK8GY; path=/; max-age=86400' --data-raw '{"name":"Shurayz287", "description": "I am a hacker!"}'
{"message":"Ticket submitted successfully"}
```

Yeah, ticket is submitted!! But, what we can do with it? Yeah, I don't know! If the challenge only need to read a source code, why does its author make a JWT in this challenge? I try some page such as, `/admin`, `/login`, and `/ticket`, `/tickets`. `/tickets` is an API that exists on the server! 

![](./img/image10.png)

Next, I added a request for a `session_token` cookie! The truth is you don't need to use `curl`  command to add `session_token` cookie, you can use Devtool! Going to Application tag, click Cookie, click to current web page url, double click under the `Name` column and type `session_token` to add the name of new cookie, and the `Value` column to add the value of new cookie!

![](./img/image11.png)   
*Adding Name of new Cookie*

![](./img/image12.png)   
*New Cookie is added*

Now, trying to go `/tickets` page! Ohh, the new result is `Access denied. Admin privileges required.`!

![](./img/image13.png)

I remember that I use `username` value in JWT token is `guest_123`, so I think it needs `admin`, I create new token with `"username": "admin"`. After editing `session_token` cookie in Application tag, I try assessing the website again! **Boom!!!** All the tickets have been leaked!

![](./img/image14.png) 
*All tickets*

In my opinion, perhaps the author's original intention was to allow players to create a admin token using the given secret key, find the `/tickets` API and get flag with the leaked tickets!

### 4. Root Cause

The vulnerability leaked all tickets have two reason. First, secret key have been exposed in frontend code, which is **Sensitive Data Exposure**. Second, although `/tickets` API have security check and only admin can read, but secret key have been leaked and hacker can use this to become a admin with web server! And `/tickets` should be called only by localhost of server and check with remote address! 
