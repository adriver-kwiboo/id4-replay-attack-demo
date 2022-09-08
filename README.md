A big thank you to Brock Allen, he has helped me diagnose this.

Basically the response that I'm intercepting and replacing has the header to set the auth cookie.

Set-Cookie: idsrv.session=XXXX; path=/; secure; samesite=none
Set-Cookie: .AspNetCore.Identity.Application=XXXX
ID Server using ASP.NET Core's cookie authentication handler checks the cookie to make sure it hasn't expired, and the claim for the user's unique ID is read from it. That's how IdentityServer knows who the user is so it can issue tokens for them.

Brock has suggested I look into the profile service in ID server to see how I can use that to confirm the user is valid on the 2nd attempt.



## Introduction

We recently failed a pen test due to our implementation of Identity Server 4 not preventing a replay attack.
I have uploaded a simplified version of our setup to github to demonstrate what is going wrong.

## Running Instructions

- Clone github repo
- In VS start multiple projects (IdentityServer + API)
- In VS Code navigate to Bebop.WebApp
- npm install
- npm start
- Get a trial version of BurpSuite Pro: https://portswigger.net/burp/pro
- Start a new temp project
- Proxy
- Options
- Make sure Intercept Server response is ticked

[![enter image description here][1]][1]

- On the Intercept tab
- Click open browser
- Navigate to http://localhost:3000
- Click Login button
- In Burpsuite click the "Intercept is off" to turn it on:

[![enter image description here][2]][2]

- Input "alice" as username and password
- In Burpsuite, Forward the first response
- You should get a 302 as the second response:

[![enter image description here][3]][3]

- Copy this response to Notepad
- Turn off Intercept, and it will continue you back into localhost:3000
- Click the Sign out button
- Navigate back to http://localhost:3000
- Click the Sign in button
- Turn on Intercept back in Burpsuite.
- Input an invalid username / password
- Forward the first response
- On the 200 response, where it displays the invalid username / password. Replace the response, with the text you previously copied into Notepad
- Turn off Intercept
- You will get an error page
  [![enter image description here][4]][4]
- Navigate to http://localhost:3000
- Click the Sign in button
- You will see that you are not prompted for the username / password, but instead logged straight in.

  [1]: https://i.stack.imgur.com/zKBgb.png
  [2]: https://i.stack.imgur.com/JPLmG.png
  [3]: https://i.stack.imgur.com/tt7ma.png
  [4]: https://i.stack.imgur.com/t4xzK.png

---

- **Identity** will run on `https://localhost:5001`.
- **Web API** will run on `https://localhost:5002`.
- **React App** will run on `https://localhost:3000`.
