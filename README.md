# couchdb-jwt-minimal-tutorial

Tutorial to get starting with JSON Web Tokens and CouchDB

After install CouchDB, open Fauxton > Configuration, + Add Option with:

```
Section = chttpd
Name = authentication_handlers
Value = {chttpd_auth, cookie_authentication_handler}, {chttpd_auth, jwt_authentication_handler}, {chttpd_auth, default_authentication_handler}
```

Generate a base64 encoded secret with:

```bash
echo -n 'supersecret' | openssl base64
```

This command will output: `c3VwZXJzZWNyZXQ=`

Obvious you will replace 'supersecret' with your own and stronger secret

Edit /opt/couchdb/etc/local.d/10-admins.ini and add 

```ini
[jwt_keys]
hmac:_default = c3VwZXJzZWNyZXQ=
```

Restart CouchDB

```bash
service couchdb restart
```

Now we need to generate a JSON Web Token to test.

Go to https://jwt.io/, select HS256 algorithm. At VERIFY SIGNATURE type `c3VwZXJzZWNyZXQ=` on the editable field and check "secret base64 encoded"

Your encoded JWT will be shown on left. Copy it.

Now test with curl:

```bash
curl -H "Authorization: Bearer PASTE_TOKEN_HERE" http://127.0.0.1:5984/_session
```

In my example, the full curl command becames:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.zbgd5BNF1cqQ_prCEqIvBTjSxMS8bDLnJAE_wE-0Cxg" http://127.0.0.1:5984/_session
```

You should revice back something like:

```JSON
{"ok":true,"userCtx":{"name":"1234567890","roles":[]},"info":{"authentication_handlers":["cookie","jwt","default"],"authenticated":"jwt"}}
```

If you get:

```JSON
{"error":"bad_request","reason":"Bad HMAC"}
```

You have made some mistake. Verify all steps and try again.


References:

https://github.com/softapalvelin/couch_jwt_auth
https://docs.couchdb.org/en/stable/api/server/authn.html#jwt-authentication
