<h1>Malicious Packet Capture Analysis Lab</h1>

<br />
<h2>Description</h2>
This lab 

<h2>Utilities Used</h2>

- <b>Wireshark</b>
- <b>Brim/Zui</b> 
- <b>Virus Total</b>
- <b>Linux CLI</b>

<h2>Environments Used </h2>

- <b>Kali Linux through Oracle Virtual Box

<h2>Lab Overview:</h2>

<p align="center">
My go-to first step when first analyzing a packet capture file is to view Capture File Properties in statistics to see how many packets, how long the capture was, and when it took place.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/6ff287ad-8204-49a4-8dfa-af0df3a198cc" alt="Dridex Malware Analysis"/>
<br />
<br />
I then pivot within statistics to conversations as it provides invaluable information about the connections/communciations such as how many there are, how many packets were send back and forth and how many bytes were within those packets. Basically showing who the loudest talkers are. In this case we see one IP address communciating with multiple others.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/6787e52d-7926-466d-ae88-e6cb5f2d0b45" alt="Dridex Malware Analysis"/>
<br />
<br />
We can see in this first few packets there is a DNS query to a domain with the IP address that happens to be our top talker back in conversations. So conducted some basic threat intelligence on the IP and Domain to see what we're working with and found domain to be labeled as malicious.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/ece84d70-b835-44d5-87c6-f602de552b92" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/aab03228-c321-4c83-bc13-8483e0f87726" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/0943a927-499c-47d5-b0e1-5aac4e7b1778" alt="Dridex Malware Analysis"/>
<br />
<br />
The sixith packet down shows a HTTP GET request which is used by a client-host to request data from a server for downloading data. I right clicked the packet and followed the HTTP stream showing the full HTTP sequence between the client and server and it reveals that the a file spet10[.]spr was downlaoded from the malicious domain klychenogg[.]com. However, interestingly, underneath the 200 OK, the string "MZ...This Program cannot be run in DOS mode", is found which the signicature MZ indicates the file to actually be a portable executable such an .exe or .dll rather than .spr. <br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/36ecd77a-151e-4c03-ae47-ab5cf585500a" alt="Dridex Malware Analysis"/>
<br />
<br />
The obfuscation seen in the previous step is common to mask the file type of malware to evade Anti-Virus but more intended for users run the program. In Wireshark I went to file -> export -> HTTP and foudn the the spet10[.]spr file and safely saved it to my Virtual Machine, I used sha256sum to obtain the hash ans uploaded to VirusTotal. The community has clearly identified this as a malicious trojan known as Ursnif Spyware. It is now clear that the host machine downloaded the malware but I will still continue my investigation.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/29849cd6-406c-4fe7-8871-22913941c217" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/7d09d2b4-4afc-40f8-b36c-b9b70baf4776" alt="Dridex Malware Analysis"/>
<br />
<br />
I then pivoted to other IP addresses seen in the conversations statistics to determine if those IPs to be malicious as well or have further Indicators of Compromise or Attack. Starting with the IP with the second most traffic 176[.]32[.]33[.]108. I saw a successful TCP connection then a HTTP GET to the domain cochrimato[.]com retriving the file ojw[.]avi. I then uploaded the domain and IP to Virus Total the IP to be claen but the domain to be malicious.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/12b476ec-f35d-4ade-afc7-d860be8f61d0" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/7954831c-243b-46c4-be4c-d79a802ea06f" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/169d900c-41e9-482d-b2cb-960d7093a6b1" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/1e75cc01-6ce5-48f3-a0f2-dc9da3bd5fe7" alt="Dridex Malware Analysis"/>
<br />
<br />
The next IP I examined the traffic was encypted as seen the the TLSv1.2 meaning it is mostly of no use except for the tje first Client Hello to the SNI or Server Name Indication which was the hsotname the the client was connecting to. I researched taht domain and again found it to be from Russia with a couple Anti-Viruses finding it malicious but nothing concrete. <br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/023c421f-5143-4f7d-bded-cb893e5416b4" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/89854591-8cfa-4f49-adee-aca5a2bc0c56" alt="Dridex Malware Analysis"/>
<br />
<br />
I was running into some deadends so I decided to switch tools and download Brim or now Zui which I have used to analyze packet captures previously for further analysis. Once I uploaded the packet capture to Zui I filtered for alerts as it comes with a comprehesive database of predefined alerts and found 12 alerts.  <br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/c920dd98-8926-4529-abbb-13c3d5b15589" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/6aff6b5b-96e5-4f32-8a88-c90995045c6f" alt="Dridex Malware Analysis"/>
<br />
<br />
Text <br/>
<img src="" alt="Dridex Malware Analysis"/>
<br />
<br />
Text <br/>
<img src="" alt="Dridex Malware Analysis"/>
<br />
<br />
Text <br/>
<img src="" alt="Dridex Malware Analysis"/>
<br />
<br />
Text <br/>
<img src="" alt="Dridex Malware Analysis"/>
<br />
<br />
</p>
<h2>Thoughts</h2>
This lab 
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
