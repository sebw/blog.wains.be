---
date: 2023-01-07
title: "Single Sign On with Authentik for your services behind Cloudflare zero trust"
---

Update Dec 25, 2024: steps updated for Authentik release 2024.12.1

Assumptions:

- you are already exposing a simple website `service.example.com` through Cloudflare Zero Trust
- this service is currently publicly accessible with no authentication whatsoever
- Authentik is already installed (it's really just a `.env` and a `docker-compose.yml`) and available at `https://auth.example.com` (behind Cloudflare too, in my case).
- you want to put the service behind an OpenID connect authentication
- you want any authenticated Authentik user to access the webpage

You will need to find the name of your Cloudflare team:

- Go to https://one.dash.cloudflare.com/
- Settings > Custom Pages
- Save the URL found under "Team domain", this will be used in the next step

In Authentik create a new provider:

- Application > Providers > Create
- Choose OAuth2/OpenID Provider
- Name: `Cloudflare`
- Authorization flow: `default-provider-authorization-implicit-consent`
- Client type: `Confidential`
- Client ID: copy and save
- Client Secret: copy and save
- Redirect URI/s (use your Team domain found in the previous step): 
  - Strict
  - `https://YOURTEAM.cloudflareaccess.com/cdn-cgi/access/callback`
- Signing Key: `authentic Self-signed Certificate`

Click Finish.

IMPORTANT: If you don't choose the signing key you'd likely get an error "Failed to fetch user/group information from the identity provider"

NOTE: At this point, the provider gives a warning "Provider not assigned to any application". Let's take care of that now.

Still in Authentik, go to Applications > Applications:

- Click Create
- Name: `Cloudflare`
- Slug: `cloudflare`
- Provider: `Cloudflare`
- Policy engine mode: `ANY`

Click "Create".

In [Cloudflare Zero Trust](https://one.dash.cloudflare.com).

Choose your account.

Create an identity provider:

- Settings > Authentication
- Under Login method, click "Add new"
- Select OpenID Connect as the identity provider
- Name: `Authentik`
- App ID: paste Client ID saved earlier
- Client secret: paste Client secret earlier
- Auth URL: `https://auth.example.com/application/o/authorize/`
- Token URL: `https://auth.example.com/application/o/token/`
- Certificate URL: `https://auth.example.com/application/o/cloudflare/jwks/` (the `cloudflare` part of the URL is the `slug` defined in the previous step! Update accordingly.)
- Save
- Test: it should quickly connect to Authentik and say "Your connection works!"

Still in Cloudflare Zero trust, create an application:

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
