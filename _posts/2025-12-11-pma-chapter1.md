---
layout: post
title:  PMA Malware Analysis VM Setup
categories: malware
comments: true
excerpt: "Setting up a Windows XP Virtual Machine in 2025" 
---
<div>
<h2>Windows XP</h2>
<p>
The labs in the Practical Malware Analysis book are designed to be used with Windows XP, which turned out to be a bit of a struggle in 2025. After trying a few images, the easiest one to work with that I could find was from <a href="https://archive.org/details/xp51_20191108" target="_blank">https://archive.org/details/xp51_20191108</a>, which also did not require a product key. What a blast from the past! Now I just need to fire up Limewire and download some Staind or something to crank on Winamp.<br>
<img src="/images/lab1/xp.png">
</p>

<p>
Part of the reason I had so much trouble using Windows XP for these labs is that it only supports TLS 1.0, which makes it incompatible with many modern websites. Even trying to download a compatible browser proved impossible and there is no choice but to set up a shared folder for the VM. However, this problem introduced me to the <a href="http://kmeleonbrowser.org/" target="_blank">K-Meleon</a> browser, which is really cool!
</p>
</div>

<div>
<p>
OK, now I have PEView, Resource Hacker, and K-Meleon installed, time to get to work on Lab 1! Wait, the first step says to upload the samples to Virus Total, but that doesn't work. No problem, they made a website for older browsers! Nope, they put a Captcha on it, which doesn't work on old browsers &#129318;<br>
<img src="/images/lab1/virus-total.png">
</p>
</div>
<br><br>
<div>
<h2>Ubuntu</h2>
<p>
I already had an Ubuntu VM set up for bug bounty and CTFs prior to these labs, and it turned out to be useful for malware analysis as well. To set up this VM to receive network requests from the XP machine, I first installed INetSim then assigned a static IP address:<br>
<img src="/images/lab1/ubuntu-ip.png">
</p>
<p>
Then for both machines I selected Internal Network for the Virtualbox network adapter and made sure the network name was the same on both:<br>
<img src="/images/lab1/ubuntu-network.png">
</p>
<br><br>
<div>
<h2>Windows 10</h2>
<p>
When I reached Chapter 5, I had no choice but to create a VM with a newer operating system. There are no legacy versions of IDA Pro available and Ghidra is 64-bit only. For these cases, I installed a Windows 10 VM, which allowed me to browse modern websites including VirusTotal, install IDA Pro, and even install some modern PE analysis tools like PE Bear. Another upside to this is that once I'm done with the labs in PMA, I can analyze some more modern actual malware samples!
<img src="/images/lab1/vt-1.png">
</p>
</div>
<br><br>
<div>
<h2>Conclusion</h2>
<p>
I know this was a short write-up, but if anyone else is in the same situation this bit of information could save you hours of frustration. The best way to transfer anything is just use a shared folder. If you absolutely need a browser, K-Meleon is definitely the way to go. I like that you can toggle JS/cookies/images, etc. right on the toolbar. Some of the tech in Practical Malware Analysis is a bit dated, so it requires a second more modern VM, but it's been worth the extra effort because I'm learning a ton!
</p>
</div>