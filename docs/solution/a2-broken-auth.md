# Broken Authentication and Session Management

## Insecure Reset Password

The `Reset password` functionality can be triggered by visiting an URL such as below

http://127.0.0.1:9090/resetpw?login=user&token=ee11cbb19052e40b07aac0ca060c23ee

The trust establishment in reset password is inherently weak because the _login_ name and _token_ parameter required to execute the password reset is user supplied. Additionally the apparently random key is the MD5 hash of _login_ name which can be easily computed by an attacker.

This issue can be exploited by an attacker to reset any user's password by using an URL such as below

```
http://127.0.0.1:9090/resetpw?login=<username>&token=<md5(username)>
```

**Solution**

Store the password reset request along with a randomly generated token string and expiry  

Email a reset link containing that token and username to the user

Validate the reset token for the user before password reset

## Insecure Session Secret

The session secret is used is insecure and is used in the example snippets across the web

**Vulnerable Code snippet**

*server.js*
```
...
app.use(session({
  secret: 'keyboard cat',
  resave: false,
...
```

This allows an attacker to
1. Decrypt a user's session
2. Potentially forge the session cookie and bypass authentication

**Solution**

Always use unique, long, secure random generated for secrets

**Recommendation**

- Do not copy paste code without understanding what it does
- Rotate session secrets
- Store secrets in environment variables or config files
- Consider using a secret management solution if your scale demands it

**References**

- https://www.owasp.org/index.php/Broken_Authentication_and_Session_Management
- https://www.owasp.org/index.php/Forgot_Password_Cheat_Sheet