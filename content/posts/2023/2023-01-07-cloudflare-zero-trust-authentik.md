---
date: 2023-01-07
title: "Single Sign On with Authentik for your services behind Cloudflare zero trust"
---

Assumptions:

- you are already exposing a simple website `service.example.com` through Cloudflare Zero Trust
- this service is currently publicly accessible with no authentication whatsoever
- Authentik is already installed (it's really just a `.env` and a `docker-compose.yml`) and available at `https://auth.example.com` (behind Cloudflare too, in my case).
- you want to put the service behind an OpenID connect authentication
- you want any authenticated Authentik user to access the webpage

In Authentik create a new provider:

- Application > Providers > Create
- Name: `Cloudflare`
- Authorization flow: `default-provider-authorization-implicit-consent`
- Client type: `Confidential`
- Client ID: copy and save
- Client Secret: copy and save
- Redirect URI/s: `https://YOURTEAM.cloudflareaccess.com/cdn-cgi/access/callback`
- Signing Key: `authentic Self-signed Certificate`

Save.

IMPORTANT: If you don't choose the signing key you'd likely get an error "Failed to fetch user/group information from the identity provider"

Still in Authentik, create an application:

- Name: `Cloudflare`
- Slug: `cloudflare`
- Provider: `Cloudflare`
- Policy engine mode: `ANY`

Save.

In [Cloudflare Zero Trust](https://one.dash.cloudflare.com).

Create an identity provider

- Settings > Authentication
- Add new
- Name: `Authentik`
- App ID: paste Client ID saved earlier
- Client secret: paste Client secret earlier
- Auth URL: `https://auth.example.com/application/o/authorize/`
- Token URL: `https://auth.example.com/application/o/token/`
- Certificate URL: `https://auth.example.com/application/o/cloudflare/jwks/` (the `cloudflare` part of the URL is the `slug` defined in the previous step! Update accordingly.)
- Save
- Test: it should quickly connect to Authentik and say "Your connection works!"

Create an application

- Go to Access > Applications
- Add an application
- Self-hosted
- Enter an application name: `myapp`
- Session duration: `24 hours`
- Application domain: specify the address of your currently exposed service (found in Access > Tunnels > )
- Identity providers: untick "accept all available identity providers" and select only "OpenID Connect Authentik"
- Tick "Skip identity provider selection if only one is configured"
- Next
- Policy name: allow
- Action: allow
- Session duration: same as application
- Selector: Everyone
- Next
- Add

Go to your website. You will be redirect to Authentik to something like `https://auth.example.com/if/flow/default-authentication-flow/...`

If not already authenticated in Authentik, do so.

You can now see your website. Your website is now secure!