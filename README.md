# Railway Lavalink

This repository deploys Lavalink 4 with the Lavalink YouTube source plugin on Railway.

## Railway deployment

1. Create a Railway project from this GitHub repository.
2. Deploy the service using the included `Dockerfile`.
3. Add the Railway variable `LAVALINK_PASSWORD` with a long random value. Never commit this value to Git.
4. Add the Railway secret variable `YOUTUBE_REFRESH_TOKEN` containing the OAuth refresh token for a separate Google account. Never commit this token to Git or post it in Discord.
5. Railway supplies `PORT` automatically. The server listens on `${PORT:2333}`.
6. Generate a public Railway domain from the service networking settings. Use the public HTTPS endpoint, normally port `443`, for the Discord bot.
7. Redeploy and inspect the deployment logs. Lavalink should initialize OAuth without an empty-token or authentication error.

## Bot configuration

Use the generated public Railway domain in the bot settings:

```json
{
  "lavalink": {
    "enabled": true,
    "name": "railway",
    "url": "YOUR-RAILWAY-DOMAIN:443",
    "secure": true
  }
}
```

Set the bot environment variable to the same value as Railway's `LAVALINK_PASSWORD`:

```env
LAVALINK_AUTH=YOUR_LAVALINK_PASSWORD
```

After changing the bot configuration, rebuild and restart it:

```bash
npm run build
pm2 restart moderation-bot --update-env
```

The bot should log:

```text
[music] Node ready: railway
```

## Validation

Replace the domain and password with values stored in your secret manager or shell environment:

```bash
curl -i \
  -H "Authorization: $LAVALINK_PASSWORD" \
  "https://YOUR-RAILWAY-DOMAIN/version"
```

A healthy deployment returns HTTP 200 and a Lavalink 4.x version. A 401 response means the service is reachable but the password is wrong.

## Security

Do not commit `LAVALINK_PASSWORD`, `YOUTUBE_REFRESH_TOKEN`, OAuth refresh tokens, Google credentials, or bot tokens. Store all credentials in Railway variables or the bot host's environment file. Rotate any credential that has been posted in a public channel.
