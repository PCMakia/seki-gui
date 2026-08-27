# seki-gui

Desktop **CustomTkinter** chat client for `seki-agent-core`. It sends `POST /agent/chat` and can open the same process’s **vault** in your browser.

## What this solves (and why it matters)

The agent is an HTTP service. This repo is the local window: type, get a reply, see health, inspect CLS-M metrics, and jump to the graph UI without running Discord.

Give it a try if you want a **keyboard-first** client on Windows, or to debug prompts (`reasoning_meta` and the topic-head cache log to the terminal after each send) while the vault shows which nodes fired.

It is not a web app. It does not start vLLM. Scheduling (`/schedule ...`) needs the Outlook bridge on agent-core.

## Hardware and prerequisites

No GPU for the GUI process.

**Install before first run**

- Python 3.10+
- Running **seki-agent-core** (host port **9080** in the mesh, or 8000 if you launched the agent with uvicorn)
- `pip` packages in `requirements-gui.txt` (`customtkinter`, `httpx`)

Windows: CustomTkinter needs a desktop session (not a headless SSH box).

## Fresh install

```powershell
git clone https://github.com/PCMakia/seki-gui.git
cd seki-gui
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements-gui.txt
```

Point at the agent (mesh default):

```powershell
$env:AGENT_BASE_URL = "http://127.0.0.1:9080"
$env:GUI_SESSION_ID = "default"
python -m src.GUI.gui_main
```

If `AGENT_BASE_URL` is unset, the client uses `http://127.0.0.1:8000`. `localhost` is rewritten to IPv4 loopback to avoid `::1` resets on Docker Desktop.

Optional: `APP_THEME` (`system` / `dark` / `light`).

## How to use it

1. Confirm status bar: inference ready and a model name, not “API unreachable”.
2. Type a message and press Enter. The transcript is local to the window; durable memory is agent-core episodes for `GUI_SESSION_ID`.
3. **Open vault** loads `{AGENT_BASE_URL}/vault/` in the browser (graph, backlinks, episodes).
4. **Show memory metrics** prints the latest CLS-M sample into the transcript.
5. **Clear chat** only clears the widget, not SQLite.
6. `/schedule <task>` hits `/agent/schedule` for the Outlook debug path.

Keep `GUI_SESSION_ID` stable if you want the reasoning cache and episodes to accumulate across launches.
