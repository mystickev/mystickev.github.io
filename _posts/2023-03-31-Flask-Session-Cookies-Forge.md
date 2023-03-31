![Heading](/assets/images/favicon/CRACKING.png)

Flask uses a secure URL-safe signed serializer called "itsdangerous" to encode client-side session cookies. To prevent tampering by the client, the Flask app utilizes a secret key to sign the session cookie.

Although the session cookie may appear to be a sequence of random characters, it is actually easy to decode with the appropriate key.
In this blog, we will look at how flask application cookies can be modified to grant different authentication-level privileges.

## Exploitation Phase:

Our web application successfully authenticates into any account and creates a cookie for the sign-on. The web application is made with a flask backend which means the cookies will be flask signed.


![Basic-web-app](/assets/images/favicon/basic-website.png)


The flask session package tracks, stores, and validates the session cookies. When using sessions, the data is typically stored in the browser as a cookie, referred to as the session cookie. 
Unlike a regular cookie, Flask employs cryptographic signing to secure the session cookie. This implies that although the contents of the cookie can be seen, it can't be altered without the secret key utilized to sign it. Hence, it's advisable to use a lengthy and intricate string as the secret key.
Upon setting the session cookie, all subsequent server requests authenticate the cookie's validity by using the same secret key to decode it. If Flask is unable to decrypt the cookie, its contents are disregarded, and a new session cookie is issued to the browser.
These cookies may seem similar to JWT tokens but are not JWT tokens. Here, our test web application creates a flask cookie during authentication to the server. 

![Session-Cookie](/assets/images/favicon/session-cookie.png)

The token can be successfully decoded in jwt.io, as shown below, but cannot be edited the same way as normal JWT tokens. Changing the value in jwt.io and re-using the cookie will prompt a 500 error on the page.

![JwT-decoding-flask-cookie](/assets/images/favicon/flask-cookie-jwt.png)

Now, to get to the fun part, Our goal is to decode the cookie and change any values that might favour our authentication and grant us access to some juicy data, cause why not.
In this engagement, i came across the flask_unsign tool that does the "magic" of decoding, bruteforcing and resigning. Pretty neat huh!!(scriptkiddie voice).
One can install it simply using python pip package.

```└─$ pip3 install flask_unsign```


First things first, Check what data is embedded inside the flask cookie. We do this using the following Flask_unsign command:

```└─$ python3 -m Flask_unsign  --decode --cookie 'eyJpc0hhY2tlZCI6ZmFsc2V9.ZCauZA.Pwa3t9s4ER7Ct6NM9Uc_UPnMqLU'```

![Decoding_flask_cookie](/assets/images/favicon/flask-unsign-decode.png)

As shown above, the tool decodes the value part , same as we observed in jwt.io.

***{'isHacked': False}***

Our goal now is to change the value above to *True*

But, If we proceed to signing the key, without the secret used to sign, the appliaction will deem the cookie as invalid and slurp us with another 500 error.

To prevent this, we are going to bruteforce for the key used to sign the cookie using the flask_unsign tool, again!. This can be achieved using the command below:

```└─$ python3 -m flask_unsign --wordlist /usr/share/wordlists/rockyou.txt --unsign --cookie 'eyJpc0hhY2tlZCI6ZmFsc2V9.ZCauZA.Pwa3t9s4ER7Ct6NM9Uc_UPnMqLU' --no-literal-eval```

The screenshot below shows the decoded secret as **"secret"**

![Bruteforced_signing_secret](/assets/images/favicon/flas-secret.png)

we can now move ahead to sign a new cookie with value {'isHacked': True} using the secret key 'secret' using the command:

```└─$ python3 -m flask_unsign --sign --cookie "{'isHacked': True}" --secret 'secret'```

The screenshot below shows the new flask cookie with the magic value to get us into the application.

![New_signed_cookie](/assets/images/favicon/new-cookie-signed.png)

We can then go ahead and paste the cookie into our browser and reload the page to use the fresh cookie.

![Inspect-change-cookie-value](/assets/images/favicon/inspect-fresh-cookie.png)

We can now see the secret "data" that we have worked hard to see below.

![Secret_data](/assets/images/favicon/flag-flask.png)

## TAKEAWAYS
 As unknown once said, ***If you don't secure your Flask cookies, you might be giving away more than just crumbs.***












