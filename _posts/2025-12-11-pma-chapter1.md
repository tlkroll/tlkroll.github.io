---
layout: post
title:  PMA Malware Analysis VM Setup
categories: malware
comments: true
excerpt: "Setting up a Windows XP Virtual Machine in 2025" 
---
<p>
The labs in the Practical Malware Analysis book are designed to be used with Windows XP, which turned out to be a bit of a struggle. After trying a few images, the easiest one to work with that I could find was from <a href="https://archive.org/details/xp51_20191108" target="_blank">https://archive.org/details/xp51_20191108</a>, which also did not require a product key. What a blast from the past! Now I just need to fire up Limewire and download some Staind or something to crank on Winamp.<br>
<img src="/images/lab1/xp.png">
</p>

<p>
Part of the reason I had so much trouble using Windows XP for these labs is that the encryption schemes used on the web today are not recognized by it and many websites cannot be viewed. Even trying to download a compatible browser proved impossible and there is no choice but to set up a shared folder for the VM. However, this problem introduced me to the <a href="http://kmeleonbrowser.org/" target="_blank">K-Meleon</a> browser, which is really cool!
</p>

<p>
OK, now I have PEView, Resource Hacker, and K-Meleon installed, time to get to work on Lab 1! Wait, the first step says to upload the samples to Virus Total, but that doesn't work. No problem, they made a website for older browsers! Nope, they put a Captcha on it, which doesn't work on old browsers &#129318;<br>
<img src="/images/lab1/virus-total.png">
</p>

<p>
I know this was a short write-up, but if anyone else is in the same situation this bit of information could save you hours of frustration. The best way to transfer anything is just use a shared folder. If you absolutely need a browser, K-Meleon is definitely the way to go. I like that you can toggle JS/cookies/images, etc. right on the toolbar.
</p>