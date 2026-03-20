## Prompt 
Didn't have time to implement the email sending feature but that's ok, the site is 100% secure if nobody knows their password to sign in!
## Enumeration
- First of all I'll have the source code open and the website open and figure out how it works as we go.
- The website greets us with a login page and give us a hint that it doesn't send emails to give the initial password
	- it seems to use a function normalizeEmail to trim special characters
- Our goal should be to log in without a password since it doesn't send an email with the temporary password. ( a glance at dashboard.ejs shows that the flag is on that page)
### Inputs
- So the next stage is to look at how this website processes our inputs
	- It seems like it normalizes the email but not the password
	- and it looks like it does protect itself against SQL injection with this line `db.get(`SELECT * FROM users WHERE email = ?`, [email], (err, user) `
- What are the conditions set for us to be redirected to dashboard?
	- if req.session.user returns true, you can pass the restrict and authenticated functions and be redirected to the dashboard
	- the way request.session.user is set is by logging in and passing the authenticate function
- How do we log in?
	- we register an account and it then creates an initial password that uses the email(not normalized)+16 random bytes
	- Then it uses bcrypt.hash to hash the random initial password and then save it in the database
	- *2 hours of questioning life and repeatedly looking through code pass by*
		- I have no idea what bcrypt.hash does so lets look it up
		- First thing that comes up when looking up bcrypt.hash documentation is security issues and concerns
			- only the first 72 bytes of the string actually get used
	- The normalized email must be less than 64 in length
	- So the none normalized, which is used in creating the password can be more than that, but once normalized it should me less than 64
	- Looking up how normalizeEmail works
		- trims whitespace, lowcases domain, does gmail specific rules like removing '.'
- So if we make an email `'test' + '.' * 58 + '@gmail.com` so that it has 72 bytes, then bcrypt.hash should only use those 72 letters to make the initial password.
## Flag
  ictf{8ee2ebc4085927c0dc85f07303354a05} 