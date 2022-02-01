# Hack The Box: "Backdoor" Machine - Walkthrough

### Introduction
Welcome! Today, we are going to take a look at a machine on HackTheBox with a dificulty rating of "easy". Even though it is listed as an "easy" box, this could prove to be a challenge for a beginner, so this guide is intended to help beginners get a good grasp on hacking and get a foot in the door.

### Getting Started
To get started on this machine, simply click the "Join Machine" button and wait for a second as the resources and IP address loads.
![Join Machine](Start.PNG?raw=true "Optional Title")
<br><br>
Once the machine is ready and the IP address loads, simply add the IP address to the host file in /etc/hosts as backdoor.htb and we are ready to get started!
### Reconnaissance
A first step in finding vulnerabilities in a machine is to do some scanning and reconnaissance. An easy place to start is with the ports. To scan the ports, I used a combination of rustscan and nmap, but you can use any port scanning program you'd like. After running a rustscan on the machine, I found that ports 22, 80, and 1337 are open. Recalling knowledge from my networking classes, I remembered that port 80 is used for web traffic **but** it runs on an unencrypted channel (Using HTTP and **not** HTTPS).
<br><br>**INSERT SCREENSHOT**<br><br>
Seeing this port as open, I immediately wanted to dig a little deeper into the ports - specifically port 80. To do so, I ran an nmap scan on the three open ports to see if I could gather some more detailed information. Running the nmap scan, I found that port 80 is indeed running an unencrypted web server that uses WordPress 5.8.1 (an outdated version of WordPress). I confirmed this by running a quick whatweb on the backdoor.htb IP address. 
### Finding an exploit
![Anakin](https://media.giphy.com/media/NsIwMll0rhfgpdQlzn/giphy.gif)
Since WordPress 5.8.1 is an outdated version of WordPress, I decided to see if there are some exploits that are available for this version. 
>*_NOTE_:* A lot of vulnerabilities exist in outdated software/hardware, and many times, somebody has already published an exploit to these vulnerabilities. Sometimes, hacking is more about taking advantage of outdated software or simple misconfigurations.
