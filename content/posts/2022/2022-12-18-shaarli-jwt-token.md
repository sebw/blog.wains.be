---
date: 2022-12-18
title: "Generate the JWT (JSON web token) for Shaarli API authentication using Python and Javascript"
---

Official [documentation](https://github.com/shaarli/api-documentation/blob/master/api-authentication.md) of Shaarli only gives a PHP example, but I'm more of a Python kind of guy.

This is how to generate a JSON web token (JWT) using some Python or Javascript code.

## Python

Keep in ming this uses `PyJWT` (`pip install PyJWT`) and not `jwt`.

Replace `SHAARLI_API_SECRET` accordingly:

```python
#!/usr/bin/python3
import time
import jwt
now = int(time.time())
payload = { "iat" : now }
SHAARLI_API_SECRET = "your shaarli REST API Secret"
SHAARLI_HASH_ALGORITHM = "HS512"
token = jwt.encode(payload, SHAARLI_API_SECRET, algorithm = SHAARLI_HASH_ALGORITHM)

print(token)
```

## Javascript

Here's an example with javascript (`npm install jsonwebtoken`)

```js
let jwt = require('jsonwebtoken');
let now = Math.floor(Date.now() / 1000)

const shaarli_api_secret = 'your shaarli REST API Secret"';
const payload = {
  "iat": now
};

let token = jwt.sign(payload, shaarli_api_secret, { algorithm: 'HS512'});

console.log(token)
```

## Test your JWT

You can now use the JWT (replace `<your-newly-obtained-jwt-token>` with the output of previous script):

```bash
curl --request GET \
  --url 'https://bookmark.example.com/api/v1/links?searchterm=linux&limit=1' \
  --header 'Authorization: Bearer <your-newly-obtained-jwt>'
```

## Output

```json
[{"id":4947,"url":"https:\/\/wiki.archlinux.org\/title\/NetworkManager#Configuring_MAC_address_randomization","shorturl":"4X0sQg","title":"NetworkManager - ArchWiki","description":"configure MAC address randomization using NetworkManager","tags":["linux","mac"],"private":false,"created":"2022-12-16T18:10:33+01:00","updated":"2022-12-18T23:00:07+01:00"}]
```