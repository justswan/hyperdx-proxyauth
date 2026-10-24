# hyperdx-proxyauth

A patch for [HyperDX](https://github.com/hyperdxio/hyperdx) that trusts an email header set by an
authenticating reverse proxy (Cloudflare Access, authentik) instead of HyperDX's own login.

- `AUTH_PROXY_EMAIL_HEADERS` — comma-separated header names; the first non-empty one is the user's email.
- `AUTH_PROXY_TEAM_NAME` — team (by name) the user is created in; unset → the single existing team.

Only run HyperDX behind a proxy that sets **and overwrites** that header.

## Releases

`release.yml` runs daily: it applies `proxy-auth.patch` to the latest upstream `@hyperdx/app@X`,
builds the `prod` image (not pushed) and publishes release `X` with the patch attached.
Consumers build their own images from the release.

## When the patch stops applying

```sh
git clone https://github.com/hyperdxio/hyperdx && cd hyperdx
git switch -c proxyauth '@hyperdx/app@X'
git am -3 ../hyperdx-proxyauth/proxy-auth.patch   # resolve conflicts, git am --continue
git format-patch -1 --stdout > ../hyperdx-proxyauth/proxy-auth.patch
```

Commit the new patch, then run `release` manually. A failed run opens a `broken` issue, which pauses the
daily schedule; a successful manual run closes it and the schedule resumes.
