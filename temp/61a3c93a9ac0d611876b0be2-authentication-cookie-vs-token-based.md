## Authentication: Cookie- vs. Token-based 

 Authentication is about confirming that users are who they say they are. Whereas authorization is about permissions of a given user (e.g. admin vs. user). Authentication is an integral part of most apps.

The two main methods for authentication are cookies and tokens. But what are the differences between the cookie- and the token-based approaches?

## Cookie-based Authentication

The cookie-based approach is also often referred to as **session authentication**. When using session authentication, a cookie with the session id is created on the server and is sent to the client. The browser automatically stores the cookie and sends it alongside every subsequent request to the server. The server then looks up the session id and verifies its validity. The client doesn't have to deal with storing session-related information at all.

**Sidenote:** Dealing with sessions by using a cookie is not the same as a **session cookie**. A session cookie is a cookie without the `Max-Age` or `Expires` attribute being set. Therefore a session cookie gets deleted when a user closes the browser window or tab (= a user is ending the session). The term session cookie provides no information about what content a cookie stores.
 
## Token-based Authentication

Tokens use a different approach. The token containing the session information is created on the server. It is encoded and signed by the server and sent to the client. The client can use the session information in that token. In this case, the client has to store the token (usually in `localStorage` or `sessionStorage`) and has to actively send the token along with every request (usually in the `Authorization` header). The server doesn't have to keep track of the sessions. The token contains all information the server needs to verify the session. (**EDIT:** Except the secret key, which is used for signing.) The signing of the token prevents the client from manipulating it.

The most popular way of using tokens for authentication is JSON web tokens (JWTs). You can learn more about JWTs specifically at [jwt.io](https://jwt.io/introduction).

## Summary

The main difference between the cookie and token-based approach is where the session information is stored. In the cookie-based approach, the burden of storing the session is on the server-side in contrast to the token-based approach where the client is responsible for storing the session information.

![authentication.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1636403503161/8wg6Rz35A.png)