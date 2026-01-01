# AIchat

A lightweight **Streamlit** group chat prototype with **shared persistence (SQLite)** and an optional **AI facilitator** (OpenAI API).

<img width="792" height="870" alt="image" src="https://github.com/user-attachments/assets/f9c5a506-71a3-4241-be6e-a8bca7d2ebaf" />

This repo exists primarily as an **evaluation harness** for testing a specific product hypothesis:

> Does an **always-on AI facilitator** in a text-based group chat produce measurable learning/discussion gains — or does it interrupt flow and reduce quality?

📌 Related case study: **Learning Outcomes Over Adoption: Why We Didn’t Ship an Always-On AI Facilitator**  
https://github.com/Leoendithas/AI-PM-Case-Studies/blob/main/outcomes-over-adoption.md

🚧 Predecessor to **Lumina** (voice-first facilitator prototype): https://github.com/adoreblvnk/lumina/tree/main/app

---

## What it is
- A multi-user chat room where participants join by entering a username
- Messages persisted in a local SQLite DB (`chat.db`)
- Auto-refreshes so the chat updates live
- Optional AI facilitator that periodically posts a short “facilitation move”
- One-click export of chat logs to **.docx**

## What it isn’t
- A production-ready system (no auth, no deployment hardening, no analytics pipeline)
- A validated learning intervention (this is a **prototype for user testing**)
- A “smart” facilitator that can choose to stay quiet (see limitation below)

---

## Key limitation (important for the evaluation)
**Always-on behaviour is baked into the prototype.**  
When AI is enabled, it posts in the chat **every N user messages** (currently every 10).  
This means the AI **cannot choose to stay silent** even when students are already having a productive discussion.

This constraint was intentional for testing the downside risk of periodic interventions in a fast-moving group chat.

---

## Features
- **Shared chat persistence** using SQLite (`chat.db`)
- **Auto-refresh** (~2 seconds) via `streamlit_autorefresh`
- **AI facilitator** toggle (sidebar)
- **Active member count + list** (unique usernames)
- Controls:
  - Clear conversation
  - Export conversation to Word (`.docx`)

---

## Repo contents
- `app2.py` — Streamlit app (UI + SQLite + OpenAI call + Word export)
- `requirements.txt` — dependencies

---

## Quick start

### 1) Install
```bash
git clone https://github.com/Leoendithas/AIchat.git
cd AIchat

python -m venv .venv
# macOS / Linux
source .venv/bin/activate
# Windows (PowerShell)
.venv\Scripts\Activate.ps1

pip install -r requirements.txt
````

> Recommended: add `streamlit` to `requirements.txt` (the app imports it).

### 2) Configure OpenAI API key (Streamlit secrets)

The app expects:

```py
st.secrets["api_keys"]["openai"]
```

Create:

* `.streamlit/secrets.toml`

Example:

```toml
[api_keys]
openai = "YOUR_OPENAI_API_KEY"
```

> Don’t commit secrets. Add `.streamlit/secrets.toml` to `.gitignore`.

### 3) Run

```bash
streamlit run app2.py
```

---

## How the AI facilitator works

* Toggle **Enable AI** in the Streamlit sidebar
* When enabled, the app calls the OpenAI API periodically
  (currently **every 10 user messages**, excluding the assistant)

The facilitator is prompted to:

1. identify themes in the chat so far
2. provide a short summary
3. ask a guiding question (engagement / steering / clarification, etc.)

Model in code: `gpt-4o-mini`

---

## Reproducing the evaluation setup (recommended)

If you’re using this for a small-group test:

1. Have 3–4 participants join with different usernames (separate browsers/devices)
2. Run a discussion round **without** AI (toggle off)
3. Run a discussion round **with** AI on
4. Export the logs to Word for qualitative review

---

## Discussion topic (hardcoded)

The discussion prompt is currently hardcoded in `app2.py`:

> “Should there be aircon in school classrooms? Discuss in your group and come up with 3 reasons to support your group's stand.”

To change:

```py
discussion_topic = """..."""
```

---

## Data storage (SQLite)

Messages are stored in:

* `chat.db`

Schema (created on startup):

* `messages(id, user, content, timestamp)`

Deployment note: on many hosted platforms, the filesystem can be ephemeral, so `chat.db` may reset between restarts.

---

## Exporting to Word

Click **Export Conversation to Word** to download a `.docx` containing:

* `username: message` in chronological order

---

## Notes / gotchas

* Assistant username stored in DB: `GPT4o` (used for filtering message counts and active members)
* Username is stored in Streamlit session state; different browsers/sessions can join as different users
* Auto-refresh interval:

```py
st_autorefresh(interval=2000, key="chat_refresh")
```

---

## Dependencies

From `requirements.txt`:

* `openai`
* `python-docx`
* `streamlit_autorefresh`

Also required:

* `streamlit`

---

## License

Licensed under the **Apache License 2.0**. See `LICENSE` for details.
