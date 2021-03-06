---
description: How to integrate a web application with Auth0.
---

# Integrating a Web App with Auth0

Auth0 supports the [OpenID Connect / OAuth2 Login](http://openid.net/specs/openid-connect-basic-1_0.html) protocol. This is the protocol used by companies like [Google](https://developers.google.com/accounts/docs/OAuth2Login), [Facebook](http://developers.facebook.com/docs/facebook-login/login-flow-for-web-no-jssdk/) and [Microsoft](http://msdn.microsoft.com/en-us/library/live/hh243647.aspx) among others so there are plenty of libraries implementing it on various platforms.

The steps are quite simple though:

1. Setting up the callback URL in Auth0.

After authenticating the user on Auth0, we will do a GET to a URL on your website. For security purposes, you have to register this URL  on the __Application Settings__ section on Auth0 Admin app.

2. Triggering login manually or integrating the Auth0Lock.

<%= include('../../_includes/_lock-sdk') %>

3. After the user authenticates, your app will be called to this endpoint with a `GET`.

```text
GET ${account.callback}?
      code=AUTHORIZATION_CODE
      &state=VALUE_THAT_SURVIVES_REDIRECTS
```

::: note
It is a good practice to check that the `state` value received and sent are the same. It can serve as a protection against XSRF attacks.
:::

4. Your app will have to send the `code` to the Auth0 server through a `POST`.

```text
POST https://${account.namespace}/oauth/token
Content-type: application/x-www-form-urlencoded
client_id=${account.clientId}
&redirect_uri=${account.callback}
&client_secret=${account.clientSecret}
&code=AUTHORIZATION_CODE
&grant_type=authorization_code
```

5. The response from the server will look like this:

```text
{
   "access_token":"2YotnF..........1zCsicMWpAA",
   "id_token": "......JSON Web Token......"
   "token_type": "Bearer",
}
```

::: note
The `access_token` can then be used to call Auth0's `userinfo` endpoint to get the attributes of the user.
:::

6. Finally, you can get the user profile by calling:

```text
GET https://${account.namespace}/userinfo/?access_token=2YotnF..........1zCsicMWpAA
```

The `userinfo` endpoint will return something like this:

```text
{
  "sub": "google-oauth2|123",
  "email": "johnfoo@gmail.com",
  "family_name": "Foo",
  "gender": "male",
  "given_name": "John",
  "locale": "en",
  "name": "John Foo",
  "nickname": "johnfoo",
  "picture": "https://lh4.googleusercontent.com/-OdsbOXom9qE/AAAAAAAAAAI/AAAAAAAAADU/_j8SzYTOJ4I/photo.jpg"
}
```

For more details on Auth0's normalized user profile, see [here](/user-profile).
