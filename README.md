<h1>Malicious Packet Capture Analysis Lab</h1>

<br />
<h2>Description</h2>
In this hybrid lab and walkthrough, I received a packet capture file of the traffic after an end user opened an email from a client containing a macro, resulting in a program crash. When I open a packet capture for analysis, I follow a series of simple triaging steps to gain basic information about the file. This includes examining Capture File Properties to determine the duration of the capture in both time and number of packets, and reviewing conversation statistics to determine the amount of device communication and data that was transferred. Within the first few packets, I observed a DNS query from the client to the domain klychenogg[.]com. Interestingly, the IP that resolved this domain happened to be the address that transferred the most data within the packet capture. With the IP address and domain of the of the first connection of the packet capture, I conducted threat intelligence and discovered the domain to be known malicious. A couple of packets lower, a HTTP GET request to the domain downloaded a file with the .spr extension. However, I examined the HTTP stream of the request and the stream data revealed the header of the file beginning with "MZ" followed by "This program cannot be run in DOS mode," indicating the file is actually a portable executable, such as .exe or .dll. Given the common employment of such obfuscation techniques by malware to deceive users, I extracted the file from Wireshark to my virtual machine and obtained its hash using sha256sum. Uploading the hash to VirusTotal, anti-virus vendors confirmed the malware to be Ursnif, a form of spyware. Now that the client machine is confirmed to be infected with malware, I shifted focus to other IP addresses within the packet capture to identify additional indicators of compromise or attack. Most of these IP addresses were associated with domains flagged as malicious, however, within the relations tab, the resolved domains all pointed back to Ursnif's attack chain. Lastly, I uploaded the packet capture to Zui (formerly Brim) for additional analysis, I filtered for event_type == "alert," and twelve alerts were generated, the first three from the final IP address within the conversations tab. Expanding the signature and category of the alert revleaded it generated for being a known Command and Control Server with Dridex. Within the packet capture, that IP address was the only one identified sending and receiving encrypted traffic. This traffic was likely the spyware sending the user's data back to thier server. With this information, I concluded the investigation having identified the initial malware-spawning file, the domains involved in further malware downloads, and the IP address facilitating the command and control server. This lab exercise provided invaluable hands-on experience in incident response, threat intelligence, and packet analysis.

<h2>Utilities Used</h2>

- <b>Wireshark</b>
- <b>Brim/Zui</b> 
- <b>Virus Total</b>
- <b>Linux CLI</b>

<h2>Environments Used </h2>

- <b>Kali Linux through Oracle Virtual Box

<h2>Lab Overview:</h2>

<p align="center">
After reviewing the scenario, the description highlights that malware was liekly downloaded onto Sara's computer by opening a file from a customer. With the provided packet capture, my go-to first step when first analyzing a packet capture file is to view Capture File Properties in statistics to see how many packets, how long the capture was, and when it took place.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/5907b800-5296-4120-ba76-969a7339a961" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/6ff287ad-8204-49a4-8dfa-af0df3a198cc" alt="Dridex Malware Analysis"/>
<br />
<br />
I then pivot within statistics to conversations as it provides invaluable information about the connections/communciations such as how many there are, how many packets were send back and forth and how many bytes were within those packets. Basically showing who the loudest talkers are. In this case we see one IP address communciating with multiple others.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/6787e52d-7926-466d-ae88-e6cb5f2d0b45" alt="Dridex Malware Analysis"/>
<br />
<br />
We can see in this first few packets there is a DNS query to a domain with the IP address that happens to be our top talker back in conversations. So I conducted some basic threat intelligence on the IP and Domain to see what we're working with and found the domain to be labeled as malicious.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/ece84d70-b835-44d5-87c6-f602de552b92" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/aab03228-c321-4c83-bc13-8483e0f87726" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/0943a927-499c-47d5-b0e1-5aac4e7b1778" alt="Dridex Malware Analysis"/>
<br />
<br />
The sixith packet down shows a HTTP GET request which is used by a client-host to request data from a server for downloading data. I right clicked the packet and followed the HTTP stream showing the full HTTP sequence between the client and server and it reveals that the file spet10[.]spr was downlaoded from the malicious domain klychenogg[.]com. However, interestingly, underneath the 200 OK, the string "MZ...This Program cannot be run in DOS mode", is found which the indicates the file to actually be a portable executable such an .exe or .dll rather than .spr. <br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/36ecd77a-151e-4c03-ae47-ab5cf585500a" alt="Dridex Malware Analysis"/>
<br />
<br />
The obfuscation seen in the previous step is common to mask the file type of malware to evade Anti-Virus but more intended for users run the program. In Wireshark I went to file -> export -> HTTP and found the spet10[.]spr file and safely saved it to my Virtual Machine, I used sha256sum to obtain the hash ans uploaded to VirusTotal. The community has clearly identified this as a malicious trojan known as Ursnif Spyware. It is now clear that the host machine downloaded the malware but I will still continue my investigation.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/29849cd6-406c-4fe7-8871-22913941c217" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/7d09d2b4-4afc-40f8-b36c-b9b70baf4776" alt="Dridex Malware Analysis"/>
<br />
<br />
I then pivoted to other IP addresses seen in the conversations statistics to determine if those IPs to be malicious as well or have further Indicators of Compromise or Attack. Starting with the IP with the second most traffic 176[.]32[.]33[.]108. I saw a successful TCP connection then a HTTP GET to the domain cochrimato[.]com retriving the file ojw[.]avi. I then uploaded the domain and IP to Virus Total the IP to be clean but the domain to be malicious.<br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/12b476ec-f35d-4ade-afc7-d860be8f61d0" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/7954831c-243b-46c4-be4c-d79a802ea06f" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/169d900c-41e9-482d-b2cb-960d7093a6b1" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/1e75cc01-6ce5-48f3-a0f2-dc9da3bd5fe7" alt="Dridex Malware Analysis"/>
<br />
<br />
The next IP I examined the traffic was encypted as seen the the TLSv1.2 meaning it is mostly of no use except for the the first Client Hello to the SNI or Server Name Indication which was the hostname the the client was connecting to. I researched that domain and a couple Anti-Viruses found it malicious but nothing concrete. <br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/023c421f-5143-4f7d-bded-cb893e5416b4" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/89854591-8cfa-4f49-adee-aca5a2bc0c56" alt="Dridex Malware Analysis"/>
<br />
<br />
I was running into deadends so I decided to switch tools and download Brim or now Zui which I have used in the past to analyze packet captures. Once I uploaded the packet capture to Zui I filtered for alerts as it comes with a comprehesive database of predefined alerts and found 12 alerts.  <br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/c920dd98-8926-4529-abbb-13c3d5b15589" alt="Dridex Malware Analysis"/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/6aff6b5b-96e5-4f32-8a88-c90995045c6f" alt="Dridex Malware Analysis"/>
<br />
<br />
The first three alerts were generated from the last IP in the list that was not investigated and upon examining the the alert signature and category it can be seen that his IP acted as the C2 or Command and Control server in which the malware Ursnif and Dridex sending the data that is stolen with the trojan. <br/>
<img src="https://github.com/KirkDJohnson/Malicious-Packet-Capture-Analysis-Lab/assets/164972007/392f12a0-52ae-4a3e-9847-f98032334970" alt="Dridex Malware Analysis"/>
<br />
<br />
I now found the type of malware affecting the host, the domain and IP it was downlaoded from, and the IP addresses that is being used for the C2 server, with this inforamtion appropriate measure can now be taken to block the malicious domains and IP adressess. It would also be recomdned to re-image the host machine to ensure removal of the spyware.<br/>
<br />
<br />

<h2>Thoughts</h2>
This lab has been made me more confident and profiecent utilizing Wireshark and Brim/Zui and  demonstrated that an attacker can use mulitple IP addresses and domains when during an attacattacks against a host. Although the clear text HTTP GET request in the sixth packet confirmed the primary malware's existence on the machine, throughout the investigation I uncovered additional layers of complexity the threat actor used in the cyebr kill chain. The walkthrough I was following mentioned that Dridex was part of the attack, however, through Wireshark I could not find any evidence of this which led my to switch to Zui. Having the ability to use mulitple tools when investigating is a crucial skill and I am glad that this lab encouraged me use more than one tool in the analysis. Lastly, being thorough and checking all domains and IP addresses demonstrates the importance of comprehensive threat intelligence in cybersecurity defense, as it equips enterprises to better mitigate similar attacks in the future.
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
