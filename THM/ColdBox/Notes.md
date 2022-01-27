# NOTES
We've got drive, power, hunger and we decided take it all out on innocent wordpress websites. Learning the tooling with wpscan and this box was a prime candidate.

## SUMMARY
	- We just wanted to work more on wordpress boxes and we got one. 
	- Simple enough most of the exploits we've already done before.
	- Foothold required wordpress sub-directory and pages enumeration.
	- After also enumerating users on the page we bruteforced the admin account.
	- Got a reverse shell through the theme 'twentyfifteen'. 
	- Privilege Escalation was through SUID vulnerability on 'find' executable.

## THINGS WE LEARNED
Aside from humility and the lack of my ability, here are somethings we learned from the "box" : 

	- wpscan : You can increase number of threads with 
			-t 20
	- Quick reminder on SUID caps on find
	- We relived the importance of going through a quick port scan first and then doing a detailed scan on the narrowed list of ports



# BOX 
All Attachments seen below will be found in their respective directories or will be in the Attachments sub-folder.

## ENUMERATION

#### NMAP
- Portscan 

		nmap -p- -T4  10.10.164.88
	![[Pasted image 20211208174704.png]]
- detailed scan
![[Pasted image 20211208180013.png]]
	- We notice a HTTP server and an SSH server running on ports 80 & 4512 respectively

		
#### FFUF 
- Found some very nice subdirectories 
![[Pasted image 20211208180837.png]]

##### HIDDEN 
- Something about c0ldd changing Hugo's password
![[Pasted image 20211208172556.png]]
- c0ldd is probably admin 

#### wpscan 
- Username Enumeration 
	- the cold in person
	- hugo
	- c0ldd
	- philip

	![[Pasted image 20211208171131.png]]
	
- Password Bruteforcing 'c0ldd' credentials as he seemed like he could be admin 

		c0ldd:9876543210
	![[Pasted image 20211208173831.png]]
	
- login to dashboard at /wp-admin with the above credentials


## EXPLOIT
- Editing theme 404.php page (Side Menu->Themes->Editor->404.php) with our reverse shell payload
![[Pasted image 20211208174300.png]]
- Visit the page to execute code and get reverse shell
![[Pasted image 20211208174151.png]]
![[Pasted image 20211208174159.png]]

## POST-EXPLOIT

- found a suid vulnerability, it was the executable quite popularly known as 'find'
	![[Pasted image 20211208175907.png]]
- Got a payload off of gtfo bins and cracked in
	![[Pasted image 20211208175937.png]]
- voila root
