# Windows OneBot Checklist

## Typical Local Files

- OpenClaw config:
  `%USERPROFILE%\.openclaw\openclaw.json`
- NapCat OneBot config:
  `%LOCALAPPDATA%\NapCatQQ\shell\config\onebot11.json`
- NapCat per-account OneBot config:
  `%LOCALAPPDATA%\NapCatQQ\shell\config\onebot11_<self-qq>.json`
- NapCat QQNT metadata:
  `%LOCALAPPDATA%\NapCatQQ\shell\qqnt.json`
- NapCat logs:
  `%LOCALAPPDATA%\NapCatQQ\shell\logs\*.log`

## Minimum PowerShell Checks

Confirm QQ and NapCat processes:

```powershell
Get-Process QQ, NapCatWinBootMain -ErrorAction SilentlyContinue
```

Read OpenClaw and NapCat config:

```powershell
Get-Content $env:USERPROFILE\.openclaw\openclaw.json
Get-Content $env:LOCALAPPDATA\NapCatQQ\shell\config\onebot11.json
Get-Content $env:LOCALAPPDATA\NapCatQQ\shell\config\onebot11_<self-qq>.json
```

Probe local listeners:

```powershell
Test-NetConnection 127.0.0.1 -Port 3001
Test-NetConnection 127.0.0.1 -Port 3002
Test-NetConnection 127.0.0.1 -Port 18789
```

Probe OpenClaw channel state:

```powershell
openclaw channels status --probe
```

Check NapCat HTTP status with the current access token:

```powershell
$token = "<access-token>"
$headers = @{ Authorization = "Bearer $token" }

Invoke-RestMethod `
  -Method Post `
  -Uri http://127.0.0.1:3002/get_status `
  -Headers $headers `
  -ContentType 'application/json' `
  -Body '{}'

Invoke-RestMethod `
  -Method Post `
  -Uri http://127.0.0.1:3002/get_login_info `
  -Headers $headers `
  -ContentType 'application/json' `
  -Body '{}'
```

## Log Signatures To Accept

Bridge and probe recovery:

- NapCat local listener is reachable
- `openclaw channels status --probe` reports `Gateway reachable`
- OneBot shows `enabled, configured, running, connected`

Valid outbound proof:

- OpenClaw `message send` succeeds
- NapCat logs a corresponding send event to the approved target

Valid inbound proof:

- NapCat logs an incoming private or group message from an allowlisted external source
- NapCat converts it into OneBot `post_type:"message"`
- OpenClaw logs receipt of that same inbound event

## Cases To Reject

- Self-send acceptance where NapCat logs only `message_sent`
- Any test that requires opening public ingress
- Any validation that widens the allowlist beyond the approved target

## Common Failure Pattern

If QQ starts reporting file-integrity or file-corruption style errors after a QQNT update, compare the installed QQNT build with NapCat's local `qqnt.json`. If NapCat's QQNT metadata is stale, align the local metadata first, keep a backup, then retry the bridge startup before touching OpenClaw.
