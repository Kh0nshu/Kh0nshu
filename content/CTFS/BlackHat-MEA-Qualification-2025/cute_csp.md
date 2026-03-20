## Prompt
So cute... so quirky...

## Enumeration
- The home page has php code that prints whatever you put if you add ?html= to the url
- There is an admin page that needs an admin token to access (this seems to be the goal)
- They have a bot that logs in as admin and tests for XSS?
- When you try opening the admin.php page it prints 'Oops! Invalid admin token'
	- You have to be logging in from local host and have an admin token
	- The source code shows that if you have FL money in your inventory it will show the flag ` printf("Final Inventory %-5s : %s\n", $cur, $cur === 'FL' ? getenv('DYN_FLAG') : $qty);`
	- So we somehow have to get into admin.php and make it buy FL  money
- Maybe we can somehow use by accessing it  through the the index page that lets us load images from anywhere to load from report.php to make the bot access admin.php and buy FL money and give us the output?
- report.php just needs you to pass ?url=http://localhost:5000/index to run the bot to 
	- we need to figure out a way for us to access admin.php through index.php
	- admin.php will give the flag if we do a post request to buy FL money