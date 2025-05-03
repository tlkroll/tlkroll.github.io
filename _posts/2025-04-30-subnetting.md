---
layout: post
title:  IPv4 Subnetting
categories: write-ups
comments: true
excerpt: "The theory behind subnets and tips for calculations (with cheat sheet)"
---
<p>
When I was first learning about networking, it was difficult for me to wrap my head around the concept of subnetting. When I'm having a tough time understanding a difficult subject like this, I make it a point to study it hard enough to understand every little aspect (maybe this is the reason for my binary exploitation obsession). Most of the material I found did not explain subnetting well and made the associated calculations much harder than they needed to be (this is a recurring theme with many topics and is the reason for my write-up on printf exploits). Here I'll try to simplify what I've learned and add an extra trick I learned along the way. I'll assume certain requisite knowledge such as how basic IP addressing works and hope that this sparks of a love of binary for you just like it did for me.
</p>

<h2>Sections:</h2>
<ol>
<a href="#what-is"><li>What Is Subnetting?</li></a>
<a href="#construction"><li>Contruction of a Subnet</li></a>
<a href="#calculations"><li>Calculations</li></a>
<a href="#cheat-sheet"><li>Cheat Sheet</li></a>
</ol>

<div id="what-is" class="section-link">
<h2>What Is Subnetting?</h2>
<p>The purpose of subnetting is a simple idea. You take the IP address assigned to your network and split it up into different segments. These different segments each act as their own network and cannot freely communicate, so in order for traffic to pass from one subnet to another, it will have to go through the router first. This is a good basic network security measure and also helps with organizing a network. 
</p>
</div>

<div id="construction" class="section-link">
<h2>Construction of a Subnet</h2>
<p>
There are two ways to identify subnets; subnet mask and CIDR notation. Subnet masks looks like <b>255.255.255.0</b> and CIDR notation is <b>/24</b>. What these symbols do is identify the beginning of the subnet range, which determines its size. The zero in <b>255.255.225.0</b> means that the entirety of the fourth octet in whichever IP address is given will be used for one subnet. The <b>/24</b> is derived from the bits of the IP address. There are 8 bits in each octet, so <b>/24</b> means that the last 8 bits will be used for the subnet. If you are not familiar with binary, the reason there are 8 bits per octet is that <b>255</b> in binary is <b>11111111</b>, or eight places. So in binary, the subnet example of <b>255.255.255.0</b> would look like <b>11111111.11111111.11111111.00000000</b>. Another subnet mask like <b>255.255.255.192</b> would be <b>11111111.11111111.11111111.11000000</b>. <b>10000000</b>, which is <b>128</b> in binary, plus <b>01000000</b>, which is <b>64</b> in binary, equals <b>192</b>. In CIDR notation, this example would be a <b>/26</b> subnet because the first 26 places are occupied, leaving the remaining 6 places for subnet addresses. 
</p>
<p>
Now that the subnet mask is defined, we can determine the size of the subnet using that mask. This is easy because all you need to know is the last occupied binary digit. Using the last example of <b>255.255.255.192</b>, which is <b>11111111.11111111.11111111.11000000</b>, I've already said that <b>01000000</b> is equal to <b>64</b> in decimal, so that is the answer. If a given IP address is subnetted using this <b>/26</b> mask, that will split the fourth octet into four segments that each includes 64 addresses. The actual <i>usable</i> addresses will be 62. For every subnet you must subtract one address for the Network ID, which will be the first address, and a second address for the Broadcast ID, which will be the last address. Using an example network address of <b>192.168.0.*</b>, the first subnet would be a range of <b>192.168.0.0 - 192.168.0.63</b>, with <b>192.168.0.0</b> being the Network ID and <b>192.168.0.63</b> being the Broadcast ID. 
</p>
<p>
This example would result in the following subnets:
</p>
<p>
<b>
192.168.0.0 - 192.168.0.63<br>
192.168.0.64 - 192.168.0.127<br>
192.168.0.128 - 192.168.0.191<br>
192.168.0.192 - 192.168.0.255
</b>
</p>
<p>
With the Network ID and Broadcast ID being the first and last address of each range, respectively.
</p>
</div>

<div id="calculations" class="section-link">
<h2>Calculations</h2>
<p>
Now we get to the good stuff. Let me begin by saying that binary is awesome and it is used for so many things that you would place yourself at a huge disadvantage if you did not study it. The first thing I did with my scrap paper when taking my CCNA exam is draw out a table so I could make quick calculations for subnetting questions. Most of these questions only involve the fourth octet, but you can draw a second chart for the third if you are so inclined. 
</p>
<p>
These are the rows of the chart I used:
<ol>
<li><b>CIDR - </b>CIDR notations from /25 through /32.</li>
<li><b># of IPS per subnet - </b>This row contains the decimal equivalents of those binary placeholders. Finding these placeholders in binary is easy; you just work from right to left, start at 1, and multiply by 2 each time.</li>
<li><b>Subnet Masks - </b>To find the subnet mask, you just use the decimal equivalent of the binary number for the CIDR notation such as the example earlier of <b>/26 = 11000000 = 192</b>. Alternatively, you could keep adding the previous mask to the current subnet size, but this would only work for the fourth octet chart.</li>
<li><b># of Hosts - </b>The number of hosts is simply subnet size - 2.</li>
<li><b># of Subnets - </b>When studying subnets I also discovered a neat trick for finding the number of subnets in a given range for fourth octet subnets. If you start at 2 and write the binary placeholders backwards it will give you the number of subnets for the given mask. I just checked how to perform this calculation for the third octet and it turns out that it's the same! I told you binary is awesome!</li>
</ol>
</p>
</div>

<div id="cheat-sheet" class="section-link">
<h2>Cheat Sheet</h2>
<p>
Here is an example of the chart I drew out for my CCNA exam for the fourth octet:
</p>
<p>
<table>
    <tr>
        <th>CIDR</th>
        <th>/25</th>
        <th>/26</th>
        <th>/27</th>
        <th>/28</th>
        <th>/29</th>
        <th>/30</th>
        <th>/31</th>
        <th>/32</th>
    </tr>
    <tr>
        <td># of IPs per subnet</td>
        <td>128</td>
        <td>64</td>
        <td>32</td>
        <td>16</td>
        <td>8</td>
        <td>4</td>
        <td>2</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Subnet Mask</td>
        <td>128</td>
        <td>192</td>
        <td>224</td>
        <td>240</td>
        <td>248</td>
        <td>252</td>
        <td>254</td>
        <td>255</td>
    </tr>
    <tr>
        <td># of Hosts</td>
        <td>126</td>
        <td>62</td>
        <td>30</td>
        <td>14</td>
        <td>6</td>
        <td>2</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td># of Subnets</td>
        <td>2</td>
        <td>4</td>
        <td>8</td>
        <td>16</td>
        <td>32</td>
        <td>64</td>
        <td>128</td>
        <td>256</td>
    </tr>
</table>
</p>
<br><br>
<p>
And if you need the third octet, here is another example (in my experience, I have only seen tests ask for the first couple masks):
</p>
<p>
<table>
    <tr>
        <th>CIDR</th>
        <th>/17</th>
        <th>/18</th>
        <th>/19</th>
        <th>/20</th>
        <th>/21</th>
        <th>/22</th>
        <th>/23</th>
        <th>/24</th>
    </tr>
    <tr>
        <td># of IPs per subnet</td>
        <td>32768</td>
        <td>16384</td>
        <td>8192</td>
        <td>4096</td>
        <td>2048</td>
        <td>1024</td>
        <td>512</td>
        <td>256</td>
    </tr>
    <tr>
        <td>Subnet Mask</td>
        <td>128</td>
        <td>192</td>
        <td>224</td>
        <td>240</td>
        <td>248</td>
        <td>252</td>
        <td>254</td>
        <td>255</td>
    </tr>
    <tr>
        <td># of Hosts</td>
        <td>32766</td>
        <td>16382</td>
        <td>8190</td>
        <td>4094</td>
        <td>2046</td>
        <td>1022</td>
        <td>510</td>
        <td>254</td>
    </tr>
    <tr>
        <td># of Subnets</td>
        <td>2</td>
        <td>4</td>
        <td>8</td>
        <td>16</td>
        <td>32</td>
        <td>64</td>
        <td>128</td>
        <td>256</td>
    </tr>
</table>
</p>
</div>
<br><br>
<p>
I hope this helps! Happy networking!
</p>