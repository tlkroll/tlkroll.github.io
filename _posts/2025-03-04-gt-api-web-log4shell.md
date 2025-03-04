---
layout: post
title:  API Security, Web Security, and Log4shell projects
categories: blog
comments: true
excerpt: "Web-based projects in 6035" 
---
Over the last few weeks I finished up these three web-based projects in 6035, which were a lot more fun than I was expecting. The API security project was actually fairly simple - I finally had a decent grasp of APIs after working on this site for a bit, but I learned a lot more about the structure of HTTP requests including headers, queries, authorization, cookies, etc., and I also learned a lot about JSON web tokens (JWTs). 

The web security project was the most fun of these three and almost as much fun as binary exploitation. This project was all about XSS and CSRF vulnerabilities. There's something very satisfying about using the input features of a website to change its content to something unintended. 

I just finished up the Log4shell project on Saturday, which explores the vulnerability in certain releases of Apache's Log4j logging utility where an attacker can execute remote code by entering a string which is logged by the software and executed, redirecting to an LDAP server, and finally redirecting to a server hosting a malicious Java file, which is downloaded and also executed. The actual string sent to the vulnerable server is simple, but the chain of events following that can be complex to follow. This project was a lot of work but also very rewarding and I actually finished it up a week early so that will give me some time to work on my 6725 paper. 

After completing these web hacking projects, I am a lot more confident in my knowledge and abilities concerning websites. If my 6725 paper is finished soon enough I plan to try my hand at some bug bounties. Right now I'm eyeballing Uber because it seems like there's a huge attack surface there where I could practice my newly-acquired web skills. 