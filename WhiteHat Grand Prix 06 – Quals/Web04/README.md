# WEB 04 - VIETNAMESE FOOD Challenge 
Challenge: http://15.165.89.5/ \
Backup: http://15.165.89.5:4567, http://15.165.89.5:4568, http://15.165.89.5:4569 \
*Docker images available here: https://hub.docker.com/repository/docker/haonh/wh/* 
 
   
# ================================== 
 
However, we have not seen any function that uses this token, it is likely to be opened when it has admin rights.
* Go to website of challenge we will see: 
![alt text](https://i.ibb.co/qjs63kg/Index.png "Index website")
Website using express framework - nodejs. Have funtions home, service, contact and login.
* We view source code website and find interesting is like user of website is test/test Use user we got to login website and get two information *You don’t have permission* and token of users test like base 64. However, we have not seen any function that uses this token, it is likely to be opened when we have user admin.
![alt text](https://i.ibb.co/F3v3Sdv/logintest.png "Login test")
* Test user does not have access privileges. So we need to find a user with admin privileges to solve this challenge. Website using nodejs so databases backend may be mongodb. We found nosql injection vuln in login funtion ( https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection#tools )
![alt text](https://i.ibb.co/BrwX5CR/burploginnosqli.png "Login test")
We can bypass login using vuln nosql injection. Opening the way to solve the challenge is to use nosql injection error to dump the data of all users and find the user who has admin privileges by use $regex.

* After dump user we found user *itachi* has admin privileges. Using this user to log in to the website via bypassing the login will open the checktoken function.

Back to the token user test we have. That is the base64 string using tool decode we have the information:
![alt text](https://i.ibb.co/kB4ZzBx/token.png "Token")
It is the json string containing the user's session information. This string seems to be dumped from the user's session.

The token chain is dumped from the user's session and then converted back. In nodejs, there are serialize and unserialize functions that support this. So we need check  exploit Node.js deserialization bug. Readmore https://www.exploit-db.com/docs/english/41289-exploiting-node.js-deserialization-bug-for-remote-code-execution.pdf
File exploit.
