# HTB CYBERAPOCALYPSE CURSED

![image_info](/assets/images/favicon/cyberapocalypse.jpg)

## WEB: PASSMAN

![date](https://img.shields.io/badge/date-18.03.2023-lightgreen.svg)  
![solved in time of CTF](https://img.shields.io/badge/solved-in%20time%20of%20CTF-lightgreen.svg)  
![WEB category](https://img.shields.io/badge/category-WEB-lightgreen.svg)
![score](https://img.shields.io/badge/score-300-blue.svg)

*Question:* Pandora discovered the presence of a mole within the ministry. To proceed with caution, she must obtain the master control password for the ministry, which is stored in a password manager. Can you hack into the password manager?

__SOLUTION__

This was a very interesting challenge to me because it was a totally new attack challenge for me. It took me a day+ to learn and figure it out. Lets dive into how i solved it.
The challenge provided us with a zip file that contained the website's source code and a dockerfile for us to run it locally. First, it appeared as a node js application, so i checked routes to see the paths that the website was routing through. Here, I found a graphql endpoint.

![Image_info](/assets/images/favicon/graphql.png)

I opened the url for the instance provided and a neat looking website opened up with a basic login screen and an option to register as a user. I proceeded to create a user with user: test password: test email: test@test.com

![Image_info](/assets/images/favicon/create-user.png)

The website directed me to what appeared to be an implementation of a password manager. I tested the password adding feature through a proxy and found nothing interesting on the graphql query used.

![image_info](/assets/images/favicon/notes-update.png)

I proceeded to test the graphql endpoint i had identified earlier. On navigating the endpoint, the website gave us an error to submit a query, obviously!:unamused:

![image_info](/assets/images/favicon/make-query.png)

I did a bit of research and came across a payloadofthings for graphql exploitation. Awesome!!
I Found a supercool query that could dump the whole graphql query path. This was a misconfiguration bug known as introspection :boom:.

![image_info](/assets/images/favicon/introspection.png)

I submitted it as a query on the graphql endpoint, https://example.com/graphql?query=(introspect-payload) and passed the request through burp and forwarded it to repeater to make future requests easier.

![image_info](/assets/images/favicon/introspectpayload.png)

This was the response the server provided, a json tree for the graphql query paths.

![image_info](/assets/images/favicon/introspectresponse.png)

After a bit more research, i found an extension in burp that could be used to automate the introspection and display some clean results.

![image_info](/assets/images/favicon/burpintrospection.png)

All the extension required was the introspection data returned by the query i did prior. I saved burps response to a file and loaded it to the extension.
This was superhelpful since i found a graphql query path that i hadn't seen before(A whole painful night of searching and trying different stuff 😬)

This was the extension response:

![image_info](/assets/images/favicon/update-pass-mutation.png)

This was the syntax to make the query. Something to note is that a mutation query is used to read, write and update server side data. This was a mutation query to update password for a user.

![image_info](/assets/images/favicon/update-pass-mutation-on-tool.png)

I had gone down a few rabbitholes before i got to this point including trying to read the username and password by making a query.

![image_info](/assets/images/favicon/tried-display-password.png)

But all in all i was able to learn alot on graphql queries. To move on, i figured to this point that this was the vulnerable query. Vulnerable how??
For me, IDOR seemed to be the most logical directions since i figured i could try changing the admin's account password. Why admin you ask?, Well,  I had tried to create an admin account which had given me an error that the user already exists.
I therefore proceeded to make the query to update the admin password and surprisingly!!!, it worked!

![image_info](/assets/images/favicon/change-pass.png)

I logged in to the application with my new password and well, the flag was there,saved as a password entry.

![image_info](/assets/images/favicon/pwned.png)

## LESSONS

Dont give up if its interesting,Wont matter if you know nothing about it, A rabbithole is good sometimes, and finally, check your watch while playing ctf, It gets dark fast.

