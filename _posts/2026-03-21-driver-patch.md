---
layout: post
title:  Patching Kernel Drivers
categories: malware
comments: true
excerpt: "Patching PMA Windows 7 Driver to Work on Windows 10" 
---
<div>
<p>
Labs 10-1 and 10-3 from Practical Malware Analysis include drivers which must be copied to <b>C:\Windows\System32</b>. The original drivers were 32-bit and written for Windows XP. Since XP is practically unusable at this point, new 64-bit drivers written for Windows 7 were made available on the PMA GitHub repo. This is again starting to become a problem because I encountered difficulties finding a Windows 7 VM image and the <b>Lab10-03.sys</b> driver crashes on Windows 10. I discovered the source of this problem is hard-coded offsets to Windows structures that change with each version (sometimes more than once per version).
</p>
</div>
<br>
<br>
<h2>Fixing the Lab10-03.sys Driver:</h2>
<ol>
<a href="#debugging"><li>Debugging the Crash</li></a>
<a href="#patching"><li>Patching the Driver</li></a>
</ol>

<div id="debugging" class="section-link">
<h2>Debugging the Crash</h2>
<p>
Stepping through the executable portion of the sample with OllyDbg showed that the crash occurs on the <b>DeviceIoControl</b> call. Setting a user-mode breakpoint on that call allowed me to try various kernel-mode breakpoints within the driver. To obtain a list of the addresses I first had to locate the device object in memory:<br>
<img src="/images/driver-patch/devobj.png">
</p>
<p>
Then, view the details of the object:<br>
<img src="/images/driver-patch/dt.png">
</p>
<p>
And finally, view the memory at the <b>MajorFunction</b> offset:<br>
<img src="/images/driver-patch/majorfunction.png"><br>
</p>
<p>
After a few tries, I was able to narrow it down to this function:<br>
<img src="/images/driver-patch/function.png">
</p>
<p>
Stepping through the function in kernel-mode revealed the specific instruction causing the crash:<br>
<img src="/images/driver-patch/error.png">
</p>
<p>
Stepping through a third time, I examined the contents of R8 before executing the instruction:<br>
<img src="/images/driver-patch/r8.png">
</p>
<p>
The driver is attempting to move a value to an empty pointer, which is causing the crash. Why is the pointer empty? Looking back at the function, R8 was last defined by an offset from RAX which was the address of the current process returned by <b>IoGetCurrentProcess</b>. After confirming that call successfully returned a valid memory address, I had to do some research to find out why the offset was empty. What I found is that the offsets being moved into R8 and RCX are pointers inside the <b>ActiveProcessLinks</b> structure of the current process for Windows 7. This structure is a list which holds <b>flink</b> (pointer to the next process) and <b>blink</b> (pointer to the previous process). The problem is that these offsets have changed several times since Windows 7 and the offset in the version of Windows 10 that I'm using is at 0x448:<br>
    <table>
        <tr>
            <th>Version</th>
            <th>Offset</th>
        </tr>
        <tr>
            <td>Windows 7</td>
            <td>0x188</td>
        </tr>
        <tr>
            <td>Windows 10 1507-1709</td>
            <td>0x2e8</td>
        </tr>
        <tr>
            <td>Windows 10 1803-1909</td>
            <td>0x2f0</td>
        </tr>
        <tr>
            <td>Windows 10 2004+ / Windows 11</td>
            <td>0x448</td>
        </tr>
    </table>
</p>
</div>

<div id="patching" class="section-link">
<h2>Patching the Driver</h2>
<p>
Before patching the driver I had to confirm my suspicions. After the offsets were written, I read the two values located at offset 0x448 which did look like nearby memory addresses. Then I wrote those addresses to RCX and R8 and continued stepping through the function. I had to repeat this a couple times because there are some redundant moves:<br>
<img src="/images/driver-patch/confirm.png"><br>
Stepping through got me through the end of the function with no errors! After resuming execution I saw that the malicious program in the VM starting generating pop-ups like it was supposed to and the process was not listed in Task Manager!
</p>
<p>
Now I had to open the driver in a hex editor and locate the incorrect values:<br>
<img src="/images/driver-patch/hexeditor.png"><br>
Then, I saved the patched driver and transferred it back to the VM.
</p>
<p>
Getting the driver to run on the VM was a bit tricky. Patching the binary changed its signature and Windows 10 refused to run it, resulting in <b>File not found</b> errors on <b>StartService</b>. First I ensured test signing was enabled:<br>
<img src="/images/driver-patch/testsigning.png">
</p>
<p>
When that didn't work, I had to access the advanced startup settings at <b>Settings > Update & Security > Recovery > Advanced Startup > Restart Now</b>. After restarting, I selected F7 to disable driver signature enforcement after rebooting by selecting <b>Troubleshoot > Advanced Options > Startup Settings > Restart</b>:<br>
<img src="/images/driver-patch/enforcement.png">
</p>
<p>
After adjust the startup settings, I moved the patched binary to <b>C:\Windows\System32</b> and stepped through with OllyDbg:<br>
<img src="/images/driver-patch/success.png"><br>
Success! <b>StartService</b> returned a non-zero value and the process successfully disappeared from Task Manager! Unfortunately, execution freezes in the loop when trying to open the URL, but the important thing is that the driver patch was successful.
</p>
</div>
<br>
<br>
<h1>Conclusion</h1>
<p>
The hard-coded values in the original driver for Lab10-03 made it difficult to complete this lab initially. Fortunately this gave me the opportunity to get some practice with WinDbg to actually debug and patch a kernel driver! I had originally planned to make the patched driver available to anyone else in my situation, but with all the trouble it takes to get it running it's probably not worth it. Still, this was a valuable experience and I learned a ton.
</p>