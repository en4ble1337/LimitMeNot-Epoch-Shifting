# LimitMeNot: Epoch Shifting

> A simple timing strategy for services with rolling 5-hour session quotas.

![LimitMeNot — Epoch Shifting](./assets/limitmenot-epoch-shifting.jpeg.jpeg)

## The idea

If a service starts a **5-hour rolling quota window** on the first interaction, you can pre-warm the session on a fixed schedule so the window starts at a predictable time.

Use:

```cron
15 */6 * * *
```

That runs at **00:15, 06:15, 12:15, and 18:15** every day.

The goal is simple: when you start working at an unpredictable time, you may already be inside a known 5-hour window, giving you a predictable reset during the work session.

> **Important:** This only works if the provider actually ties the quota window to the interaction. Some services use fixed account limits, token budgets, server-side rate limits, or anti-abuse controls instead.

## Copy-paste prompt: Codex Scheduled Tasks / Claude Routines

Replace `https://example.com/` and `example.com` with the site you want to monitor.

```text
You are the uptime check for https://example.com/ — run it and report.

1. Run exactly:
   curl -sS -L -o /dev/null --max-time 20 \
     -w "status=%{http_code} time=%{time_total}s final_url=%{url_effective}\\n" \
     https://example.com/
   If it fails or returns non-200, wait 10 seconds and run it once more.

2. Healthy = HTTP 200 in under 10 seconds.

3. If healthy: do NOT send a notification. Finish quietly.

4. If unhealthy (non-200, curl error, timeout, or both attempts fail):
   send a PushNotification. First sentence: "example.com is DOWN: <status or error>".
   Then include the exact curl output and the UTC time.
```

### Recommended schedule

For a fixed 6-hour cadence:

```cron
15 */6 * * *
```

### Why `*/6`?

Six hours divides evenly into 24 hours, so the schedule stays fixed every day instead of drifting.

## License

Free for anyone to use, modify, and distribute under the **MIT License**. See [LICENSE](./LICENSE).
