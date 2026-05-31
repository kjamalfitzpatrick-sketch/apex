# Salesforce Local Development Setup (Windows)

This guide walks through connecting a local **Cursor** development environment on **Windows** to a **Salesforce sandbox**, so you can write Apex code locally and deploy changes to your org.

## The big picture

```
┌─────────────────────────┐         ┌──────────────────────────┐
│   Cursor (Windows)      │  deploy │   Salesforce Sandbox      │
│  apex\ project  ───────────────▶  │  Apex classes, triggers   │
│  (your .cls files)      │ retrieve│                           │
│                 ◀───────────────  │                           │
└─────────────────────────┘         └──────────────────────────┘
        │ Salesforce CLI (`sf`) talks to the org via OAuth
```

The three tools that make this work:

1. **Salesforce CLI (`sf`)** — the engine that deploys/retrieves.
2. **Salesforce Extension Pack** (in Cursor) — org browser, test runner, syntax highlighting.
3. **Your SFDX project** — `sfdx-project.json` + `force-app/`.

---

## Step 1 — Install the Salesforce CLI

Pick **one** option:

| Option | Command / Action | Notes |
|--------|------------------|-------|
| **A. Installer (.exe)** | Download the Windows `x64` installer from the [official CLI page](https://developer.salesforce.com/tools/salesforcecli) and run it | Simplest; no extra tooling |
| **B. winget** (Win 10/11) | `winget install Salesforce.sf` | Built into modern Windows |
| **C. npm** | `npm install --global @salesforce/cli` | Only if you have Node.js installed |

Open a **new** PowerShell or Windows Terminal window afterward and verify:

```powershell
sf --version
```

> If you get `sf : The term 'sf' is not recognized...`, the install didn't add it to your PATH—close and reopen the terminal, or re-run the installer.

---

## Step 2 — Install the Salesforce Extension Pack in Cursor

1. Open Extensions (`Ctrl+Shift+X`).
2. Install **"Salesforce Extension Pack"** (publisher: Salesforce).

> The Apex Language Server needs **Java**. Install a JDK (e.g., [Temurin 17](https://adoptium.net/)), then point Cursor at it via the setting `salesforcedx-vscode-apex.java.home`, e.g.:
>
> ```
> C:\Program Files\Eclipse Adoptium\jdk-17.0.x.x-hotspot
> ```

---

## Step 3 — Authorize (connect to) your sandbox

Run in PowerShell or Command Prompt:

```powershell
sf org login web --instance-url https://test.salesforce.com --alias myOrg --set-default
```

- `--instance-url https://test.salesforce.com` → **required for sandboxes** (production uses `login.salesforce.com`).
- This opens your browser; log in with your sandbox credentials (username often ends in `.sandboxname`). Only you can complete this login.

Confirm:

```powershell
sf org display --target-org myOrg
sf org list
```

---

## Step 4 — Sync your project with the sandbox

```powershell
# Pull existing metadata down (e.g., all Apex classes)
sf project retrieve start --metadata "ApexClass" --target-org myOrg

# Or push your local sample project up
sf project deploy start --target-org myOrg
```

---

## Step 5 — Day-to-day development loop

```
Edit .cls in Cursor → Deploy → Run tests → Retrieve UI changes → repeat
```

| Action | Command (PowerShell) |
|--------|----------------------|
| Deploy changed files | `sf project deploy start --target-org myOrg` |
| Preview a deploy | `sf project deploy start --dry-run --target-org myOrg` |
| Deploy one file | `sf project deploy start -d force-app\main\default\classes\AccountService.cls` |
| Run all local tests | `sf apex run test --test-level RunLocalTests --code-coverage --result-format human` |
| See diffs vs. org | `sf project deploy preview --target-org myOrg` |
| Pull org changes | `sf project retrieve start --target-org myOrg` |

> **Path note:** Windows uses backslashes (`force-app\main\default\...`). In PowerShell forward slashes usually work too, but backslashes are safest.

---

## Windows-specific gotchas

- **Use a new terminal after install** so PATH changes take effect (most common "command not found" cause).
- **PowerShell execution policy:** if scripts are blocked, allow signed scripts with:

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

- **Line endings:** Windows uses CRLF. Salesforce handles this fine, but if you use Git, consider a `.gitattributes` with `* text=auto` to avoid noisy diffs.
- **Long paths:** deeply nested metadata can exceed Windows' 260-char limit. Keep your project folder near the drive root (e.g., `C:\dev\apex`) to be safe.
