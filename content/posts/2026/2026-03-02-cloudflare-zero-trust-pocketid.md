---
date: 2026-03-02
title: "Single Sign On with PocketID for your services behind Cloudflare zero trust"
---

This post is an update on my 2023 post that was using Authentik.

I've moved away from Authentik because I was probably using 1% of the tool. 

I settled on PocketID because I expect it to be simpler to maintain.


Assumptions:

- you are already exposing a simple website `service.example.com` through Cloudflare Zero Trust
- this service is currently publicly accessible with no authentication whatsoever
- you still have to install PocketID at `id.example.com` which will also be behind Cloudflare Zero Trust
- you already have Docker running
- once PocketID is deploy, you want to put `service.example.com` behind its authentication

You will need to find the name of your Cloudflare team:

- Open https://one.dash.cloudflare.com/
- Login
- Click Settings
- Find the Team name (e.g. `xyz`) and Team domain (e.g. `xyz.cloudflareaccess.com`) 
- Save those as they will be used below

Deploy PocketID:

Create a `docker-compose.yml` file:

```yaml
services:
  pocket-id:
    image: ghcr.io/pocket-id/pocket-id:v2
    restart: unless-stopped
    env_file: env
    ports:
      - 1411:1411
    volumes:
      - "/local/path/data:/app/data"
    healthcheck:
      test: [ "CMD", "/app/pocket-id", "healthcheck" ]
      interval: 1m30s
      timeout: 5s
      retries: 2
      start_period: 10s
```

Create an `env` file: 

```ini
APP_URL=https://id.example.com

# Encryption key (choose one method):
# Method 1: Direct key (simple but less secure)
# Generate with: openssl rand -base64 32
ENCRYPTION_KEY=somerandomencryptionkeythatshouldbe32characters
TRUST_PROXY=true
MAXMIND_LICENSE_KEY=
PUID=1000
PGID=1000

# opting out callbacks
ANALYTICS_DISABLED=true
VERSION_CHECK_DISABLED=true

# Explicitly refuse signups
ALLOW_USER_SIGNUPS=false
```

Deploy PocketID with `docker-compose up -d` in the folder of both files.

Configure Cloudflare Zero Trust to make PocketID publicly accessible.

- Go to https://one.dash.cloudflare.com
- Networks > Connectors
- Choose your server that runs cloudflared
- Add PocketID to "Published application routes" 
- id.example.com --> http://127.0.0.1:1411

Make sure you have an Edge certificate for your services:

- https://dash.cloudflare.com
- SSL/TLS
- Edge Certificates

You should be able to access PocketID:

- create an account
- add a passkey to access that account

Once this is all figured out, we can start getting busy in PocketID:

- Administration menu
- OIDC client
- Add OIDC client
- Name "cloudflare secured site"
- Callback URL is based on your Cloudflare URL you saved earlier
- It should be `https://xyz.cloudflareaccess.com/cdn-cgi/access/callback` in this example
- Client launch URL should be `https://xyz.cloudflareaccess.com`
- Tick PKCE
- Upload a logo if you'd like
- Capture the client ID and client secret
- In Allowed User group either set a group or click unrestricted (means anyone logged into PocketID can access the secured website)

Now go to [Cloudflare Zero Trust](https://one.dash.cloudflare.com).

Choose your account.

Create an identity provider:

- Integration 
- Identity providers
- Add an identity provider
- Select OpenID Connect as the identity provider
- Name: `PocketID`
- App ID: paste Client ID saved earlier
- Client secret: paste Client secret earlier
- Auth URL: `https://id.example.com/authorize`
- Token URL: `https://id.wains.be/api/oidc/token`
- Certificate URL: `https://id.wains.be/.well-known/jwks.json`
- Tick PKCE
- Save
- Test: it should quickly connect on PocketID and say "Your connection works!" with some details about your PocketID user.

Still in Cloudflare Zero trust, create an application:

- Go to Access Control > Applications
- Add an application
- Self-hosted
- Enter an application name: `myapp`
- Session duration: `24 hours`
- Application domain: specify the address of your currently exposed service
- Identity providers: untick "accept all available identity providers" and select only "OpenID Connect PocketID"
- Tick "Apply instant authentication" (unless you have several login methods)
- Next
- Policy name: allow
- Action: allow
- Session duration: same as application
- Selector: Everyone
- Next
- Add

Go to your website. 

Cloudflare will redirect you to PocketID. 

Log into your account with your passkey saved earlier.

You can now access your website! 
