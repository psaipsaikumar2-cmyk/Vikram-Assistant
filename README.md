mkdir -p ~/VikramAI_V1000/data
cd ~/VikramAI_V1000

cat > server.py <<'PY'
from http.server import BaseHTTPRequestHandler, HTTPServer
import json
import os
from datetime import datetime

DATA_FILE = "data/vikram.json"

def load_data():
    if not os.path.exists(DATA_FILE):
        return {
            "memory": [],
            "notes": [],
            "tasks": [],
            "history": []
        }

    try:
        with open(DATA_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    except Exception:
        return {
            "memory": [],
            "notes": [],
            "tasks": [],
            "history": []
        }

def save_data(data):
    os.makedirs("data", exist_ok=True)
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        json.dump(data, f, indent=2, ensure_ascii=False)

def reply(message):
    data = load_data()
    text = message.strip()
    low = text.lower()

    if low in ("hi", "hello", "hey"):
        answer = "నమస్తే 👋 నేను Vikram AI V1000."

    elif "నీ పేరు" in low or "your name" in low:
        answer = "నా పేరు Vikram AI V1000 🤖"

    elif "version" in low:
        answer = "Vikram AI V1000"

    elif "time" in low or "సమయం" in low:
        answer = datetime.now().strftime("%H:%M:%S")

    elif low.startswith("remember "):
        value = text[9:].strip()
        if value:
            data["memory"].append({
                "text": value,
                "time": datetime.now().isoformat()
            })
            answer = "🧠 గుర్తుంచుకున్నాను."
        else:
            answer = "ఏది గుర్తుంచుకోవాలో చెప్పు."

    elif low.startswith("note "):
        value = text[5:].strip()
        if value:
            data["notes"].append({
                "text": value,
                "time": datetime.now().isoformat()
            })
            answer = "📒 Note save చేశాను."
        else:
            answer = "Note text ఇవ్వు."

    elif low.startswith("task "):
        value = text[5:].strip()
        if value:
            data["tasks"].append({
                "text": value,
                "done": False,
                "time": datetime.now().isoformat()
            })
            answer = "📋 Task add చేశాను."
        else:
            answer = "Task text ఇవ్వు."

    elif "memory" in low:
        if data["memory"]:
            answer = "\n".join(
                f"{i}. {x['text']}"
                for i, x in enumerate(data["memory"], 1)
            )
        else:
            answer = "🧠 Memory ఖాళీగా ఉంది."

    elif "note" in low:
        if data["notes"]:
            answer = "\n".join(
                f"{i}. {x['text']}"
                for i, x in enumerate(data["notes"], 1)
            )
        else:
            answer = "📒 Notes ఖాళీగా ఉన్నాయి."

    elif "task" in low:
        if data["tasks"]:
            answer = "\n".join(
                f"{i}. {'✅' if x['done'] else '⏳'} {x['text']}"
                for i, x in enumerate(data["tasks"], 1)
            )
        else:
            answer = "📋 Tasks లేవు."

    elif "help" in low:
        answer = (
            "Commands:\n"
            "remember <text>\n"
            "note <text>\n"
            "task <text>\n"
            "memory\n"
            "notes\n"
            "tasks\n"
            "version\n"
            "time"
        )

    else:
        answer = (
            "🤖 Message వచ్చింది: " + text +
            "\n\n"
            "నేను ప్రస్తుతం local V1000 assistant. "
            "AI API connect చేస్తే advanced AI answers కూడా జోడించవచ్చు."
        )

    data["history"].append({
        "user": text,
        "reply": answer,
        "time": datetime.now().isoformat()
    })

    save_data(data)
    return answer

class Handler(BaseHTTPRequestHandler):

    def send_json(self, obj, code=200):
        raw = json.dumps(
            obj,
            ensure_ascii=False
        ).encode("utf-8")

        self.send_response(code)
        self.send_header(
            "Content-Type",
            "application/json; charset=utf-8"
        )
        self.send_header(
            "Content-Length",
            str(len(raw))
        )
        self.end_headers()
        self.wfile.write(raw)

    def do_POST(self):
        if self.path != "/api/chat":
            self.send_json(
                {"error": "Not found"},
                404
            )
            return

        try:
            length = int(
                self.headers.get("Content-Length", 0)
            )

            body = self.rfile.read(length)
            obj = json.loads(body.decode("utf-8"))

            message = obj.get("message", "").strip()

            if not message:
                self.send_json(
                    {"error": "Empty message"},
                    400
                )
                return

            self.send_json({
                "reply": reply(message)
            })

        except Exception as e:
            self.send_json(
                {"error": str(e)},
                500
            )

    def do_GET(self):
        if self.path == "/api/status":
            data = load_data()

            self.send_json({
                "version": "VIKRAM AI V1000",
                "memory": len(data["memory"]),
                "notes": len(data["notes"]),
                "tasks": len(data["tasks"]),
                "history": len(data["history"])
            })
            return

        super().do_GET()

if __name__ == "__main__":
    print("================================")
    print("     VIKRAM AI V1000")
    print("================================")
    print("Open: http://127.0.0.1:8080")
    print("Server running...")
    print("Press CTRL+C to stop.")

    HTTPServer(
        ("127.0.0.1", 8080),
        Handler
    ).serve_forever()
PY


cat > index.html <<'HTML'
<!DOCTYPE html>
<html lang="te">
<head>
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width, initial-scale=1.0">

<title>Vikram AI V1000</title>

<link rel="stylesheet" href="style.css">
</head>

<body>

<header>
    <div class="title">🤖 Vikram AI V1000</div>
    <div class="status">● Local System</div>
</header>

<main>

    <section id="chat"></section>

    <section class="quick">
        <button onclick="quick('hello')">👋 Hello</button>
        <button onclick="quick('memory')">🧠 Memory</button>
        <button onclick="quick('notes')">📒 Notes</button>
        <button onclick="quick('tasks')">📋 Tasks</button>
        <button onclick="quick('version')">ℹ️ Version</button>
    </section>

    <section class="input-area">

        <input
            id="message"
            type="text"
            placeholder="Message టైప్ చేయి..."
            autocomplete="off"
        >

        <button id="send"
                onclick="sendMessage()">
            పంపు
        </button>

    </section>

</main>

<script src="script.js"></script>

</body>
</html>
HTML


cat > style.css <<'CSS'
* {
    box-sizing: border-box;
}

body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #111;
    color: #fff;
}

header {
    padding: 16px;
    text-align: center;
    border-bottom: 1px solid #333;
}

.title {
    font-size: 23px;
    font-weight: bold;
}

.status {
    margin-top: 6px;
    font-size: 13px;
}

main {
    max-width: 800px;
    margin: auto;
}

#chat {
    height: 65vh;
    overflow-y: auto;
    padding: 15px;
}

.message {
    padding: 12px;
    margin: 10px 0;
    border-radius: 12px;
    white-space: pre-wrap;
}

.user {
    background: #333;
    text-align: right;
}

.ai {
    background: #222;
}

.quick {
    display: flex;
    gap: 7px;
    overflow-x: auto;
    padding: 8px;
}

.quick button,
#send {
    border: 0;
    border-radius: 9px;
    padding: 11px 14px;
    cursor: pointer;
}

.input-area {
    display: flex;
    gap: 8px;
    padding: 10px;
}

#message {
    flex: 1;
    padding: 13px;
    border: 1px solid #444;
    border-radius: 10px;
    background: #222;
    color: white;
    outline: none;
}

#send {
    min-width: 70px;
}
CSS


cat > script.js <<'JS'
const chat = document.getElementById("chat");
const input = document.getElementById("message");

function addMessage(text, type) {
    const box = document.createElement("div");

    box.className = "message " + type;
    box.textContent = text;

    chat.appendChild(box);
    chat.scrollTop = chat.scrollHeight;
}

async function sendMessage() {

    const message = input.value.trim();

    if (!message) return;

    addMessage("👤 " + message, "user");

    input.value = "";

    try {

        const response = await fetch("/api/chat", {
            method: "POST",

            headers: {
                "Content-Type": "application/json"
            },

            body: JSON.stringify({
                message: message
            })
        });

        const data = await response.json();

        if (data.reply) {
            addMessage(
                "🤖 " + data.reply,
                "ai"
            );
        } else {
            addMessage(
                "❌ Server response error.",
                "ai"
            );
        }

    } catch (error) {

        addMessage(
            "❌ Python serverకి connection లేదు.",
            "ai"
        );
    }
}

function quick(text) {
    input.value = text;
    sendMessage();
}

input.addEventListener(
    "keydown",
    function(event) {

        if (event.key === "Enter") {
            sendMessage();
        }

    }
);

addMessage(
    "🤖 నమస్తే! నేను Vikram AI V1000. ఏం చేయాలి?",
    "ai"
);
JS


cat > requirements.txt <<'TXT'
# Vikram AI V1000 uses Python standard library.
# No external packages are required for the basic version.
TXT


cat > .gitignore <<'TXT'
__pycache__/
*.pyc
data/vikram.json
.env
TXT


cat > README.md <<'MD'
# 🤖 Vikram AI V1000

Local Python + Web assistant.

## Features

- Web Chat
- Memory
- Notes
- Tasks
- History
- Status
- Local JSON database
- Telugu-friendly interface
- GitHub-ready project

## Run

```bash
python server.py