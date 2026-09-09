# Running the VAPT Remediation Tool on Windows

Good news: the tool is a **single static HTML file** (`index.html`) with pure
client-side JavaScript. There is **no build step, no server, and no dependencies**.
Everything the browser needs is already in the file, so it runs on Windows exactly
the same as on Linux or macOS. No code changes are required to run it.

This guide covers the Windows-specific bits: how to get the file, how to open it,
and the one path difference to be aware of in the *generated output*.

---

## 1. Get the Tool

### Option A — Git (recommended)

Install [Git for Windows](https://git-scm.com/download/win), then in PowerShell or
Command Prompt:

```powershell
git clone <repo-url>
cd vapt-tool
```

### Option B — Download ZIP (no Git needed)

1. Open the repo on GitHub.
2. Click **Code ▸ Download ZIP**.
3. Right-click the ZIP ▸ **Extract All…** and pick a folder.

---

## 2. Open the Tool

Any of these work. Pick whichever you like.

### Just double-click it

Open the extracted folder in **File Explorer** and **double-click `index.html`**.
It opens in your default browser. That's it.

### From a terminal

PowerShell:

```powershell
start index.html
```

Command Prompt (cmd.exe):

```cmd
start index.html
```

To force a specific browser:

```powershell
start chrome index.html
start msedge index.html
```

### Drag and drop

Drag `index.html` from File Explorer into an open browser window.

---

## 3. (Optional) Serve it locally

Not required, but if you prefer `http://localhost` over a `file://` path (for
example, some corporate browser policies restrict local files):

If you have **Python** installed ([python.org](https://www.python.org/downloads/windows/),
tick **"Add python.exe to PATH"** during install):

```powershell
python -m http.server 8000
```

Then visit `http://localhost:8000/index.html`.

> On Windows the command is `python`, not `python3`.

If you have **Node.js**:

```powershell
npx serve .
```

---

## 4. Using the Tool

Usage is identical on every OS. See [SETUP.md](SETUP.md) for the full walkthrough.
Short version:

1. Fill in the repo name, environment ↔ branch mapping, and Devtron config.
2. Paste or upload the Wiz CSV report.
3. Click **Generate Context File**.
4. **Copy to Clipboard** or **Download as .md**.
5. Paste the result into a fresh AI chat session.

Clipboard copy, file upload, and `.md` download all use standard browser APIs and
work the same in Chrome, Edge, and Firefox on Windows.

---

## 5. The One Windows Difference: the Tracker Path

The tool is fully cross-platform, but the **text it generates** contains a hardcoded
Linux path for the Excel tracker, e.g.:

```
/home/kushagra/Documents/Repositories/Java/vapt-tool/VAPT_Team_Tracker.xlsx
```

This is instruction text for the AI agent, not something the tool executes. When you
run the remediation on Windows, just point the agent at wherever *your* tracker lives,
using a Windows path, for example:

```
C:\Users\<you>\Documents\vapt-tool\VAPT_Team_Tracker.xlsx
```

Two ways to handle it:

- **Easiest:** after generating the context, replace the Linux path in the text with
  your Windows path before pasting it into the AI chat. Or simply tell the agent your
  tracker's real path in the chat.
- **Permanent:** edit `index.html`, find the tracker path inside `getSystemPrompt()`
  and the `generate()` function, and change it to your path. It appears twice.

If you use `openpyxl` for the tracker step, install it the same way as on Linux:

```powershell
pip install openpyxl
```

---

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| Double-click opens an editor, not a browser | Right-click `index.html` ▸ **Open with** ▸ your browser. |
| `start` says command not found | You're likely in a non-standard shell; use File Explorer double-click instead. |
| Clipboard copy does nothing | Some browsers block clipboard access over `file://`. Use the local server option (section 3) so it runs over `http://localhost`. |
| `python` not recognized | Reinstall Python and tick **"Add python.exe to PATH"**, or use the File Explorer double-click method (no Python needed). |
| CSV upload shows nothing | Make sure you exported the **Detailed CSV** and the file isn't open/locked in Excel. |

---

## Summary

- **No code changes are needed to run the tool on Windows** — it's static HTML/JS.
- Double-click `index.html` or run `start index.html`.
- The only Windows consideration is swapping the Linux tracker path in the *generated
  output* for your own Windows path.
