---
layout: post
title:  Malware Samples - PMA Chapter 3
categories: malware
comments: true
excerpt: "Four malware samples from the third chapter of Practical Malware Analysis" 
---
<p>
The second chapter of Practical Malware Analysis covers virtual machine setup in preparation for this chapter and had no labs. The third chapter covers the basics of dynamic analysis and how to inspect processes, registry entries, and network traffic associated with a sample. Before running any malware for dynamic analysis, I will take a snapshot of the VM state. Even if everything appears normal when analysis is complete, I will restore to that state because there is always the chance that something was missed. 
</p>
<p>
This chapter required a lot of additional setup. First, I had to set up INetSim on my Ubuntu VM to simulate services on common ports, then set up this VM and the Windows XP VM to share a virtualized internal network and assign static IP addresses. Then I set up ApateDNS to re-direct all requests to the Ubuntu VM. Now INetSim will spoof a variety of network services (pop3, http/https, dns, ftp, etc.):<br>
<img src="/images/lab3/inetsim.png">
</p>

<h2>Samples:</h2>
<ol>
<a href="#sample-1"><li>Malware Sample 1</li></a>
<a href="#sample-2"><li>Malware Sample 2</li></a>
<a href="#sample-3"><li>Malware Sample 3</li></a>
<a href="#sample-4"><li>Malware Sample 4</li></a>
</ol>

<div id="sample-1" class="section-link">
<h2>Malware Sample 1</h2>
<p>
This is my first dynamic analysis sample and the process was much more in-depth than the dynamic analysis I performed in my malware class.
</p>
</div>

<h2>Questions:</h2>
<ol>
<a href="#1-1"><li>What are this malware's imports and strings?</li></a>
<a href="#1-2"><li>What are the malware's host-based indicators?</li></a>
<a href="#1-3"><li>Are there any useful network-based signatures for this malware?</li></a>
</ol>

<div id="1-1" class="section-link">
<h2>What are this malware's imports and strings?</h2>
<p>
Although the section sizes don't indicate packing, the lack of imports shown in PEView does:<br>
<img src="/images/lab3/imports.png">
</p>
<p>
A quick check in PEiD confirms this:<br>
<img src="/images/lab3/peid.png">
</p>
<p>
I couldn't find any software to unpack this and this is supposed to be a dynamic analysis lab anyway, so I'll continue to check strings using Microsoft's <b>strings.exe</b>:<br>
<img src="/images/lab3/strings.png"><br>
Along with a URL and some registry paths, it also looks like some additional imports are visible here.
</p>
<p>
This was as far as I could get with static analysis, so I set up Process Explorer, procmon, ApateDNS, Wireshark, INetSim, and fired off the sample. Process Explorer showed a lot more imports than were visible with static analysis:<br>
<img src="/images/lab3/imports-2.png">
</p>
<p>
Also, looking at the handles in Process Explorer reveals a mutex that was created:<br>
<img src="/images/lab3/mutex.png">
</p>
</div>

<div id="1-2" class="section-link">
<h2>What are the malware's host-based indicators?</h2>
<p>
Before running the sample, I took a snapshot of the registry with Regshot and another afterwards. Comparing the two shows a lot of activity in the registry:<br>
<img src="/images/lab3/regshot.png">
</p>
<p>
This line was particularly interesting because it adds something to run on startup:<br>
<img src="/images/lab3/startup.png">
</p>
<p>
The hex string could be decoded, but it was easier to just view the properties of this operation in procmon:<br>
<img src="/images/lab3/service.png"><br>
Here's that mutex from Process Explorer!
</p>
<p>
Filtering on the RegSetValue and WriteFile operations in procmon shows these results:<br>
<img src="/images/lab3/filtered.png">
</p>
<p>
I was curious if it started this process at runtime, so I restarted the VM to check, and there it is!<br>
<img src="/images/lab3/restart.png">
</p>
<p>
Host-based indicators for this sample would be any reference to <b>vmx32to64.exe</b>.
</p>
</div>

<div id="1-3" class="section-link">
<h2>Are there any useful network-based signatures for this malware?</h2>
<p>
I had three network utilities (ApateDNS, INetSim, Wireshark) set up to analyze this sample. All showed the same results, but ApateDNS was the easiest to monitor:<br>
<img src="/images/lab3/apate.png">
Every 30 seconds this sample sent requests to <b>www.practicalmalwareanalysis.com</b>, which would be the only network-based indicator.
</p>
<p>
Wireshark showed that these were SSL requests on port 443.
</p>

<div id="sample-2" class="section-link">
<h2>Malware Sample 2</h2>
<p>
This sample is only a DLL, but when installed it creates a malicious service.
</p>
</div>

<h2>Questions:</h2>
<ol>
<a href="#2-1"><li>How can you get this malware to install itself?</li></a>
<a href="#2-2"><li>How would you get this malware to run after installation?</li></a>
<a href="#2-3"><li>How can you find the process under which this malware is running?</li></a>
<a href="#2-4"><li>Which filters could you set in order to use procmon to glean information?</li></a>
<a href="#2-5"><li>What are the malware's host-based indicators?</li></a>
<a href="#2-6"><li>Are there any useful network-based signatures for this malware?</li></a>
</ol>

<div id="2-1" class="section-link">
<h2>How can you get this malware to install itself?</h2>
<p>
Looking at the exports for this DLL in PEView, there are two install functions defined:<br>
<img src="/images/lab3/exports.png">
</p>
<p>
After setting up all dynamic analysis tools, I will try the first <b>Install</b> function using <b>rundll32.exe</b>:<br>
<img src="/images/lab3/install.png">
</p>
<p>
A comparison of before and after registry snapshots with RegShot revealed nothing interesting, so I tried again with <b>installA</b>. Now there's something interesting; a newly-installed service named <b>IPRIP</b>:<br>
<img src="/images/lab3/iprip.png">
</p>
</div>

<div id="2-2" class="section-link">
<h2>How would you get this malware to run after installation?</h2>
<p>
To run this malware, the new service will have to be started:<br>
<img src="/images/lab3/start-service.png">
</p>
</div>

<div id="2-3" class="section-link">
<h2>How can you find the process under which this malware is running?</h2>
<p>
Now that the new service is running, the process using it can be identified by searching for the malicious DLL in Process Explorer:<br>
<img src="/images/lab3/dll-search.png">
</p>
</div>

<div id="2-4" class="section-link">
<h2>Which filters could you set in order to use procmon to glean information?</h2>
<p>
There are many instances of svchost.exe running, so the PID of 1116 will need to be used as a filter for procmon. This was a busy process and after a lot of scrolling and applying different operation filters, its main local functionality seemed to be reading system/network settings and writing to log files.
</p>
</div>

<div id="2-5" class="section-link">
<h2>What are the malware's host-based indicators?</h2>
<p>
The main host-based indicators for this sample would be registry entries for the malicious DLL and the IPRIP service it installs.
</p>
</div>

<div id="2-6" class="section-link">
<h2>Are there any useful network-based signatures for this malware?</h2>
<p>
Starting the IPRIP service revealed several network-based indicators. First, a backup list request is made on the local network to identify targets:<br>
<img src="/images/lab3/backups.png">
</p>
<p>
Next, a request was made to <b>practicalmalwareanalysis.com</b>:<br>
<img src="/images/lab3/apate-2.png">
</p>
<p>
Checking the log details from INetSim on the Ubuntu VM shows a GET request for the file <b>serve.html</b> and INetSim sent back a fake .html file (what a cool tool!):<br>
<img src="/images/lab3/get.png">
</p>
<p>
The main network-based indicator for this sample would be URL the GET request was sent to and in the event that this malware were real, any files requested.
</p>
</div>

<div id="sample-3" class="section-link">
<h2>Malware Sample 3</h2>
<p>
The third sample only requires host-based analysis and does some interesting tricks with process manipulation.
</p>
</div>

<h2>Questions:</h2>
<ol>
<a href="#3-1"><li>What do you notice when monitoring this malware with Process Explorer?</li></a>
<a href="#3-2"><li>Can you identify any live memory modifications?</li></a>
<a href="#3-3"><li>What are the malware's host-based indicators?</li></a>
<a href="#3-4"><li>What is the purpose of this program?</li></a>
</ol>

<div id="3-1" class="section-link">
<h2>What do you notice when monitoring this malware with Process Explorer?</h2>
<p>
Without this question, I probably would have had to run this a few times to notice, but if you watch Process Explorer when running this sample, the Lab03-03.exe process begins, spawns a svchost.exe process, then the Lab03-03.exe process terminates. This happens too quickly to capture a screenshot, but here are the running processes afterwards:<br>
<img src="/images/lab3/processes-1.png">
</p>
</div>

<div id="3-2" class="section-link">
<h2>Can you identify any live memory modifications?</h2>
<p>
In a normal svchost.exe process, the file that process is spawned from is located in C:\Windows\system32:<br>
<img src="/images/lab3/processes-2.png">
</p>
<p>
For the malicious process, the file is located in the malware sample directory:<br>
<img src="/images/lab3/processes-3.png">
</p>
<p>
Since it was observed that a process was created, another way to locate this process would be setting filters in procmon. This first filter gave me the PID for the original Lab03-03.exe process:<br>
<img src="/images/lab3/find-pid.png">
</p>
<p>
Using that PID allows a search for its child processes:<br>
<img src="/images/lab3/parent.png">
</p>
</div>

<div id="3-3" class="section-link">
<h2>What are the malware's host-based indicators?</h2>
<p>
The first host-based indicator would be the creation of a svchost.exe process based on a different file from another directory. Looking at the procmon results from that process reveals another indicator:<br>
<img src="/images/lab3/file-create.png">
</p>
<p>
The second indicator would be the creation of the <b>practicalmalwareanalysis.log</b> file:<br>
<img src="/images/lab3/log.png">
</p>
</div>

<div id="3-4" class="section-link">
<h2>What is the purpose of this program?</h2>
<p>
Opening practicalmalwareanalysis.log gives an interesting clue:<br>
<img src="/images/lab3/keylog-1.png"><br>
This looks like what I typed in the procmon filter earlier.
</p>
<p>
I created a test text file and did some more typing:<br>
<img src="/images/lab3/keylog-2.png">
</p>
<p>
Sure enough, it looks like this is a keylogger!<br>
<img src="/images/lab3/keylog-3.png">
</p>
</div>

<div id="sample-4" class="section-link">
<h2>Malware Sample 4</h2>
<p>
The fourth sample is an introduction to malware that employ anti-analysis techniques. This sample was tricky to analyze and will be re-visited in Chapter 9.
</p>
</div>

<h2>Questions:</h2>
<ol>
<a href="#4-1"><li>What happens when you run this file?</li></a>
<a href="#4-2"><li>What is causing the roadblock in dynamic analysis?</li></a>
<a href="#4-3"><li>Are there other ways to run this program?</li></a>
</ol>

<div id="4-1" class="section-link">
<h2>What happens when you run this file?</h2>
<p>
Well, let's just double-click Lab03-04.exe and find out:<br>
<img src="/images/lab3/binary-1.png">
</p>
<p>
Wasn't expecting that!<br>
<img src="/images/lab3/binary-2.png">
</p>
</div>

<div id="4-2" class="section-link">
<h2>What is causing the roadblock in dynamic analysis?</h2>
<p>
It appears that this sample is possibly employing some type of anti-analysis technique and deletes itself. After trying to repeatedly run the sample adding one analysis tool at a time, I discovered that it only deletes itself when the network is monitored. Even trying to redirect requests to the Ubuntu VM by editing the hosts file still results in self-deletion.
</p>
</div>

<div id="4-3" class="section-link">
<h2>Are there other ways to run this program?</h2>
<p>
Looking at the strings results, it does appear that there are a few flags that can be used when running this sample:<br>
<img src="/images/lab3/flags.png"><br>
Unfortunately, all of these still result in self-deletion. After exhausting all of the techniques learned so far, I resorted to the solutions appendix and was relieved to find that this was the expected result. More advanced techniques are required and this material will be covered in Chapter 9. I look forward to learning what the solution is!
</p>
</div>
<br>
<br>
<div>
<h2>Conclusion</h2>
<p>
This chapter was an excellent introduction to dynamic analysis and gave me some experience with process, service, and DLL analysis (even a small taste of anti-analysis techniques). Setting up a second Ubuntu VM with a virtual network to mimic a C2 server was also a valuable learning experience. This built a lot on the dynamic analysis techniques I learned in CS 6747 and I look forward to learning even more advanced techniques in future chapters!
</p>
</div>