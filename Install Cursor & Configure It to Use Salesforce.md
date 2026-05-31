# Install Cursor & Configure It to Use Salesforce (Windows)

A step-by-step guide for a **Windows** user to install **Cursor**, set it up to use **Claude**, and add the **Salesforce Extension Pack** so you can develop against a Salesforce org from the editor.

---

## What is Cursor?

Cursor is an AI-powered code editor built on top of VS Code. It looks and feels like VS Code, but has a built-in AI assistant (which can be powered by **Claude**) that can read your code, answer questions, and make edits.

---

## Step 1 — Download & install Cursor

1. Go to **[cursor.com](https://cursor.com)** in your browser.
2. Click **Download** — it auto-detects Windows and gives you a `.exe` installer (Windows 10/11, x64).
3. Run the downloaded file (e.g., `Cursor Setup x.x.x.exe`) from your **Downloads** folder.
4. Follow the installer prompts. It installs per-user by default (no admin rights needed) and adds a desktop/Start-menu shortcut.
5. Launch **Cursor** from the Start menu.

---

## Step 2 — Initial setup

On first launch, Cursor walks you through a few quick screens:

| Prompt | What to choose |
|--------|----------------|
| **Sign in / Create account** | Sign in with Google, GitHub, or email. A free account works to start. |
| **Keyboard shortcuts** | Pick VS Code (default) if you're used to it. |
| **Import from VS Code** | Optional — imports your extensions, themes, and settings. |
| **Install `cursor` command** | Say yes — lets you open folders from the terminal with `cursor .` |

---

## Step 3 — Set up Claude as your AI model

1. Open Cursor's **Settings**:
   - Press `Ctrl+Shift+J`, **or**
   - Click the **gear icon** (top-right) → **Cursor Settings**.
2. Go to the **Models** section.
3. Enable **Claude** models (e.g., the latest Claude Sonnet/Opus options).
4. Open the AI chat panel with **`Ctrl+L`** (chat) or **`Ctrl+I`** (inline edit).
5. At the **bottom of the chat box**, use the **model picker dropdown** to select **Claude**.

> **Billing/API keys:** With a Cursor **Pro** subscription, Claude models are included — no API key needed. To use your own key instead, go to **Settings → Models → API Keys** and paste a key from the [Anthropic Console](https://console.anthropic.com).

---

## Step 4 — Open a project and start using Claude

1. **File → Open Folder** (`Ctrl+K Ctrl+O`) and pick a code folder.
2. Press **`Ctrl+L`** to open chat, confirm **Claude** is selected, and ask away.

| Shortcut | What it does |
|----------|--------------|
| `Ctrl+L` | Open the AI chat |
| `Ctrl+I` | Inline edit selected code |
| `Ctrl+K` | Quick edit/generate at cursor |
| `Tab` | Accept AI autocomplete |

---

## Step 5 — Install the Salesforce Extension Pack

The **Salesforce Extension Pack** (published by Salesforce) adds Apex language support, an Org Browser, a test runner, and deploy/retrieve commands inside Cursor.

### 5a. Open the Extensions panel

- Click the **Extensions icon** in the left sidebar, **or**
- Press **`Ctrl+Shift+X`**.

### 5b. Search and install

1. Search for **`Salesforce Extension Pack`**.
2. Find the result published by **Salesforce** (bundles Apex, SOQL, Lightning, Org Browser, etc.).
3. Click **Install** and wait for it to finish.

> **Marketplace note:** Cursor uses the Open VSX registry by default. If the official pack doesn't appear, install the core piece **`salesforcedx-vscode-apex`**, or download the `.vsix` from the [Salesforce extensions page](https://marketplace.visualstudio.com/items?itemName=salesforce.salesforcedx-vscode) and use **Extensions panel → "..." menu → Install from VSIX...**.

### 5c. Install the prerequisite: Java (JDK)

The Apex Language Server requires Java:

1. Download a JDK — [Temurin 17](https://adoptium.net/) is a solid free choice.
2. Run the installer (defaults are fine).
3. In Cursor, open **Settings** (`Ctrl+,`), search for **`salesforcedx-vscode-apex.java.home`**, and set it to your JDK path, e.g.:

```
C:\Program Files\Eclipse Adoptium\jdk-17.0.x.x-hotspot
```

4. **Restart Cursor** so the Apex Language Server picks up Java.

### 5d. Verify it's working

1. Open your Salesforce project folder (the one with `sfdx-project.json`).
2. Open any `.cls` file (e.g., `AccountService.cls`).
3. You should see:
   - **Syntax highlighting** and **code completion** for Apex.
   - **`SFDX:`** commands in the Command Palette (`Ctrl+Shift+P` → type `SFDX`).
   - An **Org Browser** (cloud) icon in the left sidebar.
   - Right-click a `.cls` → **"SFDX: Deploy Source to Org"** / **"Retrieve Source from Org"**.

### 5e. How it works with the CLI

The extension pack is a front-end for the **Salesforce CLI (`sf`)**. Make sure the CLI is installed and your sandbox is authorized (`sf org login web --instance-url https://test.salesforce.com --alias myOrg`). Then:

| Do this in Cursor's UI | Equivalent CLI command |
|------------------------|------------------------|
| Right-click → **SFDX: Deploy Source to Org** | `sf project deploy start` |
| Right-click → **SFDX: Retrieve Source from Org** | `sf project retrieve start` |
| Command Palette → **SFDX: Run Apex Tests** | `sf apex run test` |
| Sidebar **Org Browser** → browse/retrieve metadata | `sf project retrieve start --metadata ...` |

---

## Putting it all together

1. Install **Cursor**
2. Sign in & pick **Claude**
3. Install the **Salesforce CLI**
4. Install the **Salesforce Extension Pack** + **Java (JDK)**
5. Authorize the sandbox (`sf org login web ...`)
6. Deploy/retrieve from inside the editor

> See **`salesforce local dev.md`** in this project for the full CLI install and sandbox connection details.

---

## Windows-specific tips

- **Windows Defender / SmartScreen** may warn on first run — click **More info → Run anyway** (Cursor is signed).
- **`cursor` command not found in terminal:** reopen the terminal, or re-run *"Install 'cursor' command"* from the Command Palette (`Ctrl+Shift+P`).
- **Open the project near the drive root** (e.g., `C:\dev\apex`) to avoid Windows' 260-character path limit with deeply nested metadata.
