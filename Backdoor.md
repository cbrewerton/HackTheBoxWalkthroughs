# Hack The Box: "Backdoor" Machine - Walkthrough

### Introduction
Welcome! Today, we are going to take a look at a machine on HackTheBox with a dificulty rating of "easy". Even though it is listed as an "easy" box, this could prove to be a challenge for a beginner, so this guide is intended to help beginners get a good grasp on hacking and get a foot in the door.

### Getting Started
To get started on this machine, simply click the "Join Machine" button and wait for a second as the resources and IP address loads.<br><br>
![Join Machine](Start.PNG?raw=true "Optional Title")
<br><br>
Once the machine is ready and the IP address loads, simply add the IP address to the host file in /etc/hosts as backdoor.htb and we are ready to get started!
### Reconnaissance
A first step in finding vulnerabilities in a machine is to do some scanning and reconnaissance. An easy place to start is with the ports. To scan the ports, I used a combination of rustscan and nmap, but you can use any port scanning program you'd like. After running a rustscan on the machine, I found that ports 22, 80, and 1337 (this one will be useful later) are open. Recalling knowledge from my networking classes, I remembered that port 80 is used for web traffic **but** it runs on an unencrypted channel (Using HTTP and **not** HTTPS). 
<br><br>
![RustScan](Rustscan.PNG?raw=true)
<br><br>
Seeing this port as open, I immediately wanted to dig a little deeper into the ports - specifically port 80. To do so, I ran an nmap scan on the three open ports to see if I could gather some more detailed information. Running the nmap scan, I found that port 80 is indeed running an unencrypted web server that uses WordPress 5.8.1 (an outdated version of WordPress). I confirmed this by running a quick whatweb on the backdoor.htb IP address. 
### Finding an exploit
![Anakin](https://media.giphy.com/media/NsIwMll0rhfgpdQlzn/giphy.gif)<br><br>
Since WordPress 5.8.1 is an outdated version of WordPress, I decided to see if there are some exploits that are available for this version. 
A lot of vulnerabilities exist in outdated software/hardware, and many times, somebody has already published an exploit to these vulnerabilities. Sometimes, hacking is more about taking advantage of outdated software or simple misconfiguration
<br>
Since there may be a WordPress vulnerability, I decided to run a wpscan on the IP address. If you do not already have an API token for wpscan, you can register for free at wpscan.com.
<br><br>
![WPScan](WpScan.PNG?raw=true)
<br><br>
If we notice in the output for the vulnerability from wpscan, we can notice a link to a web address, and further down, we can see that there is a username that was found named: "admin".
<br><br>
Looks like this page is forbidden, but let's see if we can see anything from some of its parent folders. If we take out the "/akismet" part from the url and visit that address, we are met with a page of wp-plugins. There are a couple of links here in the folder structure, but let's focus on the ebook-download folder. Doing a simple search can show us that there are some explots to the WordPress ebook-download plugin, specifically using a directory traversal attack. (https://www.exploit-db.com/exploits/39575)
<br><br>
Looking at this web page, we can see the basic syntax for the directory traversal attack as it exists in the file structure of this ebook download plugin under the section titled PoC. If we visit that address in our browser, we can grab a file titled "wp-config.php". Let's save this somewhere and see if we can take a look at its contents.
Looks like we have a database name, database admin name, username, and password. These *could* come in handy, but they may not. Let's hold onto this file and see what else we can find too.
<br><br>
I poked through a lot of the folders here on my own time, so I will just share here what I found. I found a lot of dead ends and a lot of useless information, but some information that I did find to prove useful was the process list. To find it, I pasted this url into my browser: http://backdoor.btb/wp-content/plugins/ebook-download/filedownload.php?ebookdownloadurl=/proc/sched_debug<br>
In this file, I found a list of the running processes and found that gdbserver was running as PID 32998. (the PID may be different for you)
<br><br>
![GDB](gdbserver.PNG?raw=true)
<br><br>
If we dig into that specific process by downloading a file from /proc/[PID]/cmdline, we can verify that gdbserver is what is currently running on port 1337 (that we discorvered earlier). 

### Exploiting the vulnerability
As mentioned earlier, a lot of hacking is simply finding the vulnerabilities from outdated systems/open ports and injecting a pre written explot into these "holes". Let's do a quick search for gdbserver exploits, and we can find a python script that explots this vulnerability. If you run the metasploit console and get into the existing exploit for this vulnerability, you can see that there are some options to configure.
Configure the options by setting the RHOST=[TARGET_IP], RPORT=1337, LHOST=[YOUR_IP], LPORT=4444.<br>
Once you have configured the settings for the exploit, you can run netcat on your local machine to listen on port 4444, and then run the exploit. This will perform a TCP handshake and start a reverse shell on your system on the netcat listener. 
<br><br>
![Shell](Shell.png?raw=true)
<br><br>
Once you have started a shell, there are a few ways to get "root" access on the system. If you just type the following commands into the system, you should get a root terminal as shown below.
<br><br>
![root](Root.png?raw=true)
<br><br>
### CONGRATUATIONS! You have officially exploited the system, and gained root access through a reverse shell on a vulnerable system. 
