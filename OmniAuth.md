OmniAuth
========

- Gem provides two routes for you:
  - `/auth/:provider`
  - `/auth/:provider/callback`
    - takes the user back to the app once they've authenticated their info
    - The 'provider' is the one doing the authorization i.e. :provider is 'github'
