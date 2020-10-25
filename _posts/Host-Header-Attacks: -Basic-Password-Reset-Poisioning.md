## Hi hackers,

If you ever worked with API's (Application Programming Interface) , you might aware of different type of Requests like GET, POST, PATCH, etc.  and already know that there are some headers required for a successful request. One of these headers is **HOST Header** which is mandatory header in modern INTERNET technologies. In this blog I will talk about HOST header and how can a server with wrong configuration of this header can easily be exploit by attackers in different ways.
Before that , lets discuss what HOST header is and why it is used?

**HOST** header is a mandatory request header which specifies the domain name that a user want to visit or access. For example when you visit the url https://gurus158.github.io/blogs/ your browser will create a request containing HOST Header as 

```
GET /blogs/ HTTP/1.1
Host: gurus158.github.io
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
```

In Classic Internet there was no need for Host header as one IP address corresponds to only one domain. But due to web-clouds and virtual hosting it become very common to host multiple domains in one server / one IP and  all domain may or may not be owned by single user. So it become mandatory to use Host headers to specify which domain one is requesting as single  IP address is now  mapped to more than one domains.

There are many other use cases like Routing Traffic via intermediary system , where websites are hosted on different back-end servers , but all traffic between the client and servers is routed through an intermediary system. Due to these use cases using HOST header is very neat solution.

Although HOST Header is very important for any request for success but most developers don't give attention for validating this header due to wrong assumption that headers are not user controllable. However any hacker can easily modify headers using proxy interrupting tools. 

I will discuss one exploit known as **Password Reset Poisoning** which arise when a server is wrongly configured or configured to default and don't validate HOST Header. I will be using lab offered by portSwigger to demonstrate the exploit.

The scenario of the attack is given as follow:

```The user `carlos` will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account.  You can access your own account with the following credentials: `wiener:peter`. Any emails sent to this account can be read via the email client on the exploit server.```

Basically, we have a [website](https://ac851f3e1e9e6df380b64d9300d7005c.web-security-academy.net/login) with user login system and also forgot password functionality as shown in image below 

![9.png](https://github.com/gurus158/blogs/blob/gh-pages/images/9.png?raw=true)

we can login using credential  `wiener:peter`  , however our goal is, login to  user `carlos` . Also we have access to a server , where we can check emails of user wiener, which is hosting [this](https://acf71f451ee46dc380a24d1f01df00ec.web-security-academy.net/) domain. So any request send to this domain can be monitored by us. 

The forgot password system ,shown in image below , work as -   it asked for username or email and send password reset link to email assigned with username or the email entered(if an account exist with the email or username entered) , a very common functionality in many websites.

![10.png](https://github.com/gurus158/blogs/blob/gh-pages/images/10.png?raw=true)

Before moving forward , first we have to check that the target website is vulnerable to HOST Header attack. For that i have setup my browser to burp proxy and  i can intercept any http or https request send by my browser. 

Now i will visit the target website in browser and let burp intercept the request. Request intercepted by burp look like below

![11.png](https://github.com/gurus158/blogs/blob/gh-pages/images/11.png?raw=true)

as you can see HOST header of the above request is ```acde1f7d1e86f2c880c815d400db00f9.web-security-academy.net``` . Now i will send this to the repeater and again make a request to target server but this time I will change the value of host header to ```ace61f961ea6f2788025157c018100fa.web-security-academy.net``` as shown below

![12.png](https://github.com/gurus158/blogs/blob/gh-pages/images/12.png?raw=true)

  In response we get 200 code and site is responding same as before although we have altered the **Host Header** . This **confirms** that the target site is vulnerable to HOST Header attacks. 

NOTE: Sometime simply changing value of Host header won't work and you will get 400 or "invalid host header" error , then there some other approaches, like duplicate host header or port poisoning ,  to find if target site is vulnerable to Host Header attacks and will have to proceed accordingly. 

Next , let ask server for reset password link for user  `weiner` (the one we own) and check what the request and reset link look likes

![13.png](https://github.com/gurus158/blogs/blob/gh-pages/images/13.png?raw=true)

the reset link is `https://acde1f7d1e86f2c880c815d400db00f9.web-security-academy.net/forgot-password?temp-forgot-password-token=WbShUbgz89PZto0CupeitFm0cRF8fiGr` and important thing here is **temp-forgot-password-token's value** . So now what we want is the value of this token when `carlos` password reset link is generated then we can simply visit the link and reset his/her password. 

For that i will send the forgot password request to repeater and make target server to send the reset link to our server by changing HOST header with our server domain value and parameter **user** to `carlos` instead of `wiener` in the request , shown below 

![14.png](https://github.com/gurus158/blogs/blob/gh-pages/images/14.png?raw=true)

as you can see server response with 200 code  and asking to check the mail. However , now we can simply check the generating token in logs of our server because we changed the host header  , as shown below. 

 ![15.png](https://github.com/gurus158/blogs/blob/gh-pages/images/15.png?raw=true)

after changing token value(from above image) in reset link , password reset link for carlos will become `https://acde1f7d1e86f2c880c815d400db00f9.web-security-academy.net/forgot-password?temp-forgot-password-token=68rXVzrBwUiD5NX9goF9237yrA4S0SKS` and simply visiting this we are able to reset the password for `carlos` , further steps shown in images below

 ![16.png](https://github.com/gurus158/blogs/blob/gh-pages/images/16.png?raw=true)

![17.png](https://github.com/gurus158/blogs/blob/gh-pages/images/17.png?raw=true)

![18.png](https://github.com/gurus158/blogs/blob/gh-pages/images/18.png?raw=true)



#### This was the demonstration of basic **Password Reset Poisoning** by exploiting HOST header attacks. There are more attacks that can be done by exploiting HOST headers. Stay tunned for next exploit.

#### Till then be SAFE !!   

