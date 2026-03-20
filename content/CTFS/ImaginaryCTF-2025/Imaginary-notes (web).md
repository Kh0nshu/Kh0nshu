## Prompt 
I made a new note taking app using Supabase! Its so secure, I put my flag as the password to the "admin" account. I even put my anonymous key somewhere in the site. The password database is called, "users".

## Enumeration
- So the hint tells us that the main goal is to get the admin password
- The fact that it tells us that the password database is in 'users' means that we probably need to make an sql injection of some sort.
- Scratch that, i just found the api key in a request to supabase. That is probably the anonymous key he is talking about
	- While playing around with the application and proxying using Burpsuite, I captured a GET request to supabase with the header `GET /rest/v1/users?select=id&username=eq.testingman`
	- and with `Apikey:`
- First thought is what if I send that to intruder and edit it so that it returns the password to the admin? (aka the flag)
- so we edited the header to `GET /rest/v1/users?select=password&username=eq.admin`

## Flag
- ictf{why_d1d_1_g1v3_u_my_@p1_k3y???}