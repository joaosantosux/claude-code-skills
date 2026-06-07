# PowerShell UTF-8 Encoding Fix for API Requests

## About this skill

**What it does:** Diagnoses and fixes UTF-8 encoding corruption in PowerShell 5.1 scripts that send JSON to REST APIs — the issue where accented characters (á, é, ã, ú, ç) arrive as `?` or `°` on the receiving system.

**Who it's for:** Anyone on Windows using PowerShell 5.1 to integrate with REST APIs, CRMs, webhooks, or any HTTP endpoint that expects UTF-8 encoded JSON.

**Why it was created:** Discovered while building a GoHighLevel CRM integration — custom field names and contact data with Portuguese accented characters were arriving corrupted. Hours of debugging pointed to a single root cause: PowerShell 5.1's default encoding is Windows-1252, not UTF-8. The fix is a two-line change, but the problem is invisible and non-obvious. This skill exists so no one has to rediscover it from scratch.

**Benefit:** Instead of spending hours wondering why your data looks fine locally but arrives broken in the API, run `/powershell-utf8-fix`, get an instant diagnosis, and apply the correct fix in minutes.

---

## When to use this skill

Invoke with `/powershell-utf8-fix` when:
- Characters like á, é, ã, ú, ç appear as `?`, `°`, or other strange symbols in the target system (CRM, API, database, webhook)
- You are running **PowerShell 5.1** (default on Windows) and making POST or PUT requests with JSON bodies
- Any `Invoke-RestMethod` or `Invoke-WebRequest` call that sends a `-Body` with accented characters

## Diagnosis

First, check the PowerShell version:

```powershell
$PSVersionTable.PSVersion
```

- **Major = 5** → you have the problem. Apply the fix below.
- **Major = 7+** → UTF-8 is default. No fix needed.

## Root cause

PowerShell 5.1 uses Windows-1252 encoding by default when building HTTP request bodies. Modern APIs expect UTF-8. The byte mismatch causes accented characters to arrive corrupted on the receiving end.

## The fix

**Before (broken):**
```powershell
$json = @{ name = "Campo com Ação" } | ConvertTo-Json
Invoke-RestMethod -Uri $url -Headers $headers -Method POST -Body $json
```

**After (correct):**
```powershell
$json  = @{ name = "Campo com Ação" } | ConvertTo-Json
$bytes = [System.Text.Encoding]::UTF8.GetBytes($json)
Invoke-RestMethod -Uri $url -Headers $headers -Method POST -Body $bytes
```

The only change: convert `$json` to a UTF-8 byte array before passing it as `-Body`. Apply this to **every** POST and PUT request that includes text with accented characters.

## How to apply to existing scripts

Search the script for every `-Body $json` or `-Body $payload` pattern and replace with the two-line version:

```powershell
# Replace this pattern anywhere it appears:
-Body $json

# With this:
$bytes = [System.Text.Encoding]::UTF8.GetBytes($json)
# ...then use -Body $bytes
```

## Permanent solution: upgrade to PowerShell 7

PowerShell 7+ uses UTF-8 by default and is free. Install via:

```powershell
winget install --id Microsoft.PowerShell --source winget
```

It installs **side by side** with 5.1 — does not replace it. After installing, use `pwsh` instead of `powershell` to launch it. No workaround needed in version 7+.

## This fix applies to any system

This is not specific to GoHighLevel or any particular API. The same fix applies whenever PowerShell 5.1 sends JSON to:
- Any REST API (HubSpot, ClickUp, ActiveCampaign, Asana, etc.)
- Webhooks
- Databases via HTTP
- File creation via API

The problem is in PowerShell, not in the receiving system.
