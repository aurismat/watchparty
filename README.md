# watchparty
Docker container setup to easily run a watchparty from your homelab!

Using [SyncTube](https://github.com/RblSb/SyncTube) behind a Caddy gate,
with easily swappable key to gate users in and out on demand.

## Why?

Why would one use this, when absolutely *perfect* solutions like Discord exist?

Well:
- Discord's screen-sharing may not work perfectly(esp true with Linux setups);
- Discord's screen-share is inherently compressed, decreasing quality;
- One person needs to host the watchparty on Discord, meaning they are in ultimate
control of scroll/watch quality(plus it means they have to be present).

With this solution, each viewer gets streamed the *original* copy of whatever is being shared,
meaning everyone watches the exact same quality, all synced up!(assuming everyone's internet can take it)

## Requirements

- Docker with the compose plugin;
- **Cloudflare tunnel** pointing to your hostname/domain(recommended, alternative available - see below);

## Setup

1. Set up a Cloudflare tunnel as per their instructions (should be their dashboard -> Zero Trust -> Networks -> Tunnels), pointing to `http://localhost:18696`;
2. Edit the hostname in the last line of `new-key` script to match your hostname/(sub)domain;
3. Set your media dir in `docker-compose.yml`;
4. Generate a new key and start caddy:
   ```sh
   ./new-key
   ```
   This will write `WATCH_KEY` to `.env`, (re)starts `caddy` and prints out the invite link.
5. Start SyncTube (first-run builds it from GitHub):
   ```sh
   docker compose up -d
   ```
6. Have fun watching!

## Rotating the key

Run `./new-key` again. Old links and cookies stop working immediately; send
out the new link.

## Alternative: tunnel built in (no Cloudflare account)

`docker-compose.tunnel.yml` adds a `cloudflared` container using a free
[Quick Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/do-more-with-tunnels/trycloudflare/)
(`*.trycloudflare.com`). No account or domain needed, but:

- the hostname is random and **changes every time the container restarts**;
- it's meant for testing — no uptime guarantee, ~200 concurrent request limit.

```sh
./new-key   # ignore the printed host, the key is what matters
sudo docker compose -f docker-compose.yml -f docker-compose.tunnel.yml up -d
sudo docker compose -f docker-compose.yml -f docker-compose.tunnel.yml logs cloudflared | grep -o 'https://[a-z0-9-]*\.trycloudflare\.com'
```

Invite link is `<that URL>/join/<key from .env>`. Re-running `./new-key` only
restarts Caddy, so the tunnel URL stays the same.

## Disclaimer

This project only provides configuration for self-hosting a synced player; it
does not provide, host, link to, or endorse any media. You are solely
responsible for the content you serve with it and for having the rights to
share it with your viewers. The authors accept no liability for how it is used,
including any copyright infringement. See [LICENSE](LICENSE).
