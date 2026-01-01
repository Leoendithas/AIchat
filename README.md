# AIchat

A lightweight **Streamlit** chat room with **shared persistence (SQLite)** and an **AI facilitator** powered by the OpenAI API.  
Designed for classroom-style group discussions (ages ~12–15), with auto-refresh, active member tracking, and one-click export to Word.

<img width="792" height="870" alt="image" src="https://github.com/user-attachments/assets/f9c5a506-71a3-4241-be6e-a8bca7d2ebaf" />


This is an exploratory prototype used for user testing with real students to evaluate the usefulness of AI facilitators in human, text-based group discussions.  
Predecessor to **Lumina**, an AI voice-facilitator for group discussions: https://github.com/adoreblvnk/lumina/tree/main/app

---

## What it does

- **Multi-user shared chat** backed by `chat.db` (SQLite), so messages persist across refreshes.
- **Auto-refresh** (every ~2 seconds) so the chat updates live.
- **Optional AI facilitator** that summarizes themes and asks a guiding question to keep the discussion productive.
- **Active members** list + count (based on unique usernames).
- **Controls**
  - Clear conversation (wipes the table)
  - Export conversation to **.docx** (Word)

---

## Repository contents

- `app2.py` — Streamlit app (UI + SQLite + OpenAI call + Word export)
- `requirements.txt` — Python dependencies

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

> Note: `streamlit` is imported by the app but not listed in `requirements.txt`. If needed:
>
> ```bash
> pip install streamlit
> ```

### 2) Configure OpenAI API key (Streamlit secrets)

This app expects:

```python
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

### 3) Run the app

```bash
streamlit run app2.py
```

---

## How the AI facilitator works

* Toggle **Enable AI** in the Streamlit sidebar to turn AI responses on/off.
* When enabled, the app calls the OpenAI API periodically (currently **every 10 user messages**, excluding the assistant).
* The AI facilitator:

  1. Identifies themes in the chat so far
  2. Provides a short summary
  3. Asks a guiding question using facilitation criteria (engagement, steering, clarification, etc.)

Model used in code: `gpt-4o-mini`

---

## Discussion topic

The discussion prompt is currently hardcoded in `app2.py`:

> “Should there be aircon in school classrooms? Discuss in your group and come up with 3 reasons to support your group's stand.”

To change it:

```py
discussion_topic = """..."""
```

---

## Data storage (SQLite)

Messages are stored in a local SQLite database file:

* `chat.db`

Schema (created automatically on startup):

* `messages(id, user, content, timestamp)`

> Deployment note: on many hosted platforms, the filesystem can be ephemeral, so `chat.db` may reset between restarts.

---

## Exporting to Word

Click **Export Conversation to Word** to download a `.docx` containing:

* `username: message` for each message in chronological order.

---

## Notes / gotchas

* The assistant username stored in the DB is **`GPT4o`** (used to filter message counts and active members).
* Username is stored in Streamlit session state; different browsers/sessions can join by entering different usernames.
* Auto-refresh uses `streamlit_autorefresh`. You can adjust the interval here:

```py
st_autorefresh(interval=2000, key="chat_refresh")
```

---

## Dependencies

From `requirements.txt`:

* `openai`
* `python-docx`
* `streamlit_autorefresh`

(Plus `streamlit`.)

---

## License

Licensed under the **Apache License 2.0**. See the `LICENSE` file for details.
