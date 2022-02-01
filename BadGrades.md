# Hack The Box: "Backdoor" Machine - Walkthrough

### Introduction
Welcome! Today, we are going to take a look at a machine on HackTheBox with a dificulty rating of "easy". Even though it is listed as an "easy" box, this could prove to be a challenge for a beginner, so this guide is intended to help beginners get a good grasp on hacking and get a foot in the door.

### Getting Started
To get started on this machine, simply click the "Join Machine" button and wait for a second as the resources and IP address loads.
![Join Machine](Start.PNG?raw=true "Optional Title")
<br><br>
Once the machine is ready and the IP address loads, simply add the IP address to the host file in /etc/hosts as backdoor.htb and we are ready to get started!
### Reconnaissance
A first step in finding vulnerabilities in a machine is to do some scanning and reconnaissance. An easy place to start is with the ports. To scan the ports, I used a combination of rustscan and nmap, but you can use any port scanning program you'd like. After running a rustscan on the machine, I found that ports 22, 80, and 1337 (this one will be useful later) are open. Recalling knowledge from my networking classes, I remembered that port 80 is used for web traffic **but** it runs on an unencrypted channel (Using HTTP and **not** HTTPS). 
<br><br>**INSERT SCREENSHOT**<br><br>
Seeing this port as open, I immediately wanted to dig a little deeper into the ports - specifically port 80. To do so, I ran an nmap scan on the three open ports to see if I could gather some more detailed information. Running the nmap scan, I found that port 80 is indeed running an unencrypted web server that uses WordPress 5.8.1 (an outdated version of WordPress). I confirmed this by running a quick whatweb on the backdoor.htb IP address. 
### Finding an exploit
![Anakin](https://media.giphy.com/media/NsIwMll0rhfgpdQlzn/giphy.gif)
Since WordPress 5.8.1 is an outdated version of WordPress, I decided to see if there are some exploits that are available for this version. 
>*_NOTE_:* A lot of vulnerabilities exist in outdated software/hardware, and many times, somebody has already published an exploit to these vulnerabilities. Sometimes, hacking is more about taking advantage of outdated software or simple misconfiguration
<br>
Since there may be a WordPress vulnerability, I decided to run a wpscan on the IP address. If you do not already have an API token for wpscan, you can register for free at wpscan.com.
<br><br>**INSERT SCREENSHOT**<br><br>
If we notice in the output for the vulnerability from wpscan, we can notice a link to a web address, and further down, we can see that there is a username that was found named: "admin".
<br><br>
Looks like this page is forbidden, but let's see if we can see anything from some of its parent folders. If we take out the "/akismet" part from the url and visit that address, we are met with a page of wp-plugins. There are a couple of links here in the folder structure, but let's focus on the ebook-download folder. Doing a simple search can show us that there are some explots to the WordPress ebook-download plugin, specifically using a directory traversal attack. (https://www.exploit-db.com/exploits/39575)
<br><br>
Looking at this web page, we can see the basic syntax for the directory traversal attack as it exists in the file structure of this ebook download plugin under the section titled PoC. If we visit that address in our browser, we can grab a file titled "wp-config.php". Let's save this somewhere and see if we can take a look at its contents.
<br><br> **INSERT SCREENSHOT** <br><br>
Looks like we have a database name, database admin name, username, and password. These *could* come in handy, but they may not. Let's hold onto this file and see what else we can find too.
<br><br>
I poked through a lot of the folders here on my own time, so I will just share here what I found. I found a lot of dead ends and a lot of useless information, but some information that I did find to prove useful was the process list. To find it, I pasted this url into my browser: http://backdoor.btb/wp-content/plugins/ebook-download/filedownload.php?ebookdownloadurl=/proc/sched_debug<br>
In this file, I found a list of the running processes and found that gdbserver was running as PID 32998.
<br><br> **INSERT SCREENSHOT** <br><br>
