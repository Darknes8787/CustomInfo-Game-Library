# AI Snapshot

_Generated: 2025-10-09T20:02:01.818059Z_

## Table of contents

- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/README.md](#F:ProgrammiProgrammazioneTool Libreria Giochi PCREADMEmd)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/app.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendapppy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/installers/runner.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesinstallersrunnerpy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/metadata/constants.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesmetadataconstantspy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/metadata/db.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesmetadatadbpy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/metadata/game_aliases.json](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesmetadatagame-aliasesjson)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/metadata/library.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesmetadatalibrarypy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/settings/store.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicessettingsstorepy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/utils/cleanup_logs.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendutilscleanup-logspy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/utils/events.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendutilseventspy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/css/app.css](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendcssappcss)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/css/setting.css](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendcsssettingcss)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/css/variables.css](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendcssvariablescss)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/index.html](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendindexhtml)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/js/aliases-ui.js](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendjsaliases-uijs)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/js/app.js](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendjsappjs)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/js/ui-utils.js](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendjsui-utilsjs)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/installers/templates/generic_installer.yaml](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappinstallerstemplatesgeneric-installeryaml)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/main.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCappmainpy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/config/appinfo.json](#F:ProgrammiProgrammazioneTool Libreria Giochi PCconfigappinfojson)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/config/defaults.json](#F:ProgrammiProgrammazioneTool Libreria Giochi PCconfigdefaultsjson)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/config/schema.json](#F:ProgrammiProgrammazioneTool Libreria Giochi PCconfigschemajson)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/config/user.json](#F:ProgrammiProgrammazioneTool Libreria Giochi PCconfiguserjson)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/requirements.txt](#F:ProgrammiProgrammazioneTool Libreria Giochi PCrequirementstxt)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/scripts/conflict_cleaner.py](#F:ProgrammiProgrammazioneTool Libreria Giochi PCscriptsconflict-cleanerpy)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/scripts/dev_test_scan.pyw](#F:ProgrammiProgrammazioneTool Libreria Giochi PCscriptsdev-test-scanpyw)
- [F:/Programmi/Programmazione/Tool Libreria Giochi PC/start.pyw](#F:ProgrammiProgrammazioneTool Libreria Giochi PCstartpyw)

---


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/README.md
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCREADMEmd"></a>

```md

# CustomInfo Game Library

Una libreria giochi per PC in stile Steam, con interfaccia moderna (HTML/CSS/JS) e backend Python.
- **UI**: web (HTML/CSS/JS) renderizzata con [pywebview].
- **Backend**: Python modulare (servizi per installazione, metadati, impostazioni).
- **DB**: SQLite.
- **Config**: JSON con `defaults.json`, `schema.json`, `user.json`.
- **Installer YAML**: script per ogni gioco, riutilizzabili.

## Avvio rapido (dev)
```bash
python -m venv .venv
. .venv/Scripts/activate   # Windows
pip install -r requirements.txt
python app/main.py
```
"# CustomInfo-Game-Library" 

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/app.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendapppy"></a>

```python
# app/backend/app.py
from .services.settings.store import SettingsStore
from .services.metadata.library import LibraryService
from .services.installers.runner import InstallerRunner
from .services.metadata.db import init_db
from .utils.events import EventBus
import json
from pathlib import Path
from webview import FileDialog
from datetime import datetime
from backend.utils.cleanup_logs import cleanup_old_logs

class Backend:
    def __init__(self):
        self.events = EventBus()
        init_db()
        self.settings = SettingsStore(self.events)
        self.library = LibraryService(self.settings, self.events)
        self.installer = InstallerRunner(self.settings, self.events)

        # Buffer per messaggio di avvio (letto una sola volta dal frontend)
        self._startup_notice = ""

        # 🔹 Pulizia automatica dei log vecchi (mantiene solo gli ultimi 10)
        try:
            deleted = cleanup_old_logs(10)
            if deleted > 0:
                # Salva il messaggio: il frontend lo leggerà e mostrerà nel footer
                self._startup_notice = f"🧹 Pulizia log completata ({deleted} file rimossi)"
        except Exception:
            # silenzioso: non mostra errori in console e non invia eventi
            pass

    # ✨ nuova funzione helper per emettere messaggi al footer
    def get_startup_notice(self):
        """
        Ritorna il messaggio di avvio (se presente) e lo svuota,
        così il frontend lo mostra una sola volta.
        """
        msg = self._startup_notice or ""
        self._startup_notice = ""
        return {"message": msg}

    # --- wrapper methods esposte a JS ---
    def get_settings(self):
        return self.settings.get_user_settings()

    def set_setting(self, path, value):
        return self.settings.set_setting(path, value)

    def restore_defaults(self):
        return self.settings.restore_defaults()

    def scan_folder(self, folder):
        return self.library.scan_folder(folder)

    def get_games(self):
        return self.library.get_games()

    def install_game(self, game_id, yaml_path):
        return self.installer.install_game(game_id, yaml_path)
    
    def get_scan_status(self):
        return self.library.get_scan_status()

    def get_app_info(self):
        here = Path(__file__).resolve()
    
        candidates = [
            here.parents[2] / "config" / "appinfo.json",  # <root>/config/appinfo.json
            here.parents[1] / "config" / "appinfo.json",  # app/config/appinfo.json (fallback)
            here.parents[3] / "config" / "appinfo.json",  # ancora più in alto, per setup atipici
        ]
    
        for p in candidates:
            if p.exists():
                try:
                    return json.loads(p.read_text(encoding="utf-8"))
                except Exception as e:
                    return {"ui_version":"N/D","tool_version":"N/D","error":f"JSON invalido in {p}: {e}"}
    
        return {
            "ui_version":"N/D",
            "tool_version":"N/D",
            "build":"N/D",
            "channel":"N/D",
            "error":"appinfo.json non trovato",
            "candidates":[str(p) for p in candidates],
        }
    
    def pick_folder(self):
        """
        Apre il dialog di selezione cartella tramite pywebview e restituisce un dict compatto.
        """
        import webview
        win = webview.windows[0]
        try:
            result = win.create_file_dialog(FileDialog.FOLDER, allow_multiple=False)
            if result and len(result) > 0:
                return {"ok": True, "path": result[0]}
            return {"ok": False, "error": "cancelled"}
        except Exception as e:
            return {"ok": False, "error": str(e)}
        
    def get_recent_logs(self, topic: str | None = None, limit: int = 100):
        """
        Restituisce gli ultimi eventi dal buffer (opzionalmente filtrati per topic).
        """
        return self.events.get_recent(topic, limit)
    
    def save_log_file(self, text: str, filename: str | None = None):
        """
        Salva il contenuto 'text' in ./runtime/Log/log_YYYYMMDD_HHMMSS.txt
        Crea le cartelle se non esistono. Ritorna {"ok":True, "path": "..."}.
        """
        try:
            base = Path.cwd() / "runtime" / "logs"
            base.mkdir(parents=True, exist_ok=True)
            if not filename:
                stamp = datetime.now().strftime("%Y%m%d_%H%M%S")
                filename = f"log_{stamp}.txt"
            out = base / filename
            # Scrittura UTF-8 senza troncamenti
            out.write_text(text or "", encoding="utf-8")
            return {"ok": True, "path": str(out)}
        except Exception as e:
            return {"ok": False, "error": str(e)}

    def expose_api(self):
        import webview
        win = webview.windows[0]
        win.expose(
            self.get_settings,
            self.set_setting,
            self.restore_defaults,
            self.scan_folder,
            self.get_games,
            self.install_game,
            self.get_app_info,
            self.pick_folder,
            self.get_recent_logs,
            self.get_scan_status,
            self.save_log_file,
            self.get_startup_notice,
        )

def create_backend():
    return Backend()
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/installers/runner.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesinstallersrunnerpy"></a>

```python
import subprocess, os, time, json, sys
from pathlib import Path
import yaml

CREATE_NO_WINDOW = 0x08000000 if os.name == 'nt' else 0

ROOT = Path(__file__).resolve().parents[3]  # /app
RUNTIME = ROOT.parent / "runtime"
LOGS = RUNTIME / "logs"
TEMPLATES = ROOT / "installers" / "templates"

class InstallerRunner:
    def __init__(self, settings, events):
        self.settings = settings
        self.events = events
        LOGS.mkdir(parents=True, exist_ok=True)

    def _run_hidden(self, cmd, cwd=None):
        # Esegue un processo senza mostrare finestre (Windows) e cattura l'output
        proc = subprocess.Popen(
            cmd,
            cwd=cwd,
            stdout=subprocess.PIPE,
            stderr=subprocess.STDOUT,
            creationflags=CREATE_NO_WINDOW
        )
        lines = []
        for line in iter(proc.stdout.readline, b''):
            if not line:
                break
            text = line.decode(errors="ignore").rstrip()
            lines.append(text)
            self.events.emit("installer/log", text)
        proc.wait()
        return proc.returncode, "\n".join(lines)

    def install_game(self, game_id, yaml_path):
        # Carica il piano di installazione YAML e lo esegue in silenzio
        plan = yaml.safe_load(Path(yaml_path).read_text(encoding="utf-8"))
        code, log = 0, ""
        for step in plan.get("steps", []):
            if step["type"] == "7zip_extract":
                cmd = [step.get("sevenzip", "7z"), "x", step["archive"], f"-o{step['destination']}", "-y", "-bso0", "-bsp1"]
                code, part = self._run_hidden(cmd)
                log += part + "\n"
                if code != 0: break
            elif step["type"] == "mount_iso":
                # PowerShell silent mount
                iso = step["path"]
                ps = [
                    "powershell", "-NoProfile", "-WindowStyle", "Hidden",
                    "-Command",
                    f"Mount-DiskImage -ImagePath '{iso}' -NoDriveLetter:$false -PassThru | Get-Volume | Select -ExpandProperty DriveLetter"
                ]
                code, part = self._run_hidden(ps)
                log += part + "\n"
                if code != 0: break
            elif step["type"] == "run_setup":
                exe = step["exe"]
                args = step.get("args", [])
                code, part = self._run_hidden([exe] + args)
                log += part + "\n"
                if code != 0: break
        # Log su file
        (LOGS / f"{game_id}.log").write_text(log, encoding="utf-8")
        status = "installed" if code == 0 else "failed"
        self.events.emit("installer/done", {"game_id": game_id, "status": status})
        return {"status": status}

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/metadata/constants.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesmetadataconstantspy"></a>

```python
# app/backend/services/metadata/constants.py
import re

# ========= CANDIDATE TYPES & FILTERS =========
SUPPORTED_ISO = {".iso"}
SUPPORTED_ARCHIVES = {".7z", ".zip", ".rar"}     # estendibile
INSTALL_HINTS = {"setup.exe", "install.exe"}     # cartelle con installer

# Directory "noise" da saltare interamente (solo nome cartella)
EXCLUDE_DIR_KEYWORDS = {
    "update", "updates", "patch", "patches", "hotfix", "dlc",
    "language pack", "langpack", "crack", "redist", "redistributable",
    "support", "supportfiles", "deps", "vcredist", "directx", "minecraft",
    "steamworks", "steam_api", "movies", "docs", "doc", "mod", "mods",
    "md5"
}

# File "noise" da ignorare (solo nome file)
# ⚠️ NOTA: NON escludiamo più 'autorun' qui: lo valuteremo nello scoring.
EXCLUDE_FILE_KEYWORDS = {
    "readme", "changelog", "license", "eula", "unins",
    "quicksfv", "verify", "checksum", "sha1", "sha256",
    "langpack", "language pack", "update", "steam_api",
    "vcredist", "directx", "dotnet", "framework", "runtime",
    "images", "ubisoftgamelauncherinstaller", "uplayinstaller",
}

# Estensioni da ignorare comunque
EXCLUDE_FILE_EXT = {
    ".sfv", ".md5", ".sha1", ".sha256",
    ".nfo", ".diz", ".txt", ".rtf", ".pdf",
    ".url", ".lnk", ".py", ".pyw", ".bat",
    ".ini", ".inf", ".cfg", ".log",
}

# Correzioni titoli personalizzate
TITLE_FIX_MAP = [
    # (qui mantieni le tue regole di fix già esistenti)
    (re.compile(r"\bAssassin(?:['’]s){2,}\b", re.IGNORECASE), "Assassin's"),
    (re.compile(r"\bAssassins['’]s\b", re.IGNORECASE), "Assassin's"),
    (re.compile(r"\bassassins\b", re.IGNORECASE), "Assassin's"),
    (re.compile(r"\bdoom\s*iii\b", re.IGNORECASE), "DOOM 3"),
    (re.compile(r"\bmgs2_sse\b", re.IGNORECASE), "Metal Gear Solid 2 - Substance"),
        
    # Corregge Assassin's Creed 4 -> Assassin's Creed IV
    (re.compile(r"\bAssassin's Creed 4\b", re.IGNORECASE), "Assassin's Creed IV"),

    # Corregge Assassin's Creed 3 -> Assassin's Creed III
    (re.compile(r"\bAssassin's Creed 3\b", re.IGNORECASE), "Assassin's Creed III"),

    # Corregge Assassin's Creed 2 -> Assassin's Creed II
    (re.compile(r"\bAssassin's Creed 2\b", re.IGNORECASE), "Assassin's Creed II"),
]

# Titoli troppo generici / da scartare
BANNED_TITLES_EXACT = {
    "setup", "install", "installer", "uninstall", "setup/uninstall",
    "application", "program", "software",
    "microsoft installer", "inno setup", "installshield", "nullsoft"
}

# Parole vietate se COMPARENO DENTRO il FileDescription (match per sottostringa)
BANNED_IN_FILEDESCRIPTION = {
    "setup", "uninstall", "installer", "installshield", "inno setup", "nullsoft",
    "setup/uninstall"
}

# Versioni "vere": rimuovi solo pattern da versione, NON i sequel (2, 3, II...)
RE_VERSION_WORDS = re.compile(r'\b(?:v|ver|version)\s*\d+(?:\.\d+){0,3}\b', re.IGNORECASE)

# Build/revision: "build 1234", "rev 77", "r 1029"
RE_BUILD_WORDS = re.compile(r'\b(?:build|rev|r)\s*\d+\b', re.IGNORECASE)

# Numeri in coda SOLO se hanno almeno un punto (es. 1.0, 2.3.4) oppure sono build/rev
# Esempi rimossi: " - 1.0", " v2.1 ", " - build 1420"
# Esempi NON rimossi: " 2", " III"
RE_TRAIL_VERSION_ONLY = re.compile(
    r'[\s\-_:]*(?:v?\d+\.\d+(?:\.\d+){0,2}|(?:build|rev|r)\s*\d+)\s*$',
    re.IGNORECASE
)

# Regex per “release noise” (gruppi scene, edizioni, ecc.)
# Se prima della parola c’è un simbolo ( :, -, –, —, |, •, ·, ~ ) lo elimina insieme alla parola.
RE_NOISE_BLOCKS = re.compile(
    r'(?:\s*[:\-\|–—•·~]\s*)?\b('
    r'CODEX|FitGirl|EMPRESS|TENOKE|ElAmigos|DODI|R\.G\. Mechanics|R\.G\.Catalyst|Razor1911|GOG|FLT|Duology|'
    r'|CE|TD|'
    r'REPACK|MULTI\d*|MULTI|Director\'s Cut'
    r')\b',
    re.IGNORECASE
)

# Rimuove [ ... ] e ( ... )
RE_BRACKETS = re.compile(r'[\[\(].*?[\]\)]')

# Rumore generico nei token
CLEAN_RX = re.compile(
    r'\b(setup|installer|install|launcher|config|dxsetup|redistributable|update|patch|vc_redist|vcredist|directx|physx|dotnet|runtime)\b',
    re.IGNORECASE
)

# Manteniamo anche una regex "globale" da applicare durante il cleaning
VERSION_RX = re.compile(
    r'(?:' + RE_VERSION_WORDS.pattern + r')|(?:' + RE_BUILD_WORDS.pattern + r')',
    re.IGNORECASE
)
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/metadata/db.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesmetadatadbpy"></a>

```python
# app/backend/services/metadata/db.py
import os
import sqlite3
from pathlib import Path

DB_DIR = Path("runtime/db")
DB_PATH = DB_DIR / "library.db"

SCHEMA = """
PRAGMA journal_mode=WAL;
PRAGMA synchronous=NORMAL;

CREATE TABLE IF NOT EXISTS games (
    id TEXT PRIMARY KEY,
    title TEXT NOT NULL,
    path TEXT NOT NULL UNIQUE,
    cover_path TEXT,
    platform TEXT,
    lang TEXT,
    status TEXT DEFAULT 'indexed',
    added_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE IF NOT EXISTS tags (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL UNIQUE
);

CREATE TABLE IF NOT EXISTS game_tags (
    game_id TEXT NOT NULL,
    tag_id INTEGER NOT NULL,
    PRIMARY KEY (game_id, tag_id),
    FOREIGN KEY (game_id) REFERENCES games(id) ON DELETE CASCADE,
    FOREIGN KEY (tag_id) REFERENCES tags(id) ON DELETE CASCADE
);

CREATE TABLE IF NOT EXISTS installs (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    game_id TEXT NOT NULL,
    state TEXT NOT NULL,
    progress INTEGER DEFAULT 0,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (game_id) REFERENCES games(id) ON DELETE CASCADE
);
"""

def get_conn():
    DB_DIR.mkdir(parents=True, exist_ok=True)
    conn = sqlite3.connect(DB_PATH)
    conn.row_factory = sqlite3.Row
    return conn

def init_db():
    conn = get_conn()
    try:
        conn.executescript(SCHEMA)
        conn.commit()
    finally:
        conn.close()
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/metadata/game_aliases.json
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesmetadatagame-aliasesjson"></a>

```json
{
  "assassin's creed": {
    "title_real": "Assassin's Creed",
    "aliases": [
      "Assassin Creed",
      "Assassin's Creed",
      "Assassins Creed",
      "Assassins's Creed",
      "ac"
    ]
  },
  "assassin's creed ii": {
    "title_real": "Assassin's Creed II",
    "aliases": [
      "Assassin Creed II",
      "Assassin's Creed II",
      "Assassins Creed II",
      "Assassins's Creed II",
      "ac 2",
      "ac2",
      "assassins creed 2"
    ]
  },
  "assassin's creed brotherhood": {
    "title_real": "Assassin's Creed: Brotherhood",
    "aliases": [
      "Assassin Creed Brotherhood",
      "Assassin's Creed Brotherhood",
      "Assassins Creed Brotherhood",
      "Assassins's Creed Brotherhood",
      "acb"
    ]
  },
  "assassin's creed revelations": {
    "title_real": "Assassin's Creed: Revelations",
    "aliases": [
      "Assassin Creed Revelations",
      "Assassin's Creed Revelations",
      "Assassins Creed Revelations",
      "Assassins's Creed Revelations",
      "acr"
    ]
  },
  "assassin's creed iii": {
    "title_real": "Assassin's Creed III",
    "aliases": [
      "Assassin Creed III",
      "Assassin's Creed III",
      "Assassins Creed III",
      "Assassins's Creed III",
      "ac 3",
      "ac3",
      "assassins creed 3"
    ]
  },
  "assassin's creed iii remastered": {
    "title_real": "Assassin's Creed III - Remastered",
    "aliases": [
      "Assassin Creed III",
      "Assassin 's Creed III",
      "Assassin ' s Creed III",
      "Assassin's Creed III",
      "Assassins Creed III",
      "Assassins's Creed III",
      "ac 3",
      "ac3",
      "assassins creed 3"
    ]
  },
  "assassin's creed iv: black flag": {
    "title_real": "Assassin's Creed IV: Black Flag",
    "aliases": [
      "Assassin Creed IV: Black Flag",
      "Assassin's Creed 4 Black Flag",
      "Assassin's Creed IV Black Flag",
      "Assassin's Creed IV: BlackFlag",
      "Assassins Creed IV: Black Flag",
      "Assassins's Creed IV: Black Flag",
      "ac 4",
      "ac iv",
      "ac4",
      "assassins creed 4"
    ]
  },
  "assassin's creed rogue": {
    "title_real": "Assassin's Creed: Rogue",
    "aliases": [
      "Assassin Creed Rogue",
      "Assassin's Creed Rogue",
      "Assassins Creed Rogue",
      "Assassins's Creed Rogue",
      "acr (rogue)"
    ]
  },
  "assassin's creed unity": {
    "title_real": "Assassin's Creed: Unity",
    "aliases": [
      "Assassin Creed Unity",
      "Assassin's Creed Unity",
      "Assassins Creed Unity",
      "Assassins's Creed Unity",
      "acu"
    ]
  },
  "assassin's creed syndicate": {
    "title_real": "Assassin's Creed: Syndicate",
    "aliases": [
      "Assassin Creed Syndicate",
      "Assassin's Creed Syndicate",
      "Assassins Creed Syndicate",
      "Assassins's Creed Syndicate",
      "Assassin’s Creed 9 Syndicate",
      "acs"
    ]
  },
  "assassin's creed origins": {
    "title_real": "Assassin's Creed: Origins",
    "aliases": [
      "Assassin Creed Origins",
      "Assassin's Creed Origins",
      "Assassins Creed Origins",
      "Assassins's Creed Origins",
      "aco"
    ]
  },
  "assassin's creed odyssey": {
    "title_real": "Assassin's Creed: Odyssey",
    "aliases": [
      "Assassin Creed Odyssey",
      "Assassin's Creed Odyssey",
      "Assassins Creed Odyssey",
      "Assassins's Creed Odyssey",
      "aco (odyssey)"
    ]
  },
  "assassin's creed valhalla": {
    "title_real": "Assassin's Creed: Valhalla",
    "aliases": [
      "Assassin Creed Valhalla",
      "Assassin's Creed Valhalla",
      "Assassins Creed Valhalla",
      "Assassins's Creed Valhalla",
      "acv"
    ]
  },
  "assassin's creed mirage": {
    "title_real": "Assassin's Creed: Mirage",
    "aliases": [
      "Assassin Creed Mirage",
      "Assassin's Creed Mirage",
      "Assassins Creed Mirage",
      "Assassins's Creed Mirage",
      "acm"
    ]
  },
  "assassin's creed shadow": {
    "title_real": "Assassin's Creed: Shadows",
    "aliases": [
      "Assassin Creed Shadows",
      "Assassin's Creed Shadows",
      "Assassins Creed Shadows",
      "Assassins's Creed Shadows",
      "acs"
    ]
  },
  "bioshock": {
    "title_real": "BioShock",
    "aliases": [
      "bio shock",
      "bioshock saga"
    ]
  },
  "bioshock 2": {
    "title_real": "BioShock 2",
    "aliases": [
      "bio shock 2",
      "bioshock2"
    ]
  },
  "bioshock infinite": {
    "title_real": "BioShock Infinite",
    "aliases": [
      "bio shock infinite",
      "bioshock infinite"
    ]
  },
  "bayonetta": {
    "title_real": "Bayonetta",
    "aliases": []
  },
    "car mechanic simulator 2021": {
      "title_real": "Car Mechanic Simulator 2021",
      "aliases": [
        "car mechanic 2021",
        "car mechanic sim 2021",
        "car mechanic simulato",
        "car mechanic simulater 2021",
        "car mechanic simualtor 2021",
        "car mechanic simulator '21",
        "car mechanic simulator - 2021",
        "car mechanic simulator2021",
        "cms 2021",
        "cms21"
      ]
    },
  "darksiders": {
    "title_real": "Darksiders",
    "aliases": []
  },
  "darksiders ii": {
    "title_real": "Darksiders II",
    "aliases": [
      "darksider ii",
      "darksiders 2"
    ]
  },
  "darksiders iii": {
    "title_real": "Darksiders III",
    "aliases": [
      "darksider iii",
      "darksiders 3"
    ]
  },
  "dead island": {
    "title_real": "Dead Island",
    "aliases": []
  },
  "dead island riptide": {
    "title_real": "Dead Island Riptide",
    "aliases": []
  },
  "dead island 2": {
    "title_real": "Dead Island 2",
    "aliases": [
      "dead island two",
      "deadisland 2"
    ]
  },
  "dead space": {
    "title_real": "Dead Space",
    "aliases": [
      "deadspace"
    ]
  },
  "dead space 2": {
    "title_real": "Dead Space 2",
    "aliases": [
      "deadspace 2"
    ]
  },
  "dead space 3": {
    "title_real": "Dead Space 3",
    "aliases": [
      "deadspace 3"
    ]
  },
  "devil may cry": {
    "title_real": "Devil May Cry",
    "aliases": [
      "dmc"
    ]
  },
  "devil may cry 2": {
    "title_real": "Devil May Cry 2",
    "aliases": [
      "dmc2"
    ]
  },
  "devil may cry 3": {
    "title_real": "Devil May Cry 3",
    "aliases": [
      "dmc3"
    ]
  },
  "devil may cry 4": {
    "title_real": "Devil May Cry 4",
    "aliases": [
      "dmc4"
    ]
  },
  "devil may cry 5": {
    "title_real": "Devil May Cry 5",
    "aliases": [
      "dmc5"
    ]
  },
  "dmc: devil may cry": {
    "title_real": "DmC: Devil May Cry",
    "aliases": [
      "dmc 2013",
      "dmc reboot"
    ]
  },
  "dragon ball xenoverse 2": {
    "title_real": "Dragon Ball Xenoverse 2",
    "aliases": [
      "db xenoverse 2",
      "dbx 2"
    ]
  },
  "dragon ball z: kakarot": {
    "title_real": "Dragon Ball Z: Kakarot",
    "aliases": [
      "dbz kakarot",
      "dragon ball kakarot"
    ]
  },
  "dying light": {
    "title_real": "Dying Light",
    "aliases": [
      "dyinglight"
    ]
  },
  "dying light: the following": {
    "title_real": "Dying Light: The Following",
    "aliases": [
      "dying light following"
    ]
  },
  "dying light 2: stay human": {
    "title_real": "Dying Light 2: Stay Human",
    "aliases": [
      "dying light 2",
      "dyinglight2",
      "stay human"
    ]
  },
  "factorio": {
    "title_real": "Factorio",
    "aliases": []
  },
  "fallout": {
    "title_real": "Fallout",
    "aliases": []
  },
  "fallout 2": {
    "title_real": "Fallout 2",
    "aliases": []
  },
  "fallout 3": {
    "title_real": "Fallout 3",
    "aliases": [
      "fo3"
    ]
  },
  "fallout: new vegas": {
    "title_real": "Fallout: New Vegas",
    "aliases": [
      "fallout new vegas",
      "fnv"
    ]
  },
  "fallout 4": {
    "title_real": "Fallout 4",
    "aliases": [
      "fo4"
    ]
  },
  "far cry": {
    "title_real": "Far Cry",
    "aliases": [
      "farcry 1"
    ]
  },
  "far cry 2": {
    "title_real": "Far Cry 2",
    "aliases": [
      "farcry 2",
      "fc2"
    ]
  },
  "far cry 3": {
    "title_real": "Far Cry 3",
    "aliases": [
      "farcry 3",
      "fc3"
    ]
  },
  "far cry 3 digital deluxe edition": {
    "title_real": "Far Cry 3 - Digital Deluxe Edition",
    "aliases": [
      "farcry 3 digital delux edition",
      "fc3"
    ]
  },
  "far cry 3: blood dragon": {
    "title_real": "Far Cry 3: Blood Dragon",
    "aliases": [
      "blood dragon"
    ]
  },
  "far cry 4": {
    "title_real": "Far Cry 4",
    "aliases": [
      "farcry 4",
      "fc4"
    ]
  },
  "far cry 4 golden edition": {
    "title_real": "Far Cry 4 - Golden Edition",
    "aliases": [
      "farcry 4 gold edition",
      "fc4"
    ]
  },
  "far cry 5": {
    "title_real": "Far Cry 5",
    "aliases": [
      "farcry 5",
      "fc5"
    ]
  },
  "far cry new dawn": {
    "title_real": "Far Cry New Dawn",
    "aliases": [
      "new dawn"
    ]
  },
  "far cry 6": {
    "title_real": "Far Cry 6",
    "aliases": [
      "farcry 6",
      "fc6"
    ]
  },
  "far cry primal": {
    "title_real": "Far Cry Primal",
    "aliases": [
      "primal"
    ]
  },
  "frostpunk": {
    "title_real": "Frostpunk",
    "aliases": []
  },
  "god of war": {
    "title_real": "God of War",
    "aliases": [
      "gow",
      "gow (2018)"
    ]
  },
  "god of war ragnarök": {
    "title_real": "God of War Ragnarök",
    "aliases": [
      "god of war ragnarok",
      "gow ragnarok"
    ]
  },
  "grand theft auto iii": {
    "title_real": "Grand Theft Auto III",
    "aliases": [
      "gta 3",
      "gta iii"
    ]
  },
  "grand theft auto: vice city": {
    "title_real": "Grand Theft Auto: Vice City",
    "aliases": [
      "gta vc",
      "vice city"
    ]
  },
  "grand theft auto: san andreas": {
    "title_real": "Grand Theft Auto: San Andreas",
    "aliases": [
      "gta sa",
      "san andreas"
    ]
  },
  "grand theft auto iv": {
    "title_real": "Grand Theft Auto IV",
    "aliases": [
      "gta 4",
      "gta iv"
    ]
  },
  "grand theft auto v": {
    "title_real": "Grand Theft Auto V",
    "aliases": [
      "gta 5",
      "gta five",
      "gta v"
    ]
  },
  "horizon zero dawn": {
    "title_real": "Horizon Zero Dawn",
    "aliases": [
      "horizon dawn",
      "horizon zero",
      "hzd"
    ]
  },
  "horizon forbidden west": {
    "title_real": "Horizon Forbidden West",
    "aliases": [
      "hfw",
      "horizon west"
    ]
  },
  "hydroneer": {
    "title_real": "Hydroneer",
    "aliases": [
      "hydro neer",
      "hydronear"
    ]
  },
  "icarus": {
    "title_real": "Icarus",
    "aliases": []
  },
  "immortals fenyx rising": {
    "title_real": "Immortals Fenyx Rising",
    "aliases": [
      "fenyx rising",
      "immortals"
    ]
  },
  "just cause": {
    "title_real": "Just Cause",
    "aliases": []
  },
  "just cause 2": {
    "title_real": "Just Cause 2",
    "aliases": [
      "jc2"
    ]
  },
  "just cause 3": {
    "title_real": "Just Cause 3",
    "aliases": [
      "jc3"
    ]
  },
  "just cause 4": {
    "title_real": "Just Cause 4",
    "aliases": [
      "jc4"
    ]
  },
  "kingdom rush": {
    "title_real": "Kingdom Rush",
    "aliases": []
  },
  "kingdom rush frontiers": {
    "title_real": "Kingdom Rush Frontiers",
    "aliases": [
      "frontiers"
    ]
  },
  "kingdom rush origins": {
    "title_real": "Kingdom Rush Origins",
    "aliases": [
      "origins"
    ]
  },
  "kingdom rush vengeance": {
    "title_real": "Kingdom Rush Vengeance",
    "aliases": [
      "vengeance"
    ]
  },
  "metal gear solid 2: substance": {
    "title_real": "Metal Gear Solid 2: Substance",
    "aliases": [
      "mgs 2 substance",
      "mgs2 substance"
    ]
  },
  "metal gear solid v: the phantom pain": {
    "title_real": "Metal Gear Solid V: The Phantom Pain",
    "aliases": [
      "mgs 5",
      "mgsv",
      "phantom pain"
    ]
  },
  "metal gear rising: revengeance": {
    "title_real": "Metal Gear Rising: Revengeance",
    "aliases": [
      "mgr",
      "rising revengeance"
    ]
  },
  "middle-earth: shadow of mordor": {
    "title_real": "Middle-earth: Shadow of Mordor",
    "aliases": [
      "shadow of mordor",
      "som"
    ]
  },
  "middle-earth: shadow of war": {
    "title_real": "Middle-earth: Shadow of War",
    "aliases": [
      "definitive edition",
      "shadow of war",
      "sow"
    ]
  },
  "minecraft": {
    "title_real": "Minecraft",
    "aliases": [
      "mine craft"
    ]
  },
  "need for speed underground 2": {
    "title_real": "Need for Speed Underground 2",
    "aliases": [
      "nfs underground 2",
      "nfsu2"
    ]
  },
  "need for speed heat": {
    "title_real": "Need for Speed Heat",
    "aliases": [
      "nfs heat"
    ]
  },
  "need for speed most wanted": {
    "title_real": "Need for Speed Most Wanted",
    "aliases": [
      "nfs most wanted"
    ]
  },
  "need for speed payback": {
    "title_real": "Need for Speed Payback",
    "aliases": [
      "nfs payback"
    ]
  },
  "nier: automata": {
    "title_real": "NieR: Automata",
    "aliases": [
      "nier automata",
      "nier:automata"
    ]
  },
  "nier replicant ver.1.22474487139...": {
    "title_real": "NieR Replicant ver.1.22474487139...",
    "aliases": [
      "nier replicant",
      "nier replicant ver 1.22",
      "ver 1.22474487139"
    ]
  },
  "ninja gaiden: master collection": {
    "title_real": "Ninja Gaiden: Master Collection",
    "aliases": [
      "ng master collection",
      "ninja gaiden master"
    ]
  },
  "ninja gaiden sigma": {
    "title_real": "Ninja Gaiden Sigma",
    "aliases": [
      "sigma"
    ]
  },
  "ninja gaiden sigma 2": {
    "title_real": "Ninja Gaiden Sigma 2",
    "aliases": [
      "sigma 2"
    ]
  },
  "nioh": {
    "title_real": "Nioh",
    "aliases": []
  },
  "nioh 2": {
    "title_real": "Nioh 2",
    "aliases": [
      "nioh2"
    ]
  },
  "no man's sky": {
    "title_real": "No Man's Sky",
    "aliases": [
      "no mans sky",
      "nomans sky"
    ]
  },
  "oxygen not included": {
    "title_real": "Oxygen Not Included",
    "aliases": [
      "oxygen not include"
    ]
  },
  "palworld": {
    "title_real": "Palworld",
    "aliases": []
  },
  "pc building simulator 2": {
    "title_real": "PC Building Simulator 2",
    "aliases": [
      "pc building sim 2"
    ]
  },
  "resident evil 0": {
    "title_real": "Resident Evil 0",
    "aliases": [
      "re0",
      "resident evil 0"
    ]
  },
  "resident evil": {
    "title_real": "Resident Evil",
    "aliases": [
      "biohazard"
    ]
  },
  "resident evil 2": {
    "title_real": "Resident Evil 2",
    "aliases": [
      "re2",
      "resident evil 2"
    ]
  },
  "resident evil 3": {
    "title_real": "Resident Evil 3",
    "aliases": [
      "re3",
      "resident evil 3"
    ]
  },
  "resident evil 4": {
    "title_real": "Resident Evil 4",
    "aliases": [
      "re4",
      "resident evil 4"
    ]
  },
  "resident evil 5": {
    "title_real": "Resident Evil 5",
    "aliases": [
      "re5",
      "resident evil 5"
    ]
  },
  "resident evil 5 gold edition": {
    "title_real": "Resident Evil 5 - Gold Edition",
    "aliases": [
      "re5g",
      "resident evil 5 gold edition",
      "Resident Evil 5 - Gold Edition",
      "resident evil 5 - gold edition",
      "Resident Evil 5: Gold Edition"
    ]
  },
  "resident evil 6": {
    "title_real": "Resident Evil 6",
    "aliases": [
      "re6",
      "resident evil 6"
    ]
  },
  "resident evil 7: biohazard": {
    "title_real": "Resident Evil 7: Biohazard",
    "aliases": [
      "biohazard 7",
      "re7",
      "resident evil 7"
    ]
  },
  "resident evil village": {
    "title_real": "Resident Evil Village",
    "aliases": [
      "re8",
      "resident evil 8"
    ]
  },
  "saints row": {
    "title_real": "Saints Row",
    "aliases": [
      "saint row",
      "saints row 1"
    ]
  },
  "saints row 2": {
    "title_real": "Saints Row 2",
    "aliases": [
      "sr2"
    ]
  },
  "saints row: the third": {
    "title_real": "Saints Row: The Third",
    "aliases": [
      "saints row 3",
      "the third"
    ]
  },
  "saints row iv": {
    "title_real": "Saints Row IV",
    "aliases": [
      "saints row 4",
      "sr iv"
    ]
  },
  "saints row (2022)": {
    "title_real": "Saints Row (2022)",
    "aliases": [
      "saints row reboot"
    ]
  },
  "satisfactory": {
    "title_real": "Satisfactory",
    "aliases": []
  },
  "sekiro: shadows die twice": {
    "title_real": "Sekiro: Shadows Die Twice",
    "aliases": [
      "sekiro",
      "sekiro shadows"
    ]
  },
  "state of decay 2: juggernaut edition": {
    "title_real": "State of Decay 2: Juggernaut Edition",
    "aliases": [
      "juggernaut edition",
      "sod2",
      "state of decay 2"
    ]
  },
  "terratech": {
    "title_real": "TerraTech",
    "aliases": [
      "terra tech",
      "terra tech v1.5",
      "terratech"
    ]
  },
  "the last of us part i": {
    "title_real": "The Last of Us Part I",
    "aliases": [
      "the last of us",
      "tlou",
      "tlou part i"
    ]
  },
  "the last of us part ii": {
    "title_real": "The Last of Us Part II",
    "aliases": [
      "the last of us 2",
      "tlou part ii",
      "tlou2"
    ]
  },
  "call of duty: modern warfare": {
    "title_real": "Call of Duty: Modern Warfare",
    "aliases": [
      "cod mw",
      "modern warfare 2019"
    ]
  },
  "call of duty: modern warfare ii": {
    "title_real": "Call of Duty: Modern Warfare II",
    "aliases": [
      "cod mw2 2022",
      "modern warfare ii"
    ]
  },
  "call of duty: black ops iii": {
    "title_real": "Call of Duty: Black Ops III",
    "aliases": [
      "black ops 3",
      "cod bo3"
    ]
  },
  "battlefield 1": {
    "title_real": "Battlefield 1",
    "aliases": [
      "bf1"
    ]
  },
  "battlefield v": {
    "title_real": "Battlefield V",
    "aliases": [
      "bf v",
      "bf5"
    ]
  },
  "halo: the master chief collection": {
    "title_real": "Halo: The Master Chief Collection",
    "aliases": [
      "halo mcc"
    ]
  },
  "tomb raider": {
    "title_real": "Tomb Raider",
    "aliases": [
      "tomb raider 2013"
    ]
  },
  "rise of the tomb raider": {
    "title_real": "Rise of the Tomb Raider",
    "aliases": [
      "rottr"
    ]
  },
  "shadow of the tomb raider": {
    "title_real": "Shadow of the Tomb Raider",
    "aliases": [
      "sottr"
    ]
  }
}
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/metadata/library.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicesmetadatalibrarypy"></a>

```python
# app/backend/services/metadata/library.py
import os
import re
import time
import json
import hashlib
import subprocess
import ctypes
from pathlib import Path
from .db import get_conn
from threading import Thread

# === importa tutte le costanti/regex dal file esterno ===
from .constants import (
    SUPPORTED_ISO, SUPPORTED_ARCHIVES, INSTALL_HINTS,
    EXCLUDE_DIR_KEYWORDS, EXCLUDE_FILE_KEYWORDS, EXCLUDE_FILE_EXT,
    TITLE_FIX_MAP, RE_NOISE_BLOCKS, RE_BRACKETS, RE_TRAIL_VERSION_ONLY,
    CLEAN_RX, VERSION_RX, BANNED_TITLES_EXACT, BANNED_IN_FILEDESCRIPTION
)

import sys, time

DEBUG_SCAN = True

def dbg(*args):
    if DEBUG_SCAN:
        ts = time.strftime("%H:%M:%S")
        print(f"[SCAN {ts}]", *args, flush=True)  # flush=True = stampa immediata


# =======================================================
# ===============  SETTINGS HELPER  =====================
# =======================================================

def _get_setting(settings, key, default=None):
    """Recupera valore da dict o da oggetto tipo SettingsStore"""
    try:
        if isinstance(settings, dict):
            return settings.get(key, default)
        return getattr(settings, key, default)
    except Exception:
        return default

# =======================================================
# ===============   LIBRERIA ALIAS JSON   ===============
# =======================================================

# Cache in-process per evitare I/O ripetuti
_ALIAS_DATA = None  # {"canon_to_real": {...}, "alias_to_real": {...}, "loaded_path": str}

def _default_aliases_path() -> Path:
    # Cerca game_aliases.json a fianco di questo file
    return Path(__file__).with_name("game_aliases.json")

def _load_aliases(aliases_path: str | Path | None = None) -> dict:
    """
    Carica il JSON una volta e prepara due dizionari:
    - canon_to_real: chiave canonica -> titolo reale
    - alias_to_real: alias canonico -> titolo reale
    """
    global _ALIAS_DATA
    if _ALIAS_DATA is not None:
        return _ALIAS_DATA

    p = Path(aliases_path) if aliases_path else _default_aliases_path()
    canon_to_real: dict[str, str] = {}
    alias_to_real: dict[str, str] = {}

    try:
        with open(p, "r", encoding="utf-8") as f:
            data = json.load(f)
    except Exception:
        # Se manca il file o è invalido, lavoriamo senza mappa (fallback: niente risoluzione)
        _ALIAS_DATA = {"canon_to_real": {}, "alias_to_real": {}, "loaded_path": str(p)}
        return _ALIAS_DATA

    # data è del tipo: { "<chiave canonica>": { "title_real": "...", "aliases": [...] }, ... }
    for canon_key, payload in data.items():
        title_real = (payload or {}).get("title_real") or ""
        aliases = (payload or {}).get("aliases") or []

        if not title_real:
            continue

        # Normalizza la chiave canonica e salva
        k = canonical_title_key(canon_key)
        if k:
            canon_to_real[k] = title_real

        # Registra anche gli alias (canonizzati)
        for a in aliases:
            ak = canonical_title_key(a)
            if ak:
                alias_to_real[ak] = title_real

        # Registra sempre anche la forma "pulita" del title_real come chiave canonica
        rk = canonical_title_key(title_real)
        if rk and rk not in canon_to_real:
            canon_to_real[rk] = title_real

    _ALIAS_DATA = {"canon_to_real": canon_to_real, "alias_to_real": alias_to_real, "loaded_path": str(p)}
    return _ALIAS_DATA

def resolve_with_aliases(title: str, aliases_path: str | Path | None = None) -> str:
    """
    Applica fix/clean al titolo, poi prova a risolvere tramite la libreria:
    1) match diretto per chiave canonica
    2) match via alias
    Altrimenti ritorna il titolo pulito.
    """
    cleaned = clean_title_tokens(title)
    if not cleaned:
        return title or ""

    maps = _load_aliases(aliases_path)
    key = canonical_title_key(cleaned)

    # 1) match diretto
    real = maps["canon_to_real"].get(key)
    if real:
        return real

    # 2) match tramite alias
    real = maps["alias_to_real"].get(key)
    if real:
        return real

    # nessuna corrispondenza: usa il pulito
    return cleaned

# =======================================================
# ==================  FILTRI FILE/PERCORSI  =============
# =======================================================

# --- Esclusione directory: robusta e case-insensitive ---
EXCLUDE_DIR_KEYWORDS_LOWER = {k.lower() for k in EXCLUDE_DIR_KEYWORDS}

def _is_dir_excluded(p: Path) -> bool:
    # Nome normalizzato: minuscolo, '_' e '-' trattati come spazi, spazi compattati
    name = p.name.lower().replace('_', ' ').replace('-', ' ')
    name = re.sub(r'\s{2,}', ' ', name).strip()
    # Confronto su set già lower
    return any(k in name for k in EXCLUDE_DIR_KEYWORDS_LOWER)

def _is_file_excluded(name: str) -> bool:
    low = name.lower()
    ext = Path(name).suffix.lower()
    if ext in EXCLUDE_FILE_EXT:
        return True
    if any(k in low for k in EXCLUDE_FILE_KEYWORDS):
        return True
    return False

def _strip_untrusted_sequel_numbers(title: str) -> str:
    """
    Fallback per i casi in cui non usiamo ProductName:
    rimuove numeri/romani finali “sospetti” per evitare falsi sequel.
    """
    if not title:
        return title
    s = title
    s = re.sub(r'\b\d{1,3}\b\s*-\s*', '', s)  # "Franchise 13 - Subtitle" -> "Franchise - Subtitle"
    s = re.sub(r'[\s\-:]*\b(\d{1,4}|[IVXLCDM]{1,6})\b\s*$', '', s, flags=re.IGNORECASE)  # "... 2"/"... III" in coda
    s = re.sub(r'\s{2,}', ' ', s)
    s = re.sub(r'\s*[-_:]\s*$', '', s)
    return s.strip()

def _is_candidate_file(name: str) -> bool:
    """Prima filtri di esclusione, poi whitelist: ISO/ARCHIVI o EXE/MSI 'installer-like'."""
    if _is_file_excluded(name):
        return False
    ext = Path(name).suffix.lower()
    if ext in SUPPORTED_ISO or ext in SUPPORTED_ARCHIVES:
        return True
    if ext in (".exe", ".msi"):
        low = name.lower()
        # accetta anche autorun.exe (ci pensa lo scoring a decidere)
        if ("setup" in low) or ("install" in low) or low == "autorun.exe":
            return True
        return False
    return False


# =======================================================
# ============   NORMALIZZAZIONE TITOLI   ===============
# =======================================================

def clean_title_tokens(s: str) -> str:
    if not s:
        return s

    # 0) FIX PRIORITARI: applica subito le correzioni custom
    for rx, repl in TITLE_FIX_MAP:
        s = rx.sub(repl, s)

    # 1) normalizza apostrofi e trattini "strani"
    s = s.replace("’", "'").replace("`", "'").replace("–", "-").replace("—", "-")

    # 2) rimuovi rumore (con simboli opzionali davanti) e blocchi tra parentesi
    s = RE_NOISE_BLOCKS.sub(' ', s)
    s = RE_BRACKETS.sub(' ', s)

    # 3) rimuovi versioni in coda + rumore tecnico
    s = RE_TRAIL_VERSION_ONLY.sub(' ', s)
    s = CLEAN_RX.sub(' ', s)
    s = VERSION_RX.sub(' ', s)

    # 4) spazi/strip
    s = re.sub(r'\s*-\s*', ' - ', s)
    s = re.sub(r'\s{2,}', ' ', s).strip(' -_.:\t')

    # 5) normalizzazione speciale per Assassin's Creed (opzionale)
    m = re.match(r"^(Assassin's Creed)(.*)$", s, flags=re.IGNORECASE)
    if m:
        prefix, rest = m.groups()
        rest = rest.strip()
        if rest:
            if rest.startswith(":"):
                rest = "- " + rest[1:].strip()
            elif not rest.startswith("-"):
                rest = "- " + rest
            s = f"{prefix} {rest}"

    # 6) compattazione finale
    s = re.sub(r'\s*-\s*', ' - ', s)
    s = re.sub(r'\s{2,}', ' ', s).strip()

    return s

def canonical_title_key(s: str) -> str:
    """Chiave canonicizzata per dedup: minuscolo + token puliti."""
    return clean_title_tokens(s or "").lower()


# =======================================================
# ========= WinAPI VersionInfo (ctypes) =========
# =======================================================

ver = ctypes.windll.version if os.name == "nt" else None
LPVOID = ctypes.c_void_p

def _get_file_version_block(path):
    if not ver:
        return None
    size = ver.GetFileVersionInfoSizeW(path, None)
    if not size:
        return None
    buf = ctypes.create_string_buffer(size)
    ok = ver.GetFileVersionInfoW(path, 0, size, buf)
    if not ok:
        return None
    return buf, size

def _ver_query(buf, sub_block):
    if not ver:
        return (None, 0)
    lp = LPVOID()
    lsize = ctypes.c_uint()
    ok = ver.VerQueryValueW(buf, ctypes.c_wchar_p(sub_block), ctypes.byref(lp), ctypes.byref(lsize))
    if not ok:
        return None, 0
    return lp, lsize.value

def read_version_info(path):
    res = {}
    if os.name != "nt":
        return res
    blk = _get_file_version_block(path)
    if not blk:
        return res
    buf, _ = blk

    lp, lsize = _ver_query(buf, r"\VarFileInfo\Translation")
    translations = []
    if lp and lsize >= 4:
        count_words = lsize // 2
        arr_type = ctypes.c_ushort * count_words
        arr = ctypes.cast(lp, ctypes.POINTER(arr_type)).contents
        for i in range(0, count_words - 1, 2):
            lang = arr[i]
            code = arr[i + 1]
            if lang == 0 and code == 0:
                break
            translations.append((lang, code))
    fallbacks = [(0x0409, 0x04E4), (0x0409, 0x04B0), (0x0000, 0x04B0), (0x0000, 0x04E4)]
    for fb in fallbacks:
        if fb not in translations:
            translations.append(fb)

    extended_keys = [
        "ProductName", "FileDescription", "CompanyName", "ProductVersion", "FileVersion",
        "OriginalFilename", "InternalName", "LegalCopyright", "Comments",
        "Title", "ProductTitle", "AppName", "ApplicationName", "DisplayName", "DisplayVersion",
        "Inno Setup: App Name", "Inno Setup: App Version", "NSIS: Name", "NSIS: Version",
    ]

    rich = {}
    for lang, code in translations:
        base = f"\\StringFileInfo\\{lang:04X}{code:04X}\\"
        any_found = False
        for k in extended_keys:
            sub = base + k
            lp2, _ = _ver_query(buf, sub)
            if lp2:
                s = ctypes.wstring_at(lp2)
                if s:
                    rich[k] = s
                    any_found = True
        if any_found:
            res.update(rich)
            break

    # VS_FIXEDFILEINFO (versioni numeriche utili)
    lp_root, _ = _ver_query(buf, "\\")
    if lp_root:
        class VS_FIXEDFILEINFO(ctypes.Structure):
            _fields_ = [
                ("dwSignature", ctypes.c_uint),
                ("dwStrucVersion", ctypes.c_uint),
                ("dwFileVersionMS", ctypes.c_uint),
                ("dwFileVersionLS", ctypes.c_uint),
                ("dwProductVersionMS", ctypes.c_uint),
                ("dwProductVersionLS", ctypes.c_uint),
                ("dwFileFlagsMask", ctypes.c_uint),
                ("dwFileFlags", ctypes.c_uint),
                ("dwFileOS", ctypes.c_uint),
                ("dwFileType", ctypes.c_uint),
                ("dwFileSubtype", ctypes.c_uint),
                ("dwFileDateMS", ctypes.c_uint),
                ("dwFileDateLS", ctypes.c_uint),
            ]
        ffi = ctypes.cast(lp_root, ctypes.POINTER(VS_FIXEDFILEINFO)).contents
        if ffi.dwSignature == 0xFEEF04BD:
            fv = ((ffi.dwFileVersionMS >> 16) & 0xFFFF, (ffi.dwFileVersionMS) & 0xFFFF,
                  (ffi.dwFileVersionLS >> 16) & 0xFFFF, (ffi.dwFileVersionLS) & 0xFFFF)
            pv = ((ffi.dwProductVersionMS >> 16) & 0xFFFF, (ffi.dwProductVersionMS) & 0xFFFF,
                  (ffi.dwProductVersionLS >> 16) & 0xFFFF, (ffi.dwProductVersionLS) & 0xFFFF)
            res.setdefault("FileVersionFixed", ".".join(map(str, fv)))
            res.setdefault("ProductVersionFixed", ".".join(map(str, pv)))
    return res


# =======================================================
# ===============  FILTRI TITOLO “PUZZOSI”  =============
# =======================================================

def _is_banned_title(s: str) -> bool:
    if not s:
        return True
    low = s.strip().lower()
    return low in BANNED_TITLES_EXACT

def _desc_contains_banned(s: str) -> bool:
    if not s:
        return False
    low = s.lower()
    return any(b in low for b in BANNED_IN_FILEDESCRIPTION)

def _is_meaningful_folder_name(s: str) -> bool:
    """Scarta nomi cartella generici/rumorosi; accetta se resta qualcosa di 'titolo'."""
    if not s:
        return False
    cleaned = clean_title_tokens(s)
    if not cleaned:
        return False
    if _is_banned_title(cleaned):
        return False
    # evita pattern iper-generici tipo 'disc 1', 'bin', 'setup files'
    if re.fullmatch(r'(disc\s*\d+|cd\s*\d+|dvd\s*\d+|bin|setup\s*files?)', cleaned, flags=re.IGNORECASE):
        return False
    return True


# =======================================================
# =============  STIMA TITOLO (pre-risoluzione) =========
# =======================================================

def guess_title_from_vi_or_path(path: Path, vi: dict | None):
    """
    Regole:
    - Se ProductName è valido -> usare SOLO quello (mantiene eventuali numeri sequel).
    - Se manca ProductName -> si considerano FileDescription (se non 'puzzosa'),
      nome cartella, OriginalFilename, basename file; su questi si rimuovono i 'numeri sequel' non affidabili.
    """
    candidates_untrusted = []

    # 0) Dati base
    pn = (vi or {}).get("ProductName") if vi else None
    fd = (vi or {}).get("FileDescription") if vi else None
    ofn = (vi or {}).get("OriginalFilename") if vi else None

    # 1) ProductName: PRIORITARIO ASSOLUTO
    if pn:
        ct = clean_title_tokens(pn)
        if ct and not _is_banned_title(ct):
            return ct[:120]

    # 2) FileDescription, solo se NON contiene parole bannate (setup/uninstall/…)
    if fd and not _desc_contains_banned(fd):
        candidates_untrusted.append(fd)

    # 3) Nome cartella
    parent = os.path.basename(os.path.dirname(str(path))) or ''
    if _is_meaningful_folder_name(parent):
        candidates_untrusted.append(parent)

    # 4) OriginalFilename (raramente utile, ma ci proviamo)
    if ofn:
        candidates_untrusted.append(ofn)

    # 5) Basename del file (exe/msi/iso)
    base = os.path.basename(str(path))
    base = re.sub(r'\.(exe|msi|iso)$', '', base, flags=re.IGNORECASE)
    if base:
        candidates_untrusted.append(base)

    # Clean + strip numeri "non affidabili" sui fallback
    cleaned = []
    for c in candidates_untrusted:
        ct = clean_title_tokens(c)
        if not ct or _is_banned_title(ct):
            continue
        ct = _strip_untrusted_sequel_numbers(ct)
        if ct and not _is_banned_title(ct):
            cleaned.append(ct)

    if cleaned:
        # Preferisci stringhe più 'ricche' (più parole) ma non privilegiare i numeri
        cleaned.sort(key=lambda x: (-len(x.split()), len(x)))
        return cleaned[0][:120]

    return None


# =======================================================
# ========= ISO mount / scan =========
# =======================================================

CREATE_NO_WINDOW = 0x08000000

def _run_ps(cmd):
    full = ['powershell.exe', '-NoProfile', '-ExecutionPolicy', 'Bypass', '-Command', cmd]
    si = subprocess.STARTUPINFO()
    si.dwFlags |= subprocess.STARTF_USESHOWWINDOW
    si.wShowWindow = 0
    try:
        out = subprocess.check_output(
            full, stderr=subprocess.STDOUT, startupinfo=si,
            creationflags=CREATE_NO_WINDOW, text=True
        )
        return 0, out.strip()
    except subprocess.CalledProcessError as e:
        return e.returncode, (e.output or '').strip()

def mount_iso(image_path):
    image_path = os.path.abspath(image_path)
    ps = (
        f"$img = '{image_path}'.Replace(\"'\",\"''\"); "
        f"Mount-DiskImage -ImagePath $img -ErrorAction Stop | Out-Null; "
        f"$d = (Get-DiskImage -ImagePath $img | Get-Volume | Select -Expand DriveLetter); "
        f"Write-Output ($d + ':\\')"
    )
    code, out = _run_ps(ps)
    if code != 0 or not out or not re.match(r'^[A-Z]:\\$', out):
        return None
    time.sleep(0.5)
    return out

def dismount_iso(image_path):
    image_path = os.path.abspath(image_path)
    ps = f"$img = '{image_path}'.Replace(\"'\",\"''\"); Dismount-DiskImage -ImagePath $img -ErrorAction SilentlyContinue"
    _run_ps(ps)


# =======================================================
# ========= Installer discovery in folder =========
# =======================================================

EXE_EXTS = ('.exe', '.msi')
# NON escludiamo 'autorun' qui; lo valuteremo nello scoring
EXE_NAME_SKIP = re.compile(
    r'(quicksfv|dxsetup|vcredist|vc_redist|directx|physx|dotnet|netfx|'
    r'unins|uninstall|depots|redistributable|update|patch)',
    re.IGNORECASE
)

def find_installers_in_folder(root_folder):
    """Cerca .exe/.msi validi in una cartella (ricorsivo)."""
    candidates = []
    root_folder = os.path.abspath(root_folder)
    for dirpath, dirnames, filenames in os.walk(root_folder):
        depth = os.path.relpath(dirpath, root_folder).count(os.sep)
        for fn in filenames:
            ext = os.path.splitext(fn)[1].lower()
            if ext not in EXE_EXTS:
                continue
            if EXE_NAME_SKIP.search(fn) or EXE_NAME_SKIP.search(os.path.splitext(fn)[0]):
                continue
            fp = os.path.join(dirpath, fn)
            try:
                size = os.path.getsize(fp)
            except Exception:
                size = 0
            candidates.append({"path": fp, "size": size, "depth": depth, "name": fn})
    return candidates

def score_candidate(c):
    """Scoring con preferenze per installer reali; bonus anche per autorun.exe grande."""
    import math
    import os as _os
    s = 0.0
    size = max(c["size"], 1)

    # 1) Più grande = meglio
    s += math.log2(size + 1)

    # 2) Vicino alla root = meglio (depth 0,1)
    s += max(0, 4 - c["depth"]) * 2

    base = _os.path.basename(c["name"]).lower()

    # 3) Leggera preferenza a .exe
    if c["path"].lower().endswith('.exe'):
        s += 1.5

    # 4) Bonus forte per setup.exe
    if base == 'setup.exe':
        s += 6

    # 5) Bonus moderato per nomi che contengono "setup" o "install"
    if 'setup' in base or 'install' in base:
        s += 3

    # 6) autorun.exe: se è grande, è spesso l'installer principale (InstallShield)
    if base == 'autorun.exe':
        s += 5
        if size > 1 * 1024 * 1024:
            s += 2

    # 7) Penalizza file minuscoli
    if size < 500 * 1024:   # < 0.5 MB
        s -= 4

    return s

def pick_best_installer(candidates):
    if not candidates:
        return None
    scored = [(score_candidate(c), c) for c in candidates]
    scored.sort(key=lambda x: x[0], reverse=True)
    return scored[0][1]


# =======================================================
# ========= Title extractor orchestrator =========
# =======================================================

def extract_title_for_item(item: Path) -> str:
    """
    Ritorna un titolo 'migliore' per l'item (pulito ma NON ancora risolto su libreria):
    - ISO: monta, sceglie installer migliore, legge VersionInfo, stima titolo.
    - EXE/MSI: legge VersionInfo, stima titolo.
    - Cartella: cerca installer migliore, legge VersionInfo, stima titolo.
    Fallback: nome file/cartella pulito.
    """
    try:
        if item.is_file():
            ext = item.suffix.lower()
            if ext in SUPPORTED_ISO:
                drive = mount_iso(str(item))
                if not drive:
                    return guess_title_from_vi_or_path(str(item), {})
                try:
                    best = pick_best_installer(find_installers_in_folder(drive))
                    if not best:
                        return guess_title_from_vi_or_path(str(item), {})
                    vi = read_version_info(best["path"])
                    return guess_title_from_vi_or_path(best["path"], vi)
                finally:
                    dismount_iso(str(item))

            if ext in (".exe", ".msi"):
                vi = read_version_info(str(item))
                return guess_title_from_vi_or_path(str(item), vi)

            return guess_title_from_vi_or_path(str(item), {})

        # Cartella: cerca il miglior installer dentro
        best = pick_best_installer(find_installers_in_folder(str(item)))
        if best:
            vi = read_version_info(best["path"])
            return guess_title_from_vi_or_path(best["path"], vi)

        return guess_title_from_vi_or_path(str(item), {})
    except Exception:
        return guess_title_from_vi_or_path(str(item), {})


# =======================================================
# ======================== ID ===========================
# =======================================================

def _stable_id(path: Path) -> str:
    return hashlib.sha1(str(path.resolve()).encode("utf-8")).hexdigest()


# =======================================================
# ====================== SERVICE ========================
# =======================================================

class LibraryService:
    def __init__(self, settings, events):
        self.settings = settings
        self.events = events
        # stato live per il frontend
        self._scan_active = False
        self._current_scan_dir = ""


        # Precarica alias JSON (una volta). Puoi passare settings["aliases_path"] per path custom.
        aliases_path = _get_setting(self.settings, "aliases_path", None)
        _load_aliases(aliases_path)

    def _emit(self, topic: str, payload: dict):
        if self.events:
            try:
                self.events.emit(topic, payload)
            except Exception:
                pass

    def scan_folder(self, folder: str) -> dict:
        base = Path(folder)
        if not base.exists():
            return {"ok": False, "error": f"Folder not found: {folder}"}
    
        conn = get_conn()
        cur = conn.cursor()
    
        # Precarica chiavi titolo già presenti in DB (dedup fra scansioni diverse)
        cur.execute("SELECT title FROM games")
        existing_title_keys = { canonical_title_key(row["title"]) for row in cur.fetchall() }
    
        # === Stato LIVE: inizio ===
        self._scan_active = True
        self._current_scan_dir = str(base)
        self._emit("library/status", {"stage": "start", "folder": str(base)})
        # ==========================
    
        try:
            # --- Costruzione candidati (no duplicati) ---
            candidates_set = set()
            for root, dirs, files in os.walk(base):
                p = Path(root)
    
                # 1) Se la cartella corrente è ESCLUSA: annuncia e blocca la discesa
                if _is_dir_excluded(p):
                    self._emit("library/status", {
                        "stage": "scan-skip",
                        "dir": str(p),
                        "reason": "excluded-dir"
                    })
                    dirs[:] = []   # impedisce a os.walk di scendere qui sotto
                    continue
                
                # 2) PRUNA SUBITO le sottocartelle escluse
                dirs[:] = [d for d in dirs if not _is_dir_excluded(p / d)]
    
                # 3) LIVE: cartella valida → aggiorna stato ed emetti 'scan'
                self._current_scan_dir = str(p)
                self._emit("library/status", {"stage": "scan", "dir": self._current_scan_dir})
    
                lower_files = {f.lower() for f in files}
                has_installer = any(h in lower_files for h in INSTALL_HINTS)
    
                # Se c'è un installer "classico" nella cartella → indicizza la cartella come candidato
                if has_installer:
                    candidates_set.add(str(p.resolve()))
                    continue
                
                # Mappa dei RAR per stem (nome senza estensione) per decidere se saltare gli .exe
                rar_stems = {Path(fname).stem.lower() for fname in files if fname.lower().endswith(".rar")}
    
                for f in files:
                    if not _is_candidate_file(f):
                        continue
                    
                    full = p / f
    
                    # Ridondante ma sicuro: se per qualche motivo p non è esclusa, ricontrolla
                    if _is_dir_excluded(full.parent):
                        continue
                    
                    fl = f.lower()
                    stem = Path(f).stem.lower()
    
                    # Se esiste un .rar con lo stesso stem, salta l'.exe
                    if fl.endswith(".exe") and stem in rar_stems:
                        self._emit("library/status", {
                            "stage": "found",
                            "item": str(full.resolve()),
                            "kind": "file",
                            "note": "skipped-exe-same-stem-rar"
                        })
                        continue
                    
                    candidates_set.add(str(full.resolve()))
    
            candidates = [Path(s) for s in sorted(candidates_set)]
    
            # --- Unico loop: estrai, risolvi con alias, dedup, indicizza ---
            found = 0
            seen_titles = set()  # dedup intra-scan per titolo "canonicizzato"
    
            # path custom per il JSON, se definito
            aliases_path = _get_setting(self.settings, "aliases_path", None)
    
            for item in candidates:
                # 1) Estrai titolo "raw" (VI, descrizione, cartella, ISO) con heartbeat per ISO
                raw_title = self._extract_title_with_heartbeat(item)
                
                if not raw_title:
                    self._emit("library/status", {
                        "stage": "error",
                        "item": str(item.resolve()),
                        "error": "empty-title-fallback"
                    })
                    continue
                
                # 2) Risolvi con libreria alias (fonte finale del titolo esposto)
                title = resolve_with_aliases(raw_title, aliases_path)
                title_key = canonical_title_key(title)
    
                # 3) Dedup intra-scan
                if title_key in seen_titles:
                    self._emit("library/status", {
                        "stage": "found",
                        "item": str(item.resolve()),
                        "kind": ("file" if item.is_file() else "folder"),
                        "note": "skipped-duplicate-title"
                    })
                    continue
                
                # 4) Dedup vs DB (cross-scan)
                if title_key in existing_title_keys:
                    self._emit("library/status", {
                        "stage": "found",
                        "item": str(item.resolve()),
                        "kind": ("file" if item.is_file() else "folder"),
                        "note": "skipped-duplicate-title-db"
                    })
                    continue
                
                # Marca come visto in questa scansione (evita doppi insert nella stessa run)
                seen_titles.add(title_key)
    
                gid = _stable_id(item)
                path_str = str(item.resolve())
    
                try:
                    cur.execute(
                        """
                        INSERT INTO games (id, title, path, status)
                        VALUES (?, ?, ?, 'indexed')
                        ON CONFLICT(id) DO UPDATE SET
                          title=excluded.title,
                          path=excluded.path
                        """,
                        (gid, title, path_str),
                    )
                    conn.commit()
    
                    found += 1
                    existing_title_keys.add(title_key)  # aggiorna cache cross-scan
    
                    # Log 'found' SOLO dopo inserimento riuscito
                    self._emit("library/status", {
                        "stage": "found",
                        "item": path_str,
                        "kind": ("file" if item.is_file() else "folder"),
                        "title": title
                    })
    
                except Exception as e:
                    self._emit("library/status", {
                        "stage": "error",
                        "item": path_str,
                        "error": str(e)
                    })
    
            conn.close()
    
            self._emit("library/status", {"stage": "done", "count": found})
            return {"ok": True, "indexed": found}
    
        except Exception as e:
            # Errore alto livello
            self._emit("library/status", {"stage": "error", "error": str(e)})
            try:
                conn.close()
            except Exception:
                pass
            return {"ok": False, "error": str(e)}
    
        finally:
            # === Stato LIVE: fine (sempre) ===
            self._scan_active = False
            self._current_scan_dir = ""
    

    def get_games(self) -> list[dict]:
        conn = get_conn()
        cur = conn.cursor()
        cur.execute(
            """
            SELECT id, title, path,
                   COALESCE(cover_path, '') AS cover_path,
                   COALESCE(status, 'indexed') AS status
            FROM games
            ORDER BY title COLLATE NOCASE ASC, id ASC
            """
        )
        rows = [dict(r) for r in cur.fetchall()]
        conn.close()
        return rows

    def get_scan_status(self) -> dict:
        """Ritorna lo stato corrente della scansione per il polling del frontend."""
        return {
            "active": bool(self._scan_active),
            "dir": self._current_scan_dir or ""
        }
    
    def _extract_title_with_heartbeat(self, item: Path) -> str:
        """
        Per i file .iso, esegue extract_title_for_item in un thread separato e,
        mentre il mount/lettura è in corso, aggiorna _current_scan_dir per il mini-log
        SENZA emettere eventi (evitiamo di spammare il drawer).
        Per tutti gli altri item delega direttamente alla funzione originale.
        """
        try:
            is_iso = item.is_file() and item.suffix.lower() == ".iso"
        except Exception:
            is_iso = False
    
        if not is_iso:
            return extract_title_for_item(item)
    
        box = {"title": None, "err": None}
    
        def worker():
            try:
                box["title"] = extract_title_for_item(item)
            except Exception as e:
                box["err"] = e
    
        t = Thread(target=worker, daemon=True)
        t.start()
    
        # Heartbeat: aggiorna SOLO lo stato live, niente emit
        while t.is_alive():
            self._current_scan_dir = str(item)   # mostriamo l'ISO nel mini-log
            time.sleep(0.15)                     # cede il GIL: UI sempre reattiva
    
        # Fine mount ISO
        if box["err"] is not None:
            raise box["err"]
    
        # Riporta il focus alla cartella padre e fai un singolo emit di "scan"
        try:
            parent = item.parent
            self._current_scan_dir = str(parent)
            self._emit("library/status", {"stage": "scan", "dir": self._current_scan_dir})
        except Exception:
            pass
        
        return box["title"]

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/services/settings/store.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendservicessettingsstorepy"></a>

```python
import json, shutil, os
from pathlib import Path

ROOT = Path(__file__).resolve().parents[3]  # points to /app
CONFIG = ROOT.parent / "config"
USER = CONFIG / "user.json"
DEFAULTS = CONFIG / "defaults.json"
SCHEMA = CONFIG / "schema.json"

class SettingsStore:
    def __init__(self, events):
        self.events = events
        self._ensure_files()

    def _ensure_files(self):
        CONFIG.mkdir(parents=True, exist_ok=True)
        if not USER.exists() and DEFAULTS.exists():
            shutil.copy(DEFAULTS, USER)

    def get_user_settings(self):
        return json.loads(USER.read_text(encoding="utf-8"))

    def set_setting(self, path, value):
        # path es: "ui.theme" oppure "install.silent_7z"
        data = self.get_user_settings()
        keys = path.split(".")
        ref = data
        for k in keys[:-1]:
            ref = ref.setdefault(k, {})
        ref[keys[-1]] = value
        USER.write_text(json.dumps(data, indent=2), encoding="utf-8")
        self.events.emit("settings/changed", {"path": path, "value": value})
        return True

    def restore_defaults(self):
        shutil.copy(DEFAULTS, USER)
        self.events.emit("settings/restored", self.get_user_settings())
        return True
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/utils/cleanup_logs.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendutilscleanup-logspy"></a>

```python
# app/backend/utils/cleanup_logs.py
"""
Pulizia automatica dei file di log nella cartella runtime/logs.
Mantiene solo gli N log più recenti (default: 10) e rimuove gli altri.
"""

from pathlib import Path

def cleanup_old_logs(keep_last: int = 10) -> int:
    """
    Elimina i log più vecchi nella cartella runtime/logs mantenendo
    solo i più recenti 'keep_last'.

    Ritorna il numero di file eliminati.
    """
    base = Path.cwd() / "runtime" / "logs"   # <-- allineato a save_log_file
    if not base.exists():
        return 0

    logs = sorted(
        base.glob("log_*.txt"),
        key=lambda f: f.stat().st_mtime,
        reverse=True
    )

    # Se i log sono <= keep_last, non fare nulla
    if len(logs) <= keep_last:
        return 0

    # Elimina tutto ciò che eccede i keep_last più recenti
    to_delete = logs[keep_last:]
    deleted = 0

    for f in to_delete:
        try:
            f.unlink(missing_ok=True)
            deleted += 1
        except Exception:
            # ignora eventuali errori (file in uso, permessi, ecc.)
            pass

    return deleted
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/backend/utils/events.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappbackendutilseventspy"></a>

```python
# app/backend/utils/events.py
from collections import deque
from datetime import datetime

class EventBus:
    def __init__(self, max_buffer=500):
        self.subs = {}
        self.buffer = deque(maxlen=max_buffer)

    def emit(self, topic: str, payload: dict | None):
        event = {
            "topic": topic,
            "ts": datetime.now().isoformat(timespec="seconds"),
            "payload": payload or {}
        }
        self.buffer.append(event)
        for cb in self.subs.get(topic, []):
            try:
                cb(event)
            except Exception:
                pass

    def get_recent(self, topic: str | None = None, limit: int = 100):
        items = list(self.buffer)[-limit:]
        if topic:
            items = [e for e in items if e["topic"] == topic]
        return items
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/css/app.css
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendcssappcss"></a>

```css
/* ========== BASE / RESET ==================================================== */
*{box-sizing:border-box}
html,body,#app{height:100%}
body{margin:0; background:var(--bg); color:var(--text); font-family:system-ui,Segoe UI,Roboto,Arial}
body { font-size: 15px; line-height: 1.5; }
h2, h3 { letter-spacing: .3px; }
.settings-row { padding: 14px 0; } /* un po’ più “aria” */

/* Testi sempre leggibili */
body,.sidebar,.main,.view,.panel,.empty,.toolbar,.topbar,.settings,.settings-row label,.status,.nav-btn{color:var(--text)}

/* Input/select coerenti col tema (prima definizione, verrà affinata più sotto) */
input[type="text"],input[type="search"],select{color:var(--text); background:var(--panel-2); border:1px solid var(--line)}

/* Etichette impostazioni */
.settings-row label{font-weight:600; color:var(--text)}

/* Area bottoni impostazioni + dimensioni “compatte” */
.settings-actions {display: flex;  align-items: center;  justify-content: flex-end;  gap: 12px; margin-top:24px;  margin-bottom: 8px;  padding: 18px 16px 0;  border-top: 1px solid var(--line);  position: relative;}
/* Linea luminosa viola sopra */
.settings-actions::before {content: ""; position: absolute; top: 0; left: 0; width: 100%; height: 1px; background: linear-gradient(   90deg,   rgba(111, 77, 239, 0) 0%,   rgba(111, 77, 239, 0.6) 35%,   rgba(111, 77, 239, 0.8) 50%,   rgba(111, 77, 239, 0.6) 65%,   rgba(111, 77, 239, 0) 100% ); pointer-events: none;}
.settings-actions button{height:34px; padding:0 18px; font-size:13px; border-radius:8px}
/* ========== BUTTONS (tipi e varianti) ====================================== */
button{display:inline-flex;  align-items:center;  justify-content:center;  gap:8px; font-weight:600;  line-height:1.2;  border-radius:var(--radius);  cursor:pointer;  padding:8px 16px; min-height:42px; height:auto; box-sizing:border-box;  border:1px solid transparent;  transition: background .25s, border-color .25s, color .25s, transform .1s, box-shadow .25s;}
button:active { transform: scale(0.97); }
button:focus-visible{ outline:2px solid var(--accent); outline-offset:2px; }
  
/* ========== BUTTON: VARIANTE PRIMARY ======================================= */
button.primary{background: var(--accent); color:#fff !important; border: none; box-shadow: 0 0 0 rgba(0,0,0,0);}
button.primary:hover{filter: brightness(1.05); box-shadow: 0 0 8px -2px var(--accent);}
button.primary:active{filter: brightness(0.98); box-shadow: 0 0 6px -3px var(--accent);}

/* ========== BUTTON: VARIANTE GHOST (usata da Filtri, Mostra Log, Salva Log) == */
button.ghost{background: var(--panel-2); color: var(--text); border:1px solid var(--line); border-radius:10px;}
button.ghost:hover{background: var(--panel); border-color: var(--accent); box-shadow:   0 0 8px -2px var(--accent),   inset 0 0 6px -4px var(--accent); transform: scale(1.02);}
button.ghost:active{transform: scale(0.97); box-shadow:   0 0 6px -3px var(--accent),   inset 0 0 6px -3px var(--accent);}

/* ========== BUTTON: VARIANTE DANGER ======================================== */
button.danger {background: var(--danger); color: #fff !important; border: none; box-shadow:   0 0 0 transparent,   inset 0 1px 0 rgba(255,255,255,0.12); transition: background .25s ease, box-shadow .25s ease, transform .15s ease;}
button.danger:hover {background: #ff6b6b; /* leggermente più chiaro */ box-shadow:   0 0 12px -3px rgba(255, 107, 107, 0.75),   inset 0 1px 0 rgba(255,255,255,0.15); transform: translateY(-1px); /* 🔹 effetto sollevamento */}
button.danger:active {transform: scale(0.97);filter: brightness(0.95);}

/* ========== BUTTON: DIMENSIONE SMALL ======================================= */
button.small{font-size:13px;padding:6px 12px;min-height:34px;height:auto;}

/* ========== BUTTON: ICON-ONLY (es. X chiusura) ============================= */
button.icon-btn{all: unset; position: relative; display:inline-flex; align-items:center; justify-content:center; width:32px; height:32px; border-radius:8px; cursor:pointer; color: var(--muted); background: linear-gradient(180deg, rgba(255,255,255,0.04), rgba(0,0,0,0.25)); border: 1px solid rgba(255,255,255,0.06); box-shadow: inset 0 1px 0 rgba(255,255,255,0.04); transition: background .25s, border-color .25s, transform .15s, box-shadow .25s, color .2s;}
button.icon-btn:hover{background: linear-gradient(180deg, rgba(111,77,239,0.12), rgba(111,77,239,0.08)); border-color: var(--accent); color: var(--text); box-shadow: 0 0 6px rgba(111,77,239,0.3), inset 0 1px 0 rgba(255,255,255,0.05); transform: rotate(90deg);}
button.icon-btn:active{ transform: scale(0.95) rotate(90deg); }
button.icon-btn:focus-visible{ outline: 2px solid var(--accent); outline-offset: 2px; }
button.icon-btn:hover::after{content:""; position:absolute; inset:0; border-radius:8px; box-shadow:0 0 8px rgba(111,77,239,0.4); pointer-events:none;}

/* ===================================================== */
/*  VARIANTE VIOLA GLOBALE (aggiungibile a qualsiasi btn) */
/* ===================================================== */
button.btn-purple {background: #7b5cff; color: #fff !important; border: none; box-shadow:   0 0 8px -3px rgba(123, 92, 255, 0.6),   inset 0 1px 0 rgba(255,255,255,0.1); transition: background .25s, box-shadow .25s, transform .1s;} 
/* Hover – leggera luce viola */
button.btn-purple:hover {background: #8a6bff; box-shadow:  0 0 12px -3px rgba(123, 92, 255, 0.8),  inset 0 1px 0 rgba(255,255,255,0.15); transform: translateY(-1px);} 
/* Active – effetto “pressato” */
button.btn-purple:active {background: #6f4def; box-shadow:   0 0 8px -4px rgba(111, 77, 239, 0.8),   inset 0 1px 0 rgba(255,255,255,0.08); transform: scale(0.97);}
/* Focus – contorno visibile */
button.btn-purple:focus-visible {outline: 2px solid #8a6bff; outline-offset: 2px;}

/* ============ */
/*  SOLO ICONE  */
/* ============ */
button.icon-btn{all:unset; display:inline-flex; align-items:center; justify-content:center; cursor:pointer}
/* Icone nei bottoni */
button img{width:18px; height:18px}
/* Mini-log in tempo reale */
#status-text {color: var(--status-text-color);}

/* ========== LAYOUT / STRUTTURA ============================================= */
.app{display:flex}

.sidebar{width:270px; background:linear-gradient(180deg,var(--panel),#0a0f19); border-right:1px solid var(--line); display:flex; flex-direction:column; padding:18px; gap:24px}
.sidebar{position: fixed; top: 0; left: 0; bottom: 0; height: 100vh; z-index: 1000;}
.brand{display:flex; align-items:center; gap:10px; font-weight:800; color:var(--brand); letter-spacing:.3px}
.brand-icon{width:24px; height:24px; filter:drop-shadow(0 2px 6px rgba(0,0,0,.2))}
.nav{display:flex; flex-direction:column; gap:14px; margin-top:8px}
.nav-btn{all:unset; display:flex; align-items:center; gap:10px; padding:12px; border-radius:12px; background:var(--panel-2); cursor:pointer; border:1px solid var(--line)}
.nav-btn.active{outline:2px solid var(--accent)}
.nav-btn img{width:18px; height:18px; opacity:.9}
.sidebar-footer{display: grid; margin-top: auto; grid-template-columns: auto 1fr; align-items: center; gap: 8px 32px; color: var(--muted);}
/* il bottone resta compatto, non si deforma */
#btn-theme-toggle{justify-self: start; white-space: nowrap; flex: none;}
/* il blocco versione prende lo spazio e può andare a capo */
.sidebar-footer .version{min-width: 0;}
/* il testo della versione può spezzare quando serve */
#ui-version{display: block; white-space: normal; overflow-wrap: anywhere; word-break: break-word; line-height: 1.35; font-size: 12px;}

.main{margin-left: 270px; padding-top: 72px;}
.main{flex:1; display:flex; flex-direction:column}
.topbar{position: fixed;  inset: 0 0 auto 270px;  z-index: 999;  display:flex;  align-items:center;  justify-content:space-between;  padding:16px 20px;  border-bottom:1px solid var(--line);  background: linear-gradient(0deg, rgba(0,0,0,0), rgba(0,0,0,.12));  -webkit-backdrop-filter: blur(6px);  backdrop-filter: blur(6px);  box-sizing: border-box;}
/* Linea luminosa alla base della topbar */
.topbar::after {content: ""; position: absolute; bottom: 0; left: 0; width: 100%; height: 1px; background: linear-gradient(90deg,   rgba(111, 77, 239, 0) 0%,   rgba(111, 77, 239, 0.6) 35%,   rgba(111, 77, 239, 0.8) 50%,   rgba(111, 77, 239, 0.6) 65%,   rgba(111, 77, 239, 0) 100% ); pointer-events: none; opacity: 0.9;}        
.search-wrap{display:flex; gap:10px; align-items:center; flex:1}
.search-wrap input{flex:1; padding:12px; border-radius:12px; border:1px solid var(--line); background:var(--panel-2); color:var(--text)}
.view{display:none; padding:20px;  }
.view.active{display:block}

.empty{margin:60px auto; max-width:680px; text-align:center; background:var(--panel); border:1px solid var(--line); border-radius:16px; padding:32px; box-shadow:var(--shadow)}
.empty img{width:220px; height:auto; opacity:.9}
.empty-actions{margin:16px 0}
.tips{display:grid; gap:10px; margin-top:10px}
.tip{background:var(--panel-2); border:1px solid var(--line); padding:10px; border-radius:10px; color:var(--muted); text-align:left}

.grid{display:grid; grid-template-columns:repeat(auto-fill,minmax(180px,1fr)); gap:var(--gap)}
.card{background:var(--panel); border:1px solid var(--line); border-radius:16px; overflow:hidden; display:flex; flex-direction:column; transition:transform .15s ease, box-shadow .2s ease}
.card:hover{transform:translateY(-2px); box-shadow:var(--shadow)}
.card img{width:100%; aspect-ratio:3/4; object-fit:cover; background:#0a0f19}
.card .meta{padding:10px}
.card .title{font-weight:700; margin:0 0 6px}
.card .tags{display:flex; gap:6px; flex-wrap:wrap}
.badge{font-size:11px; padding:4px 6px; border-radius:8px; background:var(--panel-2); border:1px solid var(--line); color:var(--muted)}

.panel{background:var(--panel); border:1px solid var(--line); border-radius:16px; padding:16px}
.placeholder-list{display:grid; gap:10px; margin-top:10px}
.skeleton{border-radius:8px; background:linear-gradient(90deg,rgba(255,255,255,.06),rgba(255,255,255,.12),rgba(255,255,255,.06)); background-size:200% 100%; animation:pulse 1.2s infinite}
.skeleton.line{height:14px}
@keyframes pulse{0%{background-position:200% 0}100%{background-position:-200% 0}}

/* ==================== */
/*  Bottom Bar (footer) */
/* ==================== */

.bottombar{position: sticky;  bottom: 0;  z-index: 99;  margin-top:auto;   border-top:1px solid var(--line);  backdrop-filter:blur(6px);   display:flex;   align-items:center; justify-content: space-between;   gap:22px;  padding:12px 22px;  background-color: #0d131e98; 
/* background:linear-gradient(0deg,rgba(0, 0, 0, 0.),rgba(0,0,0,.12)) */}
/* Linea luminosa superiore (coerente con topbar ma invertita) */
.bottombar::before {content: "";position: absolute;top: 0;left: 0;width: 100%;height: 1px;background: linear-gradient(90deg,  rgba(111, 77, 239, 0) 0%,  rgba(111, 77, 239, 0.6) 35%,  rgba(111, 77, 239, 0.8) 50%,  rgba(111, 77, 239, 0.6) 65%,  rgba(111, 77, 239, 0) 100%);pointer-events: none;opacity: 0.9;}
/* il mini-log prende lo spazio e può andare a capo, il bottone non si restringe */
.bottombar .status{ flex: 1 1 auto; min-width: 0; }
.bottombar button{ flex: 0 0 auto; }
.bottombar button{ white-space: nowrap; }

/* Testo di stato e bottone coerenti */
.status {color: var(--muted); font-size: 14px;}
#btn-log {margin-right: 10px; transition: all 0.25s ease;}
.status{color:var(--muted); font-size:14px; }

/* ========================== */
/*  Progres Bar Indeterminata */
/* ========================== */

/* Contenitore */
.progress{height: 8px; width: 280px; background: var(--panel-2); border: 1px solid var(--line); border-radius: 999px; overflow: hidden;          /* mantiene gli angoli arrotondati */ position: relative; isolation: isolate;        /* evita contaminazioni di blending esterni */ box-shadow:   inset 0 1px 0 rgba(255,255,255,.06),   inset 0 -1px 0 rgba(0,0,0,.25);}
/* Barra – COLORE PURO, nessun gradiente */
.progress .bar{height: 100%; width: 0%; background: var(--accent); position: relative; border-radius: inherit; transition: width .25s ease-out; overflow: visible; box-shadow:   0 0 10px 0 rgba(0,0,0,0),   0 0 14px -4px var(--accent),   0 0 24px -8px var(--accent);}
/* Sheen molto leggero: micro highlight in alto, non cambia tinta */
.progress .bar::before{content: ""; position: absolute; left: 0; right: 0; top: 0; height: 55%; background: linear-gradient(to bottom, rgba(255,255,255,.18), rgba(255,255,255,0)); pointer-events: none;}
/* Scia/glow aggiuntivo: stesso colore, solo sfumato */
.progress .bar::after{content: ""; position: absolute; inset: 0; background: var(--accent); filter: blur(8px); opacity: .40; pointer-events: none; mask-image: linear-gradient(90deg, transparent 0 6px, #000 22px calc(100% - 12px), transparent 100%); -webkit-mask-image: linear-gradient(90deg, transparent 0 6px, #000 22px calc(100% - 12px), transparent 100%);}

/* ===================== */
/*  Indeterminate: sweep + fade su entrambi i lati + inner glow */
/* ===================== */
.progress.indeterminate{--ind-bar-w: 28%; --ind-fade: 16px;}
.progress.indeterminate .bar{position: absolute; top: 0; height: 100%; width: var(--ind-bar-w); left: calc(-1 * var(--ind-bar-w)); background: var(--accent); transform: none; will-change: left; animation: indeterminate-run 1.05s linear infinite; -webkit-mask-image: linear-gradient(90deg,   rgba(0,0,0,0) 0%,   rgba(0,0,0,1) var(--ind-fade),   rgba(0,0,0,1) calc(100% - var(--ind-fade)),   rgba(0,0,0,0) 100%);         mask-image: linear-gradient(90deg,   rgba(0,0,0,0) 0%,   rgba(0,0,0,1) var(--ind-fade),   rgba(0,0,0,1) calc(100% - var(--ind-fade)),   rgba(0,0,0,0) 100%);}
/* Glow centrale (stesso colore, appena più chiaro), confinato dalla maschera */
.progress.indeterminate .bar::after{content: ""; position: absolute; inset: 0; background: radial-gradient(80% 140% at 50% 50%,   color-mix(in srgb, var(--accent) 88%, white 12%) 0%,   var(--accent) 55%,   transparent 85%); opacity: .50; pointer-events: none; -webkit-mask-image: inherit;         mask-image: inherit; animation: ind-glow-breathe 2.2s ease-in-out infinite;}
  
/* Movimento L→R: esce a destra e riparte da sinistra off-screen (no flicker) */
@keyframes indeterminate-run{
  0%   { left: calc(-1 * var(--ind-bar-w)); }
  100% { left: 100%; }
}

/* “respiro” del glow (non altera la tinta) */
@keyframes ind-glow-breathe{
  0%,100% { opacity: .42; }
  50%     { opacity: .62; }
}

/* Drawer overlay + pannello log (transizioni vere in open/close) */
.drawer{position: fixed; inset: 0; pointer-events: none; z-index: 1000;}
/* Scrim: fade in/out */
.drawer-scrim{position: absolute; inset: 0; z-index: 1; background: rgba(0,0,0,.45); -webkit-backdrop-filter: blur(6px); backdrop-filter: blur(6px); opacity: 0; transition: opacity .25s ease; will-change: opacity, backdrop-filter;}
.drawer.show .drawer-scrim{opacity:1}
/* Pannello: slide + fade */
.drawer-panel{position: absolute; right: 0; top: 0; height: 100%; width: 520px; background: var(--panel); border-left: 1px solid var(--line); box-shadow: var(--shadow); display: flex; flex-direction: column; z-index: 2; transform: translateX(12px); opacity: 0; transition: transform .25s ease, opacity .25s ease;}
/* Stato aperto: abilita click + porta scrim/pannello a 1 */
.drawer.show{pointer-events: auto;}
.drawer.show .drawer-scrim{ opacity: 1; }
.drawer.show .drawer-panel{transform: translateX(0); opacity: 1;}
/* HEADER DEL DRAWER */
.drawer-header {display: flex; align-items: center; justify-content: space-between; padding: 14px 16px; border-bottom: 1px solid var(--line); background: linear-gradient(0deg, rgba(0,0,0,.0), rgba(0,0,0,.12)); backdrop-filter: blur(6px); -webkit-backdrop-filter: blur(6px); position: relative;}
/* Linea luminosa sotto al titolo */
.drawer-header::after {content: ""; position: absolute; bottom: 0; left: 0; width: 100%; height: 1px; background: linear-gradient(90deg,   rgba(111, 77, 239, 0) 0%,   rgba(111, 77, 239, 0.6) 35%,   rgba(111, 77, 239, 0.8) 50%,   rgba(111, 77, 239, 0.6) 65%,   rgba(111, 77, 239, 0) 100% ); pointer-events: none;}
/* Titolo "Registro attività" o "CustomInfo Console" */
.drawer-header h3 {font-size: 16px; font-weight: 700; letter-spacing: 0.4px; display: flex; align-items: center; gap: 10px; margin: 0;}

/* ===================== */
/* Pulsante chiusura drawer (la “X”) – versione migliorata */
/* ===================== */
.drawer-header .icon-btn {
all: unset;
position: relative;
display: inline-flex;
align-items: center;
justify-content: center;
width: 32px;
height: 32px;
border-radius: 8px;
cursor: pointer;
transition: background .25s ease, transform .15s ease, border-color .25s ease;
color: var(--muted);
  
/* Stile base visibile ma sobrio */
background: linear-gradient(180deg, rgba(255,255,255,0.04), rgba(0,0,0,0.25));
border: 1px solid rgba(255,255,255,0.06);
box-shadow: inset 0 1px 0 rgba(255,255,255,0.04);}
  
.drawer-header .icon-btn:hover {background: linear-gradient(180deg, rgba(111,77,239,0.12), rgba(111,77,239,0.08)); border-color: var(--accent); color: var(--text); transform: rotate(90deg); box-shadow: 0 0 6px rgba(111,77,239,0.3), inset 0 1px 0 rgba(255,255,255,0.05);}
.drawer-header .icon-btn:active {transform: scale(0.95) rotate(90deg); background: linear-gradient(180deg, rgba(111,77,239,0.2), rgba(111,77,239,0.1)); border-color: color-mix(in srgb, var(--accent) 80%, black 20%);}
.drawer-header .icon-btn:focus-visible {outline: 2px solid var(--accent); outline-offset: 2px;}
  
/* Alone sottile al passaggio */
.drawer-header .icon-btn:hover::after {content: ""; position: absolute; inset: 0; border-radius: 8px; box-shadow: 0 0 8px rgba(111, 77, 239, 0.4); pointer-events: none;}
  
.logbox{flex:1; padding:16px; margin:0; overflow:auto; background:#0b0f19; border-top:1px solid var(--line)}
/* Footer del drawer: stile simile all’header ma più basso e sticky in fondo */
/* FOOTER DEL DRAWER (coerente con header, con linea luminosa) */
.drawer-footer {position: sticky; bottom: 0; z-index: 3; display: flex; align-items: center; justify-content:space-between; gap: 12px; padding: 10px 16px; border-top: 1px solid var(--line); background: linear-gradient(180deg, rgba(0,0,0,.0), rgba(0,0,0,.12)); -webkit-backdrop-filter: blur(6px); backdrop-filter: blur(6px); position: relative; overflow: hidden;}
/* Linea luminosa superiore (stesso stile dell’header ma invertita) */
.drawer-footer::before {content: ""; position: absolute; top: 0; left: 0; width: 100%; height: 1px; background: linear-gradient(90deg,   rgba(111, 77, 239, 0) 0%,   rgba(111, 77, 239, 0.6) 35%,   rgba(111, 77, 239, 0.8) 50%,   rgba(111, 77, 239, 0.6) 65%,   rgba(111, 77, 239, 0) 100% ); pointer-events: none;}

/* ========================== */
/* STATUS DEL DRAWER (LOG)    */
/* ========================== */
.drawer-status{font-size: 13px; font-weight: 500; letter-spacing: .2px; max-width: 70%; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; padding-left: 10px; position: relative; opacity: 0; color: var(--muted);}
/* pallino stato */
.drawer-status::before{content: ""; position: absolute; left: 0; top: 50%; transform: translateY(-50%); width: 5px; height: 5px; border-radius: 50%; background: currentColor; box-shadow: 0 0 6px color-mix(in srgb, currentColor 20%, transparent);}
/* ---------- Stati visibili + sheen interno (L→R, no flicker) ---------- */
.drawer-status.ok,
.drawer-status.error{
  --status-color: var(--accent);
  opacity: 1;

  /* 2 layer: base pieno + highlight mobile */
  background-image:
    linear-gradient(0deg, var(--status-color), var(--status-color)),     /* base */
    linear-gradient(90deg, transparent 0%,
      color-mix(in srgb, var(--status-color) 85%, white 15%) 50%,
      transparent 100%);                                                 /* sheen */
  background-size: 100% 100%, 30% 100%;
  background-position: 0 0, -35% 0;  /* parte off-screen a sinistra */
  background-repeat: no-repeat, no-repeat;

  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;                /* il testo mostra i layer */
  text-shadow: 0 0 3px color-mix(in srgb, var(--status-color) 18%, transparent);

  /* 1) comparsa dal basso  2) sheen L→R continuo */
  animation:
    status-pop-in .45s cubic-bezier(.25,1.1,.3,1) both,
    sheen-ltr 3.2s linear infinite .45s;

  will-change: transform, opacity, filter, background-position;
}

/* ERRORE in rosso tema */
.drawer-status.error{ --status-color: var(--danger); }

/* puntino allineato al colore dello stato */
.drawer-status.ok::before,
.drawer-status.error::before{
  background: var(--status-color);
  box-shadow: 0 0 8px color-mix(in srgb, var(--status-color) 30%, transparent);
}

/* Comparsa: dal basso verso la posizione finale (come avevi prima) */
@keyframes status-pop-in{
  0%   { opacity: 0; transform: translateY(6px) scale(.98); filter: blur(2px); }
  60%  { opacity: 1; transform: translateY(-1px) scale(1.01); filter: blur(0); }
  100% { opacity: 1; transform: translateY(0) scale(1); }
}

/* Sheen interno SINISTRA→DESTRA, rientro fluido (no lampeggio) */
@keyframes sheen-ltr{
  0%   { background-position: 0 0, -35% 0; }   /* sheen fuori a sinistra */
  100% { background-position: 0 0, 135% 0; }   /* sheen fuori a destra  */
}

/* Bottone “Salva Log” con micro-glow */
 #btn-save-log {margin-right: 4px; transition: all 0.25s ease;}
.settings{max-width:720px; margin:0 auto}
.settings-row{display:grid; grid-template-columns:220px 1fr; gap:12px; align-items:center; padding:10px 0; border-bottom:1px dashed var(--line)}
.settings-row:last-child{border-bottom:none}
/* Help aggiuntivo sotto la label nella griglia delle impostazioni */
/* Help aggiuntivo sotto la label (spaziato correttamente) */
.settings-row .help-label {
    grid-column: 1;               /* rimane nella colonna della label */
    align-self: start;
    font-size: 12px;
    line-height: 1.35;
    color: var(--muted);
    opacity: .95;
    margin-top: -85px;              /* <-- era 2px, ora 6 per separarlo meglio */
    padding-left: 2px;            /* piccolo offset per allinearsi otticamente */
    white-space: normal;
    font-style: italic;
  }
  
  
.chips{display:flex; gap:8px; flex-wrap:wrap}
.chip{padding:6px 8px; border-radius:999px; border:1px solid var(--line); background:var(--panel-2); display:flex; gap:6px; align-items:center}
.chip .x{cursor:pointer; opacity:.8}

/* ========== ICONS (per tema) =============================================== */
/* Dark → icone bianche */
[data-theme="dark"] .brand-icon,[data-theme="dark"] .nav-btn img,[data-theme="dark"] .topbar button img,[data-theme="dark"] .drawer-header .icon-btn img,[data-theme="dark"] .chips .x img,[data-theme="dark"] #add-lib img{filter:invert(1) brightness(1.2); opacity:.95}

/* Light → icone naturali */
[data-theme="light"] .brand-icon,[data-theme="light"] .nav-btn img,[data-theme="light"] .topbar button img,[data-theme="light"] .drawer-header .icon-btn img,[data-theme="light"] .chips .x img,[data-theme="light"] #add-lib img{filter:none; opacity:.95}

/* Icona sempre bianca su “Aggiungi giochi” */
#btn-add img{filter:invert(1) brightness(2)}

/* Spazio tra “Filtri” e “Aggiungi giochi” */
.topbar .actions{margin-left:12px}

/* ========== INPUTS & SELECTS (versione definitiva, più corposa) ============ */
input[type="text"],input[type="search"],select{color:var(--text); background:var(--panel-2); border:1px solid var(--line); border-radius:10px; padding:10px 14px; font-size:14px; height:40px; transition:border-color .2s,background .2s}
input[type="text"]:focus,input[type="search"]:focus,select:focus{outline:none; border-color:var(--accent); background:var(--panel)}

/* ========== NAVBAR HOVER (aggiornato con ombra) ============================ */
.nav-btn:hover {background: var(--panel); border-color: var(--accent); color: var(--text); transition: background .2s, border-color .2s, box-shadow .25s ease;
box-shadow:0 0 12px rgba(111, 77, 239, 0.25), inset 0 0 4px rgba(111, 77, 239, 0.15);}
.nav-btn:hover img {filter: invert(1) brightness(1.2); transition: filter .2s;}  
/* stato attivo (click o selezione): effetto “premuto” */
.nav-btn:active { transform: scale(0.97); box-shadow: 0 0 6px rgba(111, 77, 239, 0.2), inset 0 0 6px rgba(111, 77, 239, 0.25);}

/* ========== FILTRI (ghost dedicato) ======================================= */
#btn-filters{background:var(--panel); color:var(--text); border:1px solid var(--line)}
#btn-filters:hover {background: var(--panel-2); border-color: var(--accent); box-shadow:   0 0 8px -2px var(--accent),   inset 0 0 6px -4px var(--accent); transform: scale(1.02);}
  
/* ========== INFO Build (informazioni segli aggiornamenti ) =================== */
.status .sep { margin: 0 6px; color: var(--muted); }
.status-item { cursor: help; }           /* punta-dito: suggerisce tooltip */
.version #ui-version { cursor: help; }   /* idem per la versione UI */

/* Status in fondo al pannello Impostazioni */
.settings .status{display:flex; align-items:center; justify-content:flex-end; gap:6px; margin-top:16px; padding-top:12px; border-top:1px dashed var(--line); color:var(--muted); font-size:13px;}
.settings .status .sep{margin:0 6px; color:var(--muted); opacity:.85;}
.status-item{ cursor:help; }   /* evidenzia che ha un tooltip */

/* Logbox tuning */
#logbox {font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace; line-height: 1.4; white-space: pre-wrap; word-break: break-word;
}  

/* ========== SETTINGS: Tabs (CSS-only) ========== */
.settings-tabs { display: grid; gap: 12px; }
.settings-tabs > input[type="radio"]{ position:absolute; opacity:0; pointer-events:none; }

/* Barra schede */
.tab-nav{position: sticky; top: 0; z-index: 2; display: flex; gap: 12px; padding: 6px 2px 16px; background: var(--panel); border-bottom: 1px dashed var(--line);
}
/* Linea luminosa viola sotto */
.tab-nav::after {content: ""; position: absolute; bottom: 0; left: 0; width: 100%; height: 1px; background: linear-gradient(   90deg,   rgba(111, 77, 239, 0) 0%,   rgba(111, 77, 239, 0.6) 35%,   rgba(111, 77, 239, 0.8) 50%,   rgba(111, 77, 239, 0.6) 65%,   rgba(111, 77, 239, 0) 100% ); pointer-events: none;}
.tab-nav label{
  padding: 8px 12px; border-radius: 10px; cursor: pointer; user-select: none;
  background: var(--panel-2); border: 1px solid var(--line); font-weight: 600;
  transition: background .2s, border-color .2s, box-shadow .2s, transform .1s;
}
.tab-nav label:hover{
  background: var(--panel);
  border-color: var(--accent);
  box-shadow: 0 0 10px -3px var(--accent), inset 0 0 6px -5px var(--accent);
  transform: translateY(-1px);
}

/* Stato attivo per la label corrispondente */
#tab-library:checked ~ .tab-nav label[for="tab-library"],
#tab-scan:checked    ~ .tab-nav label[for="tab-scan"],
#tab-logs:checked    ~ .tab-nav label[for="tab-logs"],
#tab-ui:checked      ~ .tab-nav label[for="tab-ui"],
#tab-esc:checked      ~ .tab-nav label[for="tab-esc"],
#tab-adv:checked     ~ .tab-nav label[for="tab-adv"]{
  outline: 2px solid var(--accent);
}

/* Pannelli */
.tab-panels{ position: relative; }
.tab-panel{ display: none; padding-top: 6px; }
#tab-library:checked ~ .tab-panels .tab-panel[data-tab="tab-library"],
#tab-scan:checked    ~ .tab-panels .tab-panel[data-tab="tab-scan"],
#tab-logs:checked    ~ .tab-panels .tab-panel[data-tab="tab-logs"],
#tab-ui:checked      ~ .tab-panels .tab-panel[data-tab="tab-ui"],
#tab-esc:checked      ~ .tab-panels .tab-panel[data-tab="tab-esc"],
#tab-adv:checked     ~ .tab-panels .tab-panel[data-tab="tab-adv"]{ display: block; }

/* Riga inline per controlli a destra */
.row-inline{ display: flex; align-items: center; gap: 10px; flex-wrap: wrap; }
.stack{ display: grid; gap: 8px; }
/* Le righe della griglia occupano solo il contenuto reale */
.stack { grid-auto-rows: min-content; }

.textarea{
  width: 100%; min-height: 92px; resize: vertical;
  border: 1px solid var(--line); border-radius: 10px;
  background: var(--panel-2); color: var(--text); padding: 10px 14px;
}
.help{ color: var(--muted); font-size: 12px; font-style: italic;}
/* Cartelle libreria: elimina lo “spazio fantasma” quando non ci sono chip */
#lib-list:empty {
    display: none;
  }
  

/* ========== Switch (tema coerente) ========== */
.switch{ --w:46px; --h:26px; position:relative; width:var(--w); height:var(--h); flex:0 0 auto; }
.switch input{ position:absolute; inset:0; opacity:0; }
.switch .track{
  position:absolute; inset:0; border-radius:999px;
  background: var(--panel-2); border:1px solid var(--line);
  box-shadow: inset 0 1px 0 rgba(255,255,255,.06), inset 0 -1px 0 rgba(0,0,0,.25);
  transition: background .2s, border-color .2s;
}
.switch .thumb{
  position:absolute; top:2px; left:2px; width:calc(var(--h) - 4px); height:calc(var(--h) - 4px);
  border-radius:50%; background:#fff; box-shadow: 0 2px 6px rgba(0,0,0,.35);
  transition: transform .2s ease
}
.switch input:checked ~ .track{ background: var(--accent); border-color: var(--accent); }
.switch input:checked ~ .thumb{ transform: translateX(calc(var(--w) - var(--h))); }






/* ======== FORM CONTROLS (tema CustomInfo) ======== */
.settings input[type="text"],
.settings input[type="number"],
.settings input[type="password"],
.settings input[type="email"],
.settings input[type="search"],
.settings select {
  background: var(--panel-2);
  color: var(--text);
  border: 1px solid var(--line);
  border-radius: 10px;
  height: 36px;
  padding: 8px 12px;
  outline: none;
  box-shadow: inset 0 1px 0 rgba(255,255,255,.05), inset 0 -1px 0 rgba(0,0,0,.25);
  transition: border-color .15s ease, box-shadow .15s ease, background .15s ease;
}

.settings input::placeholder { color: var(--muted); opacity: .85; }

.settings input:hover,
.settings select:hover {
  border-color: var(--accent);
}
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              
.settings input:focus,
.settings select:focus,
.textarea:focus {
  border-color: var(--accent);
  box-shadow: 0 0 10px -3px var(--accent), inset 0 0 6px -6px var(--accent);
}

/* Number: rimuovi spinner nativi per un look pulito */
.settings input[type="number"] { -moz-appearance: textfield; }
.settings input[type="number"]::-webkit-outer-spin-button,
.settings input[type="number"]::-webkit-inner-spin-button {
  -webkit-appearance: none; margin: 0;
}

/* =========================================== */
/*    Number external spinner (CustomInfo)     */
/* =========================================== */

/* 1) Rimuovi/annulla il vecchio hover-bridge */
.num-wrap::after { content: none !important; }

/* 2) Lo spinner occupa l’intera altezza dell’input e include il "gap" come padding,
      così l’area di hover/click è continua e grande esattamente come i tasti */
.num-wrap{
  position: relative;
  display: inline-block;
  --spin-w: 28px;     /* larghezza colonna frecce */
  --spin-gap: 8px;    /* distanza visiva dall’input */
}

.num-spin{
  position: absolute;
  top: 0;
  bottom: 0;  /* = stessa altezza dell'input */
  right: calc(-1 * (var(--spin-w) + var(--spin-gap)));
  width: calc(var(--spin-w) + var(--spin-gap)); /* include il "gap" come area attiva */
  padding-left: var(--spin-gap);                 /* gap visivo, ma cliccabile */
  display: grid;
  grid-template-rows: 1fr 1fr;                   /* due pulsanti uguali */
  gap: 4px;
  opacity: 0;
  pointer-events: none;
  transition: opacity .18s ease, filter .18s ease;
  z-index: 5;                                    /* sopra tutto */
}

/* mostra frecce in hover su wrapper o quando l'input è a fuoco */
.num-wrap:hover .num-spin,
.num-wrap:focus-within .num-spin{
  opacity: 1;
  pointer-events: auto;  /* ora riceve click su tutta l’area */
}

/* pulsanti: grandezza piena della "cella" */
.num-spin button{
  all: unset;
  display:flex; align-items:center; justify-content:center;
  width: 100%; height: 100%;
  border-radius: 8px;
  cursor: pointer;
  background: var(--panel-2);
  border: 1px solid var(--line);
  box-shadow: inset 0 1px 0 rgba(255,255,255,.05), inset 0 -1px 0 rgba(0,0,0,.2);
  transition: background .15s ease, border-color .15s ease, transform .1s ease, box-shadow .2s ease;
}

/* hover/active come prima */
.num-spin button:hover{
  background: var(--panel);
  border-color: var(--accent);
  box-shadow: 0 0 8px -2px var(--accent);
}
.num-spin button:active{ transform: scale(0.96); }

/* icone */
.num-spin svg{
  width: 14px; height: 14px;
  fill: currentColor;
  color: var(--muted);
  transition: color .15s ease;
}
.num-spin button:hover svg{ color: var(--accent); }

/* disabilitati */
.num-spin button[disabled]{ opacity: .35; cursor: not-allowed; box-shadow: none; }

  

/* NUOVE MODIFICHE */


/* Piccolo miglioramento per le rows esistenti */
.settings-row label{ align-self: start; }
@media (max-width: 720px){
  .tab-nav{ position: sticky; top: 0; }
}

/* Mobile: va a capo e parte a sinistra */
@media (max-width: 640px){
.settings .status{justify-content:flex-start; flex-wrap:wrap; gap:4px 8px;}
}
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/css/setting.css
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendcsssettingcss"></a>

```css
/* ===========================================
   Settings – Search, Dirty state, Reset per campo,
   Dirty dot su Tab, Banner salvataggio
   =========================================== */
   :root{
    --search: #3cd3ff; /* azzurro acceso leggibile sul tuo tema scuro */
  }

/* ===========================================
 Barra di ricerca
=========================================== */

/* Barra di ricerca impostazioni (iniettata via JS sotto .tab-nav) */
.settings-search {
    position: sticky;
    top: 46px;            /* subito sotto la tab-nav (che è sticky) */
    z-index: 2;
    background: var(--panel);
    padding: 6px 2px 10px;
    border-bottom: 1px dashed var(--line);
  }
  .settings-search input {
    width: 100%;
    height: 34px;
    border-radius: 10px;
    border: 1px solid var(--line);
    background: var(--panel-2);
    color: var(--text);
    padding: 6px 10px;
    outline: none;
    transition: border-color .15s, box-shadow .15s;
  }
  .settings-search input:focus {
    border-color: var(--accent);
    box-shadow: 0 0 8px -2px var(--accent);
  }

/* Puntino tab quando c'è un risultato di ricerca in quella tab */
.tab-nav label.tab-search-hit{
  transform: translateY(-5px);            /* micro–alzata di 1px */
}
/* Se la tab è attiva, niente alzata anche se ha risultati */
.tab-nav label.tab-search-hit.tab-active{
  transform: none;
}

.tab-nav label.tab-search-hit::after{
  content: "•";
  color: var(--search);
  margin-left: 6px;
  filter: drop-shadow(0 0 4px color-mix(in srgb, var(--search) 60%, transparent));
}
  
/* Evidenziazione dei match (più visibile) */
.settings-row mark{
  --mk-intensity: 42%; /* aumenta/diminuisci “quanto accent” c’è nello sfondo */
  --mk-ring:      70%; /* intensità dell’anello esterno */

  background: color-mix(in srgb, var(--accent) var(--mk-intensity), var(--panel));
  color: #fff;                    /* testo chiaro, leggibile sullo sfondo scuro */
  padding: 0 4px;
  border-radius: 6px;
  font-weight: 600;               /* un filo più “bold” aiuta la lettura */
  box-shadow:
    0 0 0 1px color-mix(in srgb, var(--accent) var(--mk-ring), transparent),
    0 0 8px -2px color-mix(in srgb, var(--accent) 45%, transparent);
}

/* puntatore sulla "X" del campo search (Blink/WebKit) */
.settings-search input[type="search"]::-webkit-search-cancel-button{
  cursor: pointer;
}

  
/* ===========================================
    Dirty
   =========================================== */

/* Riga “sporca” (campo modificato) — niente shift, solo label spostata */
.settings-row.dirty{
  --dirty-w: 2px;        /* spessore bordino */
  --dirty-gutter: 6px;   /* spazio extra tra bordino e testo label (REGOLA QUI) */
  background: linear-gradient(180deg, rgba(111,77,239,0.06), rgba(0,0,0,0));
  box-shadow: inset var(--dirty-w) 0 0 0 var(--accent); /* "bordo" che non occupa spazio */
}

/* sposta SOLO la label, il resto della riga non cambia posizione */
.settings-row.dirty > label{
  position: relative;
  transform: translateX(calc(var(--dirty-w) + var(--dirty-gutter)));
  will-change: transform;               /* anti-jitter */
}

/* se usi anche .help-label accanto alla label, includila */
.settings-row.dirty > .help-label{
  position: relative;
  transform: translateX(calc(var(--dirty-w) + var(--dirty-gutter)));
}
  
/* Puntino sulle tab con modifiche non salvate */
.tab-nav label.tab-dirty::after { content: "•"; color: var(--accent); margin-left: 6px; filter: drop-shadow(0 0 4px color-mix(in srgb, var(--accent) 60%, transparent));}
/* Se una tab è sia “dirty” che “search-hit”, diamo priorità visiva alla ricerca */
.tab-nav label.tab-dirty.tab-search-hit::after{
  color: var(--search);
  filter: drop-shadow(0 0 4px color-mix(in srgb, var(--search) 60%, transparent));
}

  

  /* Banner stato salvataggio (iniettato in .settings-actions) */
  .settings-actions .draft-info {
    margin-right: auto;
    font-size: 13px;
    color: var(--muted);
    display: inline-flex; align-items: center; gap: 8px;
  }
  .draft-info .dot {
    width: 8px; height: 8px; border-radius: 50%;
    background: var(--accent);
    box-shadow: 0 0 8px color-mix(in srgb, var(--accent) 40%, transparent);
  }
  
  /* Loader minimal (per “Salvataggio in corso…”) */
  .loader-dot {
    width: 6px; height: 6px; border-radius: 50%; background: var(--accent);
    box-shadow: 0 0 8px color-mix(in srgb, var(--accent) 50%, transparent);
    animation: loader-bounce 0.9s infinite ease-in-out;
  }
  .loader-dot:nth-child(2){ animation-delay: .15s; }
  .loader-dot:nth-child(3){ animation-delay: .30s; }
  @keyframes loader-bounce {
    0%, 80%, 100% { transform: translateY(0); opacity: .7; }
    40%          { transform: translateY(-3px); opacity: 1; }
  }
  
  /* Tooltip micro (riuso per validazioni inline) */
  .inline-hint {
    font-size: 12px; color: var(--muted);
  }
  .inline-hint.error { color: var(--danger); }

  /* Validazione inline sobria */
.settings input:invalid,
.settings select:invalid,
.settings textarea:invalid {
  border-color: var(--danger) !important;
  box-shadow: 0 0 8px -2px var(--danger) !important;
}
.settings-row .inline-hint.error { display: block; margin-top: 6px; }

.settings-row:has(:invalid) > label::after{
    content:" ⚠";
    color: var(--danger);
    margin-left: 4px;
  }
  
/* Risultati ricerca impostazioni */
.settings-search .search-meta{
    margin-top: 6px; font-size: 12px; color: var(--muted);
  }
  .settings-search .search-meta .bad{
    color: var(--danger);
  }
  
/* Sezioni collassabili */
.set-sec{
    margin: 12px 0 6px; padding: 8px 12px; border-top:1px solid var(--line);
    position: relative; user-select: none; cursor: pointer;
    background: linear-gradient(0deg, rgba(0,0,0,0), rgba(0,0,0,.08));
  }
  .set-sec::after{
    content:""; position:absolute; left:0; bottom:0; height:1px; width:100%;
    background: linear-gradient(90deg,
      rgba(111,77,239,0) 0%,
      rgba(111,77,239,0.6) 35%,
      rgba(111,77,239,0.8) 50%,
      rgba(111,77,239,0.6) 65%,
      rgba(111,77,239,0) 100%);
  }
  .set-sec .caret{
    margin-right:8px; display:inline-block; transform: rotate(0deg); transition: transform .2s ease;
  }
  .set-sec.collapsed .caret{ transform: rotate(-90deg); }
  .set-block{ margin: 6px 0 10px; }
  .set-block.collapsed{ display: none; }

 /* Danger zone box */
.settings-danger{
    background: var(--panel-2);
    border: 1px solid color-mix(in srgb, var(--danger) 35%, var(--line) 65%);
    border-left: 3px solid var(--danger);
    border-radius: 12px;
    padding: 12px;
    margin: 12px 0;
  }
  .settings-danger h4{
    margin: 0 0 8px; color: var(--danger);
  }
  .settings-danger .actions{
    display:flex; gap:8px; justify-content:flex-end; margin-top:8px;
  }
  
/* Context menu per chip */
.chip-menu{
    position: fixed; z-index: 10000;
    background: var(--panel);
    border:1px solid var(--line);
    border-radius:10px;
    box-shadow: var(--shadow);
    padding:6px; display:none; min-width:180px;
  }
  .chip-menu button{
    all:unset; display:block; width:100%;
    padding:8px 10px; border-radius:8px; cursor:pointer;
  }
  .chip-menu button:hover{
    background: var(--panel-2); border:1px solid var(--line);
  }

  #lib-list.drop-hl{
    outline: 2px dashed var(--accent);
    border-radius: 12px;
    padding: 8px;
  }
  .excl-wrap{ display:flex; flex-direction:column; gap:8px; }
  .excl-chips{ display:flex; gap:6px; flex-wrap:wrap; }
  .excl-chips .chip{ cursor: default; }
  .excl-meta{ font-size:12px; color: var(--muted); }

/* ============================================
   RESET LAYOUT PER TIPO DI CONTROLLO GENERICO
   ============================================ */

     /* Pulsantino reset per singolo campo */
  .field-reset {
    all: unset;
    display: inline-flex; 
    align-items:center; 
    justify-content:center;
    width: 26px; 
    height: 26px; 
    margin-left: 6px;
    margin-right: 3px;
    border-radius: 8px; cursor: pointer;
    background: var(--panel-2); border:1px solid var(--line);
    color: var(--muted);

  }
  .field-reset:hover {
    background: var(--panel);
    border-color: var(--accent);
    color: var(--text);
    box-shadow: 0 0 8px -2px var(--accent);

  }
  .field-reset:active { transform: scale(0.96); }

/* ===================================================
 1) RESET LAYOUT PER TIPO DI CONTROLLO — TEXT/SELECT
==================================================== */

/* wrapper generico che tiene input e reset in linea */
.field-wrap{
    position: relative;
    display: flex;
    align-items: center;
    width: 100%;
    min-width: 0;      /* evita overflow quando compare il reset */
    flex-wrap: nowrap; /* impedisce il wrap del bottone reset */
  }
  
  .field-wrap.field--text{
    --reset-gap: 8px; /* ←← REGOLA QUI lo spazio tra input/select e tasto reset */
    gap: var(--reset-gap);
  }
  
  /* l’input/select occupa tutto lo spazio e si restringe se serve */
  .field-wrap.field--text > input[type="text"],
  .field-wrap.field--text > input[type="search"],
  .field-wrap.field--text > input[type="email"],
  .field-wrap.field--text > input[type="password"],
  .field-wrap.field--text > select{
    flex: 1 1 0%;  /* usa tutto lo spazio rimasto */
    min-width: 0;  /* consente lo shrink senza overflow */
    width: auto;   /* lascia a flex la gestione larghezza */
  }
  
  /* il reset sta IN FLOW (non assoluto) e ha larghezza fissa */
  .field-wrap.field--text > .field-reset{
    position: static;
    transform: none;
    flex: 0 0 26px; /* dimensione pulsante coerente col tuo stile */
    margin-left: 0; /* lo spacing è dato da 'gap' */
  }

/* ============================================
2) NUMERICI → [input][frecce][reset] in flow
   ============================================ */
   .field-wrap{
    position: relative;
    display: flex;
    align-items: center;
    width: 100%;
    min-width: 0;
    flex-wrap: nowrap;
  }
  .settings-row > .field-wrap{ flex: 1 1 0%; min-width: 0; }
  
  /* wrapper specifico numerici */
  .field-wrap.field--number{
    --spin-w: 36px;        /* ← LARGHEZZA VISIVA delle frecce (regola se servisse) */
    --gap-ir: 10px;        /* ← spazio frecce ↔ reset */
  }
  
  /* input+frecce: NON tocchiamo lo stile interno delle frecce */
  .field-wrap.field--number > .num-wrap{
    display: inline-flex;
    align-items: center;
    min-width: 0;
    /* corsia dinamica per le frecce: si apre solo quando l'utente interagisce */
    margin-right: 0;
    transition: margin-right .15s ease;
  }
  
  /* quando l'utente è sopra o sta usando il campo numero → apri corsia frecce */
  .field-wrap.field--number > .num-wrap:hover,
  .field-wrap.field--number > .num-wrap:focus-within{
    margin-right: calc(var(--spin-w) + var(--gap-ir));
  }
  
  /* reset in flow a destra, sempre cliccabile */
  .field-wrap.field--number > .field-reset{
    position: relative;
    z-index: 1;       /* nel caso qualche skin dello spinner “sbordi” visivamente */
    flex: 0 0 26px;
    transform: none;
  }

  /* Solo quando il NUMBER è in focus E il reset è in active → tieni aperta la corsia */
  .field-wrap.field--number:has(.num-wrap input:focus):has(> .field-reset:active) > .num-wrap{
    margin-right: calc(var(--spin-w) + var(--gap-ir));
  }

  /* (fallback a classe, vedi JS sotto) */
  .field-wrap.field--number.keep-lane > .num-wrap{
    margin-right: calc(var(--spin-w) + var(--gap-ir));
  }

/* ============================================
3) TEXTAREA → reset FUORI, in basso a destra
   ============================================ */

/* TEXTAREA — reset quadrato perfetto */
.field-wrap.field--textarea > .field-reset{
    --ta-reset-size: 36px;         /* ← misura ESTERNA desiderata */
    box-sizing: border-box;        /* include il bordo nella misura */
    width: var(--ta-reset-size);
    height: var(--ta-reset-size);
    aspect-ratio: 1 / 1;           /* cintura di sicurezza */
    padding: 0;                    /* assicurati che non allarghi */
    line-height: 1;                /* niente gonfiaggi verticali */
    transform: none !important;    /* niente scale/translate su textarea */
    font-size: 18px;                 /* ingrandisce il glifo “↺” */
  }
  
  /* disattiva gli effetti di hover/active SOLO qui */
  .field-wrap.field--textarea > .field-reset:hover,
  .field-wrap.field--textarea > .field-reset:active{
    transform: none !important;
  }
  
  














  /* MODIFICHE DA APPORTARE ALLE NUOVE FUNZIONI

  1) capire perchè quando si inserisce max="10" negli input di testo gli help si mettono sulla stessa linea
  2) implementare la ricerca perchè non dà nessun risultato e far comparire la scritta "Nessun Risultato" solo alla pressione dell'invio
     implementare il "point" quando si passa sulla "X" per azzerare la ricerca
  3)


/* =========================
   Alias Dialog
   ========================= */
   .alias-dialog {
    position: fixed; inset: 0; z-index: 10000;
    background: color-mix(in srgb, black 45%, transparent); /* overlay più scuro */
    backdrop-filter: blur(2px);
    display: none;
  }
  
.alias-dialog.open { display: block; }

.alias-panel{
  position: absolute; inset: 8% 10% auto 10%;
  background: var(--panel);
  border:1px solid var(--line);
  border-radius: 14px;
  box-shadow: var(--shadow);
  padding: 16px;                  /* +2px */
  display: flex; flex-direction: column; gap: 12px;
  max-height: 84%;
  overflow: hidden;               /* evita che i bordi mostrino overflow */
}

/* animazione ingresso dolce */
.alias-dialog.open .alias-panel{
  animation: aliasIn .14s ease-out;
}
@keyframes aliasIn{
  from{ opacity: 0; transform: translateY(6px) scale(.985); }
  to  { opacity: 1; transform: none; }
}

.alias-header{
  display: grid;
  grid-template-columns: 1fr auto;
  gap: 10px; align-items: center;
}
.alias-header h3{ margin: 0; }
.alias-tools{ display: inline-flex; gap: 8px; }

.alias-search{
  display: grid; gap: 6px;
}
.alias-search input[type="search"]{
  width: 100%; height: 34px; border-radius: 10px;
  border: 1px solid var(--line); background: var(--panel-2); color: var(--text);
  padding: 6px 10px; outline: none; transition: border-color .15s, box-shadow .15s;
}
.alias-search input[type="search"]:focus{
  border-color: var(--accent);
  box-shadow: 0 0 8px -2px var(--accent);
}
.alias-search .meta{ font-size: 12px; color: var(--muted); min-height: 18px; }

.alias-results{
  overflow: auto; border-top:1px dashed var(--line); padding-top: 10px;
  display: grid; gap: 10px;
}

/* Card */
.alias-card{
  border:1px solid var(--line);
  border-radius:12px;
  background: var(--panel-2);
  padding: 12px;                     /* +2px */
  display: grid; gap: 8px;
  transition: border-color .15s, box-shadow .15s, background .15s;
}
.alias-card:hover{
  border-color: color-mix(in srgb, var(--accent) 45%, var(--line));
  box-shadow: 0 2px 12px rgba(0,0,0,.18);
}
.alias-card__head{
  display:flex; align-items:center; justify-content:space-between; gap: 8px;
}
.alias-title{ font-weight: 600; font-size: 14.5px; }
.alias-ctas{ display:inline-flex; gap:6px; }

.alias-ctas .btn,
.alias-footer .btn{
  all: unset;
  display:inline-flex; align-items:center; justify-content:center;
  height: 34px; min-width: 34px;           /* target comodo */
  padding: 0 10px;
  border:1px solid var(--line);
  border-radius:10px;
  cursor:pointer;
  transition: border-color .15s, box-shadow .15s, background .15s, transform .04s;
}
.alias-ctas .btn:hover,
.alias-footer .btn:hover{
  background: var(--panel);
  border-color: var(--accent);
  box-shadow: 0 0 8px -2px var(--accent);
}
.alias-ctas .btn:active,
.alias-footer .btn:active{
  transform: translateY(1px);
}
.alias-footer .btn.save{
  background: color-mix(in srgb, var(--accent) 12%, var(--panel));
}


.alias-chips{ display:flex; gap:6px; flex-wrap:wrap; }
.alias-chip{
  display:inline-flex; align-items:center; gap:6px;
  padding:4px 10px;
  border-radius:999px;
  background: var(--panel);
  border:1px solid var(--line);
  color: var(--text);
  font-size:13px;
  transition: border-color .15s, background .15s;
}
.alias-chip:hover{
  background: color-mix(in srgb, var(--panel) 70%, var(--panel-2) 30%);
  border-color: color-mix(in srgb, var(--line) 60%, var(--accent) 40%);
}
.alias-chip .del{ all:unset; cursor:pointer; opacity:.7; }
.alias-chip .del:hover{ opacity:1; color: var(--danger); }

.alias-edit{
  display:grid; gap:8px; background: var(--panel); border:1px dashed var(--line);
  border-radius:10px; padding:10px;
}
.alias-grid{
  display:grid; gap:8px;
  grid-template-columns: minmax(140px, 220px) 1fr;  /* più fluido */
  align-items:start;
}
@media (max-width: 880px){
  .alias-grid{ grid-template-columns: 1fr; }
  .alias-grid label{ padding-top: 0; }
}
.alias-grid label{ color: var(--muted); padding-top: 6px; }
.alias-list{ display:grid; gap:6px; }
.alias-row{ display:flex; gap:6px; }
.alias-row input[type="text"]{
  flex:1 1 auto; height: 32px; border-radius: 10px; border:1px solid var(--line);
  background: var(--panel-2); color: var(--text); padding: 6px 10px; outline: none;
}
.alias-row .add, .alias-row .rm{
  all:unset;
  width: 34px; height: 34px;                  /* quadrati e coerenti */
  display:inline-flex; align-items:center; justify-content:center;
  border:1px solid var(--line); border-radius:8px; cursor:pointer;
  transition: border-color .15s, background .15s, transform .04s;
}
.alias-row .add:hover, .alias-row .rm:hover{
  background: var(--panel); border-color: var(--accent);
}
.alias-row .add:active, .alias-row .rm:active{ transform: translateY(1px); }

.alias-row input[type="text"]{
  flex:1 1 auto; height: 34px;                 /* allineato ai bottoni */
  border-radius: 10px; border:1px solid var(--line);
  background: var(--panel-2); color: var(--text); padding: 6px 10px; outline: none;
}

.alias-warn{ font-size:12px; color: var(--danger); }
.alias-log{ font-size:12px; color: var(--muted); }

.alias-footer{ display:flex; justify-content:flex-end; gap:8px; }
.alias-footer .btn{
  all:unset; padding: 8px 10px; border-radius:10px; cursor:pointer; border:1px solid var(--line);
}
.alias-footer .btn.save{ background: color-mix(in srgb, var(--accent) 12%, var(--panel)); }
.alias-footer .btn:hover{ border-color: var(--accent); }

.alias-row .add, .alias-row .rm{
  all:unset;
  width: 34px; height: 34px;                  /* quadrati e coerenti */
  display:inline-flex; align-items:center; justify-content:center;
  border:1px solid var(--line); border-radius:8px; cursor:pointer;
  transition: border-color .15s, background .15s, transform .04s;
}
.alias-row .add:hover, .alias-row .rm:hover{
  background: var(--panel); border-color: var(--accent);
}
.alias-row .add:active, .alias-row .rm:active{ transform: translateY(1px); }

.alias-row input[type="text"]{
  flex:1 1 auto; height: 34px;                 /* allineato ai bottoni */
  border-radius: 10px; border:1px solid var(--line);
  background: var(--panel-2); color: var(--text); padding: 6px 10px; outline: none;
}
/* Evidenziazione match nei risultati alias */
.alias-results mark{
  --mk-intensity: 42%;
  --mk-ring: 70%;
  background: color-mix(in srgb, var(--accent) var(--mk-intensity), var(--panel));
  color: #fff;
  padding: 0 4px;
  border-radius: 6px;
  font-weight: 600;
  box-shadow:
    0 0 0 1px color-mix(in srgb, var(--accent) var(--mk-ring), transparent),
    0 0 8px -2px color-mix(in srgb, var(--accent) 45%, transparent);
}

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/css/variables.css
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendcssvariablescss"></a>

```css
:root {
  /* === DARK THEME (default) === */
  --bg:        #0d1117;   /* sfondo principale */
  --panel:     #111826;   /* pannelli, card */
  --panel-2:   #0b101a;   /* sotto-livelli */
  --line:      #1b2334;   /* bordi */
  --text:      #e6ebf3;   /* testo primario */
  --muted:     #8a94a8;   /* testo secondario */
  --status-text-color: #e6ebf3;

  --brand:     #65a2ff;   /* blu secondario (icone, highlight) */
  --accent:    #7c5cff;   /* viola: primary button */
  --ok:        #2ecc71;   /* verde OK */
  --warn:      #f1c40f;   /* giallo attenzione */
  --danger:    #ff5c5c;   /* rosso error/danger */

  --radius:    16px;
  --gap:       16px;
  --shadow:    0 6px 24px rgba(0,0,0,.35);
}

[data-theme="light"] {
  /* === LIGHT THEME (coerente col dark) === */
  --bg:        #f7f8fb;   /* sfondo principale */
  --panel:     #ffffff;   /* pannelli, card */
  --panel-2:   #f3f5fa;   /* sotto-livelli per contrasto */
  --line:      #dbe1ee;   /* bordi */
  --text:      #0c1224;   /* testo primario */
  --muted:     #5d6680;   /* testo secondario */

  --brand:     #2a6bff;   /* blu secondario */
  --accent:    #5a2af0;   /* viola coerente col dark, leggermente più saturo */
  --ok:        #27ae60;   /* verde OK */
  --warn:      #d4ac0d;   /* giallo attenzione */
  --danger:    #e74c3c;   /* rosso error/danger */
  
}
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/index.html
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendindexhtml"></a>

```html
<!doctype html>
<html lang="it">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1"/>
<title>CustomInfo Game Library</title>
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="stylesheet" href="css/variables.css">
<link rel="stylesheet" href="css/app.css">
<link rel="stylesheet" href="css/setting.css">
</head>
<body>
  <div id="app" class="app">
    <aside class="sidebar">
      <div class="brand">
        <img src="assets/icons/controller.svg" class="brand-icon" alt="logo"/>
        <span>CustomInfo</span>
      </div>
      <nav class="nav">
        <button class="nav-btn active" data-view="library">
          <img src="assets/icons/library.svg" alt=""/> Libreria
        </button>
        <button class="nav-btn" data-view="updates">
          <img src="assets/icons/update.svg" alt=""/> Aggiornamenti
        </button>
        <button class="nav-btn" data-view="settings">
          <img src="assets/icons/settings.svg" alt=""/> Impostazioni
        </button>
      </nav>
      <div class="sidebar-footer">
        <button id="btn-theme-toggle" class="ghost small btn-purple">Tema</button>
        <div class="version"><span id="ui-version"></span></div>
      </div>
    </aside>

    <main class="main">
      <header class="topbar">
        <div class="search-wrap">
          <input id="search" type="search" placeholder="Cerca giochi per titolo, tag…">
          <button id="btn-filters" class="ghost">
            <img src="assets/icons/filter.svg" alt=""/> Filtri
          </button>
        </div>
        <div class="actions">
          <button id="btn-add" class="primary btn-purple">
            <img src="assets/icons/plus.svg" alt=""/> Aggiungi Giochi
          </button>
        </div>
      </header>
<!-- Libreria -->
<div class="content-scroll">
      <section id="view-library" class="view active">
        <div id="empty-state" class="empty">
          <img src="assets/images/empty_library.svg" alt="Nessun gioco" />
          <h2>Nessun gioco ancora</h2>
          <p>Aggiungi una cartella per iniziare a popolare la tua libreria.</p>
          <div class="empty-actions">
            <button id="btn-empty-add" class="primary">Seleziona cartella</button>
          </div>
          <div class="tips">
            <div class="tip"><b>Consiglio:</b> puoi aggiungere più cartelle libreria dalle impostazioni.</div>
            <div class="tip"><b>Supporto installer:</b> 7z silente, ISO mount nascosto, setup /S.</div>
          </div>
        </div>
        <div id="grid" class="grid" hidden></div>
      </section>


      <section id="view-updates" class="view">
        <div class="panel">
          <h2>Aggiornamenti</h2>
          <p>Qui vedrai patch disponibili, ordini di update e prerequisiti per ogni gioco.</p>
          <div class="placeholder-list">
            <div class="skeleton line"></div>
            <div class="skeleton line"></div>
            <div class="skeleton line"></div>
          </div>
        </div>
      </section>

      <section id="view-settings" class="view">
        <div class="panel settings">
          <h2>Impostazioni</h2>
      
          <!-- ========== SCHEDE (CSS-only con radio) ========== -->
          <div class="settings-tabs">
            <!-- Radios nascosti per stato scheda -->
            <input type="radio" name="settings-tab" id="tab-library" checked>
            <input type="radio" name="settings-tab" id="tab-scan">
            <input type="radio" name="settings-tab" id="tab-logs">
            <input type="radio" name="settings-tab" id="tab-ui">
            <input type="radio" name="settings-tab" id="tab-esc">
            <input type="radio" name="settings-tab" id="tab-adv">
      
            <!-- Barra schede -->
            <nav class="tab-nav">
              <label for="tab-library">Libreria</label>
              <label for="tab-scan">Scansione</label>
              <label for="tab-logs">Log</label>
              <label for="tab-ui">Interfaccia</label>
              <label for="tab-esc">Esclusioni</label>
              <label for="tab-adv">Avanzate</label>
            </nav>
      
            <!-- Contenuto schede -->
            <div class="tab-panels">
      
              <!-- LIBRERIA -->
              <div class="tab-panel" data-tab="tab-library">
                <div class="settings-row">
                  <label>Cartelle libreria</label>
                  <div class="stack">
                    <div id="lib-list" class="chips"></div>
                    <button id="add-lib" class="ghost small">
                      <img src="assets/icons/plus.svg" alt=""/> Aggiungi cartella
                    </button>
                    <div class="help">Aggiungi più percorsi: la libreria verrà popolata unendo i risultati.</div>
                  </div>
                </div>
      
                <div class="settings-row">
                  <label>Cartella di default</label>
                  <div class="stack row-inline">
                    <input id="default-lib" type="text" placeholder="es. D:\Giochi">
                    <button class="ghost small">Sfoglia…</button>
                    <div class="help">Usata come percorso predefinito per la scansione rapida.</div>
                  </div>
                </div>
              </div>
      
              <!-- SCANSIONE -->
              <div class="tab-panel" data-tab="tab-scan">
                <div class="settings-row">
                  <label>Scansione all’avvio</label>
                  <div class="row-inline">
                    <label class="switch">
                      <input id="scan-on-start" type="checkbox">
                      <span class="track"></span><span class="thumb"></span>
                    </label>
                    <div class="help">Se attivo, analizza automaticamente la cartella di default ad ogni avvio.</div>
                  </div>
                </div>
      
                <div class="settings-row">
                  <label>Profondità sottocartelle</label>
                  <div class="row-inline">
                    <input data-ui="ext-spin" id="scan-depth" type="number" required min="0" step="1" value="3" style="max-width:120px">
                    <div class="help">0 = solo cartella corrente. Aumenta per progetti molto annidati.</div>
                  </div>
                </div>
      
                <div class="settings-row">
                  <label>Rilevamento duplicati</label>
                  <div class="row-inline">
                    <label class="switch">
                      <input id="dedupe-titles" type="checkbox" checked>
                      <span class="track"></span><span class="thumb"></span>
                    </label>
                    <div class="help">Evita di re-inserire titoli già indicizzati (case-insensitive).</div>
                  </div>
                </div>
              </div>
      
              <!-- LOG -->
              <div class="tab-panel" data-tab="tab-logs">
                <div class="settings-row">
                  <label>Conservazione log</label>
                  <div class="row-inline">
                    <input data-ui="ext-spin" id="logs-keep" type="number" required min="1" step="1" value="10" style="max-width:120px"> 
                    <div class="help">Numero di file da conservare; gli altri verranno rimossi in automatico.</div>
                  </div>
                </div>
      
                <div class="settings-row">
                  <label>Salvataggio automatico</label>
                  <div class="row-inline">
                    <label class="switch">
                      <input id="logs-autosave" type="checkbox">
                      <span class="track"></span><span class="thumb"></span>
                    </label>
                    <div class="help">Al termine di una scansione, salva un dump del registro attività.</div>
                  </div>
                </div>
      
                <div class="settings-row">
                  <label>Azioni</label>
                  <div class="row-inline">
                    <button class="ghost small" id="logs-clean-now">Pulisci log ora</button>
                    <div class="help">Elimina subito i file log eccedenti il limite.</div>
                  </div>
                </div>
              </div>
      
              <!-- INTERFACCIA -->
              <div class="tab-panel" data-tab="tab-ui">
                <div class="settings-row">
                  <label for="theme">Tema</label>
                  <select id="theme">
                    <option value="dark">Dark</option>
                    <option value="light">Light</option>
                  </select>
                </div>
      
                <div class="settings-row">
                  <label>Mostra progress bar</label>
                  <div class="row-inline">
                    <label class="switch">
                      <input id="ui-progress" type="checkbox" checked>
                      <span class="track"></span><span class="thumb"></span>
                    </label>
                    <div class="help">Barra indeterminata durante le scansioni più lunghe.</div>
                  </div>
                </div>
              </div>
      
              <!-- ESCLUSIONI -->
              <div class="tab-panel" data-tab="tab-esc">
                <div class="settings-row">
                  <label>Esclusioni cartelle</label>
                  <div class="stack row-inline">
                    <textarea id="dir-exclude" rows="3" class="textarea"
                      placeholder="update; patch; dlc; language pack; crack"></textarea>
                    <div class="help">
                      Parole chiave presenti nel nome di una <b>cartella</b> che devono essere ignorate
                      durante la scansione. Separate ciascun termine con un <b>punto e virgola</b>.
                    </div>
                  </div>
                </div>
              
                <div class="settings-row">
                  <label>Esclusioni file</label>
                  <div class="stack row-inline">
                    <textarea id="file-exclude" rows="3" class="textarea"
                      placeholder="readme; changelog; license; eula; unins"></textarea>
                    <div class="help">
                      Nomi o parti di nome dei <b>file</b> che vuoi escludere dalla libreria.
                      Ad esempio, documenti o script inutili presenti nei pacchetti.
                    </div>
                  </div>
                </div>
              
                <div class="settings-row">
                  <label>Estensioni da escludere</label>
                  <div class="stack row-inline">
                    <textarea id="ext-exclude" rows="3" class="textarea"
                      placeholder=".txt; .nfo; .pdf; .ini; .url"></textarea>
                    <div class="help">
                      Elenco di <b>estensioni di file</b> da ignorare (con o senza punto iniziale),
                      separate da punto e virgola.  
                      Esempio: <code>.txt; .nfo; .pdf</code>
                    </div>
                  </div>
                </div>
              </div>

              <!-- AVANZATE -->
              <div class="tab-panel" data-tab="tab-adv">
                <div class="settings-row">
                  <label>7-Zip path</label>
                  <input id="sevenzip" type="text" placeholder="es. C:\Program Files\7-Zip\7z.exe">
                </div>
      
                <div class="settings-row">
                  <label>File alias titoli</label>
                  <div class="stack row-inline">
                    <input id="alias-file" type="text" placeholder="(opzionale) Percorso game_aliases.json">
                    <button class="ghost small">Sfoglia…</button>
                    <div class="help">Se non impostato, usa il file accanto al modulo (default).</div>
                  </div>
                  <div class="help-label">
                    Un <em>alias</em> è un nome alternativo applicato ai titoli durante la scansione
                    (senza rinominare i file). Utile per uniformare i nomi, rimuovere tag/scene,
                    abbreviazioni o tradurre i titoli.
                  </div>
                </div>
              </div>
<!--               <button id="btn-manage-aliases" class="btn btn-purple">Gestisci alias…</button> -->
      
            </div> <!-- /tab-panels -->
          </div>   <!-- /settings-tabs -->
      
          <!-- Azioni globali (mantengo gli ID per il JS già presente) -->
          <div class="settings-actions">
            <button id="restore" class="danger">Ripristina default</button>
            <button id="save" class="primary btn-purple">Salva</button>
          </div>
      
          <!-- Info build/canale/updated (riuso gli ID esistenti) -->
          <div class="status">
            <span id="status-build" class="status-item" title="Build">—</span>
            <span class="sep">—</span>
            <span id="status-channel" class="status-item" title="Canale">—</span>
            <span class="sep">•</span>
            <span id="status-updated" class="status-item" title="Ultimo aggiornamento">—</span>
          </div>
        </div>
      </section>
      
      
    </div>
<footer class="bottombar">
<div class="status"><span id="status-text">Pronto</span></div>
<div class="progress-wrap" hidden>
<div id="progress" class="progress"><div class="bar" style="width:0%"></div></div>
</div>
<button id="btn-log" class="ghost small">Mostra Log</button>
</footer>
</main>
</div>

<!-- Drawer Log -->
<div id="log-drawer" class="drawer" aria-hidden="true">
<div class="drawer-panel">
<header class="drawer-header">
<div class="drawer-title"><h3>Registro attività</h3></div>
<button id="log-close" class="icon-btn" aria-label="Chiudi"><img src="assets/icons/close.svg" alt=""/></button>
</header>
<pre id="logbox" class="logbox"></pre>
<div class="drawer-footer">
  <span id="drawer-status" class="drawer-status muted"></span>
  <button id="btn-save-log" class="ghost small">Salva Log</button>
</div>

</div>
<div class="drawer-scrim"></div>
</div>
<script src="js/app.js"></script>
<script src="js/ui-utils.js" defer></script>
<script src="js/aliases-ui.js"></script>
</body>
</html>
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/js/aliases-ui.js
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendjsaliases-uijs"></a>

```javascript
/* aliases-ui.js
 * UI dialog per gestione alias titoli (HTML/CSS/JS lato client).
 * - Ricerca live (>=3 char, highlight, "Nessun risultato" su Invio)
 * - Crea / Modifica / Elimina titoli e alias
 * - Import/Export
 *   -> Con backend REST      (se /api/aliases esiste)
 *   -> Oppure fallback locale (LocalStorage) se il backend non c'è
 *
 * Espone window.UIAliases.open()
 * Autocollega #btn-manage-aliases se presente.
 */
(function () {
  'use strict';

  // =========================
  // Config
  // =========================
  const CFG = {
    API: '/api/aliases',
    FORCE_BACKEND: false,          // metti true se vuoi forzare l'uso del backend
    DEV_LS_KEY: 'ui.aliases.dev',  // storage locale quando il backend non c'è
  };

  // =========================
  // Helpers base
  // =========================
  const $  = (sel, ctx = document) => ctx.querySelector(sel);
  const $$ = (sel, ctx = document) => Array.from(ctx.querySelectorAll(sel));
  const debounce = (fn, ms = 200) => { let t; return (...a) => { clearTimeout(t); t = setTimeout(() => fn(...a), ms); }; };

  async function fetchJSON(url, opts = {}) {
    const res = await fetch(url, {
      headers: { ...(opts.body instanceof FormData ? {} : { 'Content-Type': 'application/json' }), ...(opts.headers || {}) },
      credentials: 'include',
      ...opts
    });
    if (!res.ok) {
      let text = '';
      try { text = await res.text(); } catch {}
      throw new Error(`HTTP ${res.status} – ${text || res.statusText}`);
    }
    const ct = res.headers.get('content-type') || '';
    if (!ct.includes('application/json')) return {};
    return await res.json();
  }
  const getData = (payload) => (payload && payload.data) ? payload.data : payload;

  function say(msg) {
    let live = $('#alias-aria');
    if (!live) {
      live = document.createElement('div');
      live.id = 'alias-aria';
      live.setAttribute('aria-live', 'polite');
      live.style.position = 'absolute';
      live.style.left = '-9999px';
      document.body.appendChild(live);
    }
    live.textContent = msg || '';
  }

  // =========================
  // Normalizzazione / Index
  // =========================
  const norm = (s) => (s || '')
    .normalize('NFKD').replace(/[\u0300-\u036f]/g, '')
    .toLowerCase()
    .replace(/['’`"]/g, '')
    .replace(/\s+/g, ' ')
    .trim();

  const sortAsc = (a, b) => a.localeCompare(b, undefined, { sensitivity: 'base' });
  const keyOf = (titleReal) => norm(titleReal);

  const toIndex = (raw) => {
    const map = new Map();
    Object.entries(raw || {}).forEach(([key, obj]) => {
      const real = obj?.title_real || key;
      const aliases = Array.isArray(obj?.aliases) ? obj.aliases : [];
      map.set(keyOf(real), { title_real: real, aliases: new Set(aliases.filter(Boolean)) });
    });
    return map;
  };

  const fromIndex = (map) => {
    const entries = Array.from(map.values()).sort((a, b) => sortAsc(a.title_real, b.title_real));
    const out = {};
    for (const { title_real, aliases } of entries) {
      out[keyOf(title_real)] = { title_real, aliases: Array.from(aliases).sort(sortAsc) };
    }
    return out;
  };

  const matches = (item, q) => {
    const qq = norm(q);
    if (norm(item.title_real).includes(qq)) return true;
    for (const al of item.aliases) if (norm(al).includes(qq)) return true;
    return false;
  };

  // =========================
  // IO Backend
  // =========================
  const BackendIO = {
    async init() {
      if (CFG.FORCE_BACKEND) return true;
      try {
        await fetchJSON(CFG.API); // prova GET /api/aliases
        return true;
      } catch (e) {
        return false; // 404/Network → non disponibile
      }
    },
    async load() {
      const resp = await fetchJSON(CFG.API);
      return getData(resp) || {};
    },
    async upsert({ title_real, aliases, original_key = null }) {
      return await fetchJSON(`${CFG.API}/upsert`, {
        method: 'POST',
        body: JSON.stringify({ title_real, aliases, original_key })
      });
    },
    async remove(key) {
      return await fetchJSON(`${CFG.API}/${encodeURIComponent(key)}`, { method: 'DELETE' });
    },
    async replace(allData) {
      return await fetchJSON(CFG.API, { method: 'PUT', body: JSON.stringify(allData) });
    },
    async export() {
      window.open(`${CFG.API}/export`, '_blank');
    },
    async importFile(file) {
      const fd = new FormData();
      fd.append('file', file);
      const res = await fetch(`${CFG.API}/import`, { method: 'POST', body: fd, credentials: 'include' });
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      return await res.json();
    }
  };

  // =========================
  // IO Local (fallback)
  // =========================
  function readLS() {
    try {
      const txt = localStorage.getItem(CFG.DEV_LS_KEY);
      if (!txt) return {};
      const obj = JSON.parse(txt);
      if (obj && typeof obj === 'object') return obj;
    } catch {}
    return {};
  }
  function writeLS(obj) {
    localStorage.setItem(CFG.DEV_LS_KEY, JSON.stringify(obj, null, 2));
  }

  function mergeUpsertLocal(storeObj, { title_real, aliases, original_key }) {
    // storeObj: { key: {title_real, aliases:[..]}, ... }
    const out = { ...storeObj };
    const nextKey = keyOf(title_real);
    const inAliases = Array.from(new Set((aliases || []).map(a => a.trim()).filter(Boolean)));

    const ensureEntry = (key, title) => {
      if (!out[key]) out[key] = { title_real: title, aliases: [] };
    };

    // se c'è rinomina (original_key)
    let baseAliases = [];
    if (original_key && out[original_key]) {
      baseAliases = out[original_key].aliases || [];
      // se target key già esiste, mergeremo; altrimenti rinominiamo
      if (original_key !== nextKey) {
        if (!out[nextKey]) {
          out[nextKey] = { title_real, aliases: baseAliases.slice() };
        } else {
          // merge su già esistente
          out[nextKey].title_real = title_real; // tieni il titolo nuovo
          out[nextKey].aliases = Array.from(new Set([...(out[nextKey].aliases || []), ...baseAliases]));
        }
        delete out[original_key];
      } else {
        // stessa key: tieni titolo aggiornato e riparti con alias esistenti
        ensureEntry(nextKey, title_real);
        out[nextKey].title_real = title_real;
        out[nextKey].aliases = Array.from(new Set([...(out[nextKey].aliases || []), ...baseAliases]));
      }
    } else {
      // nessuna rinomina: assicurati che l'entry esista
      ensureEntry(nextKey, title_real);
      out[nextKey].title_real = title_real;
    }

    // merge alias nuovi
    const prev = new Set((out[nextKey].aliases || []).map(a => norm(a)));
    let added = 0, skipped = 0;
    for (const a of inAliases) {
      const na = norm(a);
      if (!na || prev.has(na) || na === nextKey) { skipped++; continue; }
      out[nextKey].aliases.push(a);
      prev.add(na);
      added++;
    }
    // ordina alias
    out[nextKey].aliases = Array.from(new Set(out[nextKey].aliases)).sort(sortAsc);

    // riordina chiavi per output finale
    const idx = toIndex(out);
    const saved = fromIndex(idx);
    writeLS(saved);

    return { data: saved, report: { added, skipped, conflicts: [] } };
  }

  function deleteLocal(storeObj, key) {
    const out = { ...storeObj };
    delete out[key];
    const saved = fromIndex(toIndex(out));
    writeLS(saved);
    return { data: saved };
  }

  function replaceLocal(allData) {
    const saved = fromIndex(toIndex(allData || {})); // normalizza e ordina
    writeLS(saved);
    return { data: saved };
  }

  function importLocal(storeObj, incomingObj) {
    const base = toIndex(storeObj);
    const inc  = toIndex(incomingObj);
    for (const [k, it] of inc.entries()) {
      const cur = base.get(k);
      if (!cur) { base.set(k, it); continue; }
      const merged = new Set(cur.aliases);
      for (const a of it.aliases) merged.add(a);
      base.set(k, { title_real: it.title_real, aliases: merged });
    }
    const saved = fromIndex(base);
    writeLS(saved);
    return { data: saved };
  }

  const LocalIO = {
    async init() { return !CFG.FORCE_BACKEND; },
    async load() { return readLS(); },
    async upsert({ title_real, aliases, original_key = null }) {
      const current = readLS();
      return mergeUpsertLocal(current, { title_real, aliases, original_key });
    },
    async remove(key) {
      const current = readLS();
      return deleteLocal(current, key);
    },
    async replace(allData) {
      return replaceLocal(allData);
    },
    async export() {
      const data = readLS();
      const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
      const a = document.createElement('a');
      a.href = URL.createObjectURL(blob);
      a.download = 'game_aliases.json';
      a.click();
      URL.revokeObjectURL(a.href);
    },
    async importFile(file) {
      const txt = await file.text();
      let obj;
      try { obj = JSON.parse(txt); } catch { throw new Error('File JSON non valido'); }
      const current = readLS();
      return importLocal(current, obj);
    }
  };

  // L'istanza IO effettiva (Backend o Local) viene scelta in open()
  let IO = BackendIO;
  let usingBackend = false;

  // =========================
  // DOM / Stato UI
  // =========================
  let store = new Map();       // Map<key, {title_real, aliases:Set}>
  let undoSnapshot = null;     // ultimo snapshot completo (object)

  function ensureDialog() {
    if ($('#alias-dialog')) return;
    const wrapper = document.createElement('div');
    wrapper.id = 'alias-dialog';
    wrapper.className = 'alias-dialog';
    wrapper.innerHTML = `
      <div class="alias-panel" role="dialog" aria-modal="true" aria-label="Gestione alias">
        <div class="alias-header">
          <h3>Alias giochi</h3>
          <div class="alias-tools">
            <button class="btn btn-purple" id="alias-import">Importa</button>
            <button class="btn btn-purple" id="alias-export">Esporta</button>
            <button class="btn btn-purple" id="alias-new">Nuovo titolo</button>
            <button class="btn btn-purple" id="alias-close">Chiudi</button>
          </div>
        </div>

        <div class="alias-search">
          <input type="search" id="alias-q" placeholder="Cerca titolo o alias… (min 3)">
          <div class="meta" id="alias-meta"></div>
        </div>

        <div class="alias-results"></div>

        <div class="alias-footer">
          <button class="btn save btn-purple" id="alias-undo">Annulla ultimo salvataggio</button>
        </div>
      </div>
    `;
    document.body.appendChild(wrapper);

    // chiudi clic su backdrop
    wrapper.addEventListener('mousedown', (e) => {
      if (e.target === wrapper) { e.preventDefault(); UIAliases.close(); }
    });

    // click delegato
    wrapper.addEventListener('click', async (e) => {
      const id = e.target.id;
      if (id === 'alias-close') UIAliases.close();
      if (id === 'alias-export') IO.export();
      if (id === 'alias-new') createNew();
      if (id === 'alias-undo') undo();

      if (id === 'alias-import') {
        const inp = document.createElement('input');
        inp.type = 'file'; inp.accept = 'application/json';
        inp.addEventListener('change', async () => {
          const file = inp.files?.[0]; if (!file) return;
          try {
            undoSnapshot = fromIndex(store);
            const resp = await IO.importFile(file);
            const saved = getData(resp);
            store = toIndex(saved);
            dlgFilter(false);
            say('Import completato');
          } catch (err) {
            alert('Errore import: ' + (err.message || err));
          }
        });
        inp.click();
      }

      // card actions
      const card = e.target.closest('.alias-card');
      if (card) {
        if (e.target.classList.contains('edit')) openEditCard(card.dataset.key);
        if (e.target.classList.contains('del')) removeTitle(card.dataset.key);
      }
    });

    wrapper.addEventListener('keydown', (e) => {
      if (e.key === 'Escape') { e.preventDefault(); UIAliases.close(); }
    });

    // search
    const q = $('#alias-q', wrapper);
    let submitted = false;
    const run = () => dlgFilter(submitted);
    q.addEventListener('input', debounce(() => { submitted = false; run(); }, 120));
    q.addEventListener('keydown', (e) => {
      if (e.key === 'Enter') { e.preventDefault(); submitted = true; run(); }
      if (e.key === 'Escape') { q.value = ''; submitted = false; run(); }
    });
    q.addEventListener('search', () => { submitted = false; run(); });
  }

  function renderList(items, q, opts = {}) {
    const dlg = $('#alias-dialog');
    const res = $('.alias-results', dlg);
    const meta = $('#alias-meta', dlg);
    res.innerHTML = '';

    if (!items.length) {
      meta.innerHTML = opts.submitted ? `<span class="bad">Nessun risultato</span>` : '';
      return;
    }
    meta.textContent = '';

    const safeQ = q ? q.replace(/[.*+?^${}()|[\]\\]/g, '\\$&') : null;
    const hilite = (html) => safeQ ? html.replace(new RegExp(`(${safeQ})`, 'ig'), '<mark>$1</mark>') : html;

    for (const it of items) {
      const card = document.createElement('div');
      card.className = 'alias-card';
      card.dataset.key = keyOf(it.title_real);
      card.innerHTML = `
        <div class="alias-card__head">
          <div class="alias-title">${hilite(it.title_real)}</div>
          <div class="alias-ctas">
            <button class="btn edit">Modifica</button>
            <button class="btn del">Elimina</button>
          </div>
        </div>
        <div class="alias-chips">
          ${Array.from(it.aliases).sort(sortAsc).map(a => `<span class="alias-chip"><span>${hilite(a)}</span></span>`).join('')}
        </div>
      `;
      res.appendChild(card);
    }
  }

  function rowTpl(val) {
    return `
      <div class="alias-row">
        <input type="text" value="${(val || '').replace(/"/g, '&quot;')}">
        <button class="add" title="Aggiungi riga">＋</button>
        <button class="rm"  title="Rimuovi">−</button>
      </div>`;
  }

  function openEditCard(key) {
    const it = store.get(key);
    if (!it) return;

    const dlg = $('#alias-dialog');
    const res = $('.alias-results', dlg);
    const card = res.querySelector(`.alias-card[data-key="${key}"]`);
    if (!card) return;

    const aliases = Array.from(it.aliases).sort(sortAsc);
    card.innerHTML = `
      <div class="alias-edit">
        <div class="alias-grid">
          <label>Nome visualizzato</label>
          <input type="text" class="title-input" value="${it.title_real.replace(/"/g,'&quot;')}">
          <label>Alias</label>
          <div class="alias-list">
            ${aliases.map(a => rowTpl(a)).join('')}
            ${rowTpl('')}
          </div>
        </div>
        <div class="alias-warn"></div>
        <div class="alias-log"></div>
        <div style="display:flex; gap:6px; justify-content:flex-end;">
          <button class="btn save btn-purple">Salva</button>
          <button class="btn cancel danger">Annulla</button>
        </div>
      </div>
    `;

    const list = card.querySelector('.alias-list');
    const warn = card.querySelector('.alias-warn');
    const log  = card.querySelector('.alias-log');
    const titleInput = card.querySelector('.title-input');

    list.addEventListener('click', (e) => {
      if (e.target.classList.contains('add')) list.insertAdjacentHTML('beforeend', rowTpl(''));
      if (e.target.classList.contains('rm'))  e.target.closest('.alias-row')?.remove();
    });

    card.querySelector('.btn.cancel').addEventListener('click', () => dlgFilter(false));

    card.querySelector('.btn.save').addEventListener('click', async () => {
      warn.textContent = ''; log.textContent = '';
      const nextTitle = titleInput.value.trim();
      const original_key = key;
      const rows = Array.from(list.querySelectorAll('input[type="text"]'));
      const rawAliases = rows.map(i => i.value.trim()).filter(Boolean);

      // dedup locale base (UX)
      const seen = new Set(); const aliasesClean = [];
      for (const a of rawAliases) {
        const na = norm(a);
        if (!na || seen.has(na)) continue;
        seen.add(na); aliasesClean.push(a);
      }

      if (!nextTitle) { warn.textContent = 'Inserisci un nome visualizzato.'; return; }

      // snapshot per Undo
      undoSnapshot = fromIndex(store);

      try {
        const resp = await IO.upsert({ title_real: nextTitle, aliases: aliasesClean, original_key });
        const saved = getData(resp);
        store = toIndex(saved);

        const rep = (resp && resp.report) || {};
        const parts = [];
        if (typeof rep.added   === 'number') parts.push(`+${rep.added} aggiunti`);
        if (typeof rep.skipped === 'number') parts.push(`${rep.skipped} duplicati saltati`);
        if (rep.conflicts && rep.conflicts.length) parts.push(`conflitti: ${rep.conflicts.join(', ')}`);

        log.textContent = parts.length ? `Salvato (${parts.join(' · ')}).` : 'Salvato.';
        say('Alias salvati');
        dlgFilter(false);
      } catch (err) {
        warn.textContent = 'Errore salvataggio: ' + err.message;
      }
    });
  }

  function createNew() {
    const res = $('.alias-results', $('#alias-dialog'));
    const ghost = document.createElement('div');
    ghost.className = 'alias-card';
    const newKey = `__new__${Date.now()}`;
    ghost.dataset.key = newKey;
    ghost.innerHTML = `
      <div class="alias-edit">
        <div class="alias-grid">
          <label>Nome visualizzato</label>
          <input type="text" class="title-input" value="">
          <label>Alias</label>
          <div class="alias-list">${rowTpl('')}</div>
        </div>
        <div class="alias-warn"></div>
        <div class="alias-log"></div>
        <div style="display:flex; gap:6px; justify-content:flex-end;">
          <button class="btn save btn-purple">Salva</button>
          <button class="btn cancel">Annulla</button>
        </div>
      </div>
    `;
    res.prepend(ghost);

    const list = ghost.querySelector('.alias-list');
    const warn = ghost.querySelector('.alias-warn');
    const log  = ghost.querySelector('.alias-log');
    const titleInput = ghost.querySelector('.title-input');

    list.addEventListener('click', (e) => {
      if (e.target.classList.contains('add')) list.insertAdjacentHTML('beforeend', rowTpl(''));
      if (e.target.classList.contains('rm'))  e.target.closest('.alias-row')?.remove();
    });

    ghost.querySelector('.btn.cancel').addEventListener('click', () => ghost.remove());

    ghost.querySelector('.btn.save').addEventListener('click', async () => {
      warn.textContent = ''; log.textContent = '';
      const title = titleInput.value.trim();
      const rows = Array.from(list.querySelectorAll('input[type="text"]'));
      const rawAliases = rows.map(i => i.value.trim()).filter(Boolean);

      if (!title) { warn.textContent = 'Inserisci un nome visualizzato.'; return; }

      const seen = new Set(); const uniq = [];
      for (const a of rawAliases) {
        const na = norm(a);
        if (!na || seen.has(na)) continue;
        seen.add(na); uniq.push(a);
      }

      undoSnapshot = fromIndex(store);
      try {
        const resp = await IO.upsert({ title_real: title, aliases: uniq, original_key: null });
        const saved = getData(resp);
        store = toIndex(saved);

        const rep = (resp && resp.report) || {};
        const added = typeof rep.added === 'number' ? rep.added : uniq.length;
        log.textContent = `Creato "${title}" (${added} alias).`;
        say('Alias salvati');
        dlgFilter(false);
      } catch (err) {
        warn.textContent = 'Errore creazione: ' + err.message;
      }
    });
  }

  async function removeTitle(key) {
    const it = store.get(key);
    if (!it) return;
    if (!confirm(`Eliminare "${it.title_real}" e tutti i suoi alias?`)) return;

    undoSnapshot = fromIndex(store);
    try {
      const resp = await IO.remove(key);
      const saved = getData(resp);
      store = toIndex(saved);
      dlgFilter(false);
      say('Titolo eliminato');
    } catch (err) {
      alert('Errore eliminazione: ' + err.message);
    }
  }

  async function undo() {
    if (!undoSnapshot) return;
    try {
      const resp = await IO.replace(undoSnapshot);
      const saved = getData(resp) || undoSnapshot;
      store = toIndex(saved);
      undoSnapshot = null;
      say('Ripristinato stato precedente');
      dlgFilter(false);
    } catch (err) {
      alert('Errore ripristino: ' + err.message);
    }
  }

  function dlgFilter(submitted) {
    const dlg = $('#alias-dialog');
    const qEl = $('#alias-q', dlg);
    const q = (qEl?.value || '').trim();

    const itemsAll = Array.from(store.values()).sort((a, b) => sortAsc(a.title_real, b.title_real));
    const items = (q.length >= 3) ? itemsAll.filter(it => matches(it, q)) : itemsAll;

    renderList(items, q, { submitted });
  }

  async function open() {
    ensureDialog();
    const dlg = $('#alias-dialog');
    dlg.classList.add('open');

    // Scegli IO: backend se disponibile, altrimenti locale
    usingBackend = await BackendIO.init();
    IO = usingBackend ? BackendIO : LocalIO;

    try {
      const data = await IO.load();
      store = toIndex(data);
    } catch (err) {
      store = new Map();
      console.error('Errore caricamento alias:', err);
    }

    dlgFilter(false);
    setTimeout(() => $('#alias-q', dlg)?.focus(), 0);

    // suggerimento dev se senza backend
    if (!usingBackend) {
      console.warn('[aliases-ui] Backend non disponibile: uso fallback locale (LocalStorage).');
    }
  }

  function close() {
    $('#alias-dialog')?.classList.remove('open');
  }

  // =========================
  // API pubblica
  // =========================
  const UIAliases = { open, close };
  window.UIAliases = UIAliases;

  // Autohook sul bottone standard, se esiste
  function autoBind() {
    const btn = document.getElementById('btn-manage-aliases');
    if (btn && !btn.dataset.aliasBound) {
      btn.addEventListener('click', () => UIAliases.open());
      btn.dataset.aliasBound = '1';
    }
  }
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', autoBind);
  } else {
    autoBind();
  }
})();

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/js/app.js
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendjsappjs"></a>

```javascript
// UI utils
const $  = (s, root = document) => root.querySelector(s);
const $$ = (s, root = document) => Array.from(root.querySelectorAll(s));

function py() { return window.pywebview?.api || null; }

function setStatus(msg) {
  const el = document.getElementById('status-text');
  if (el) el.textContent = msg;
}
// Status Mini-log Drawer
function setDrawerStatus(msg, kind = 'ok') {
  const el = document.getElementById('drawer-status');
  if (!el) return;
  el.textContent = msg || '';
  el.classList.remove('ok', 'error', 'muted');
  if (!msg) {
    el.classList.add('muted');
  } else {
    el.classList.add(kind === 'error' ? 'error' : 'ok');
  }
}

// === Countdown sul mini-log del footer (status-text) ===
let footerCountdownTimer = null;

function stopFooterCountdown() {
  if (footerCountdownTimer) {
    clearInterval(footerCountdownTimer);
    footerCountdownTimer = null;
  }
}

/**
 * Mostra `baseMsg` con un countdown (es. 10s) e al termine ripristina "Pronto".
 * Si interrompe da solo se lo status cambia per altri motivi (scan, errori, ecc).
 */
function startFooterCountdown(baseMsg, seconds = 10, resetText = 'Pronto') {
  const el = document.getElementById('status-text');
  if (!el) return;

  stopFooterCountdown(); // evita doppi timer

  let remaining = Math.max(0, seconds | 0);
  // prima render immediata
  setStatus(`${baseMsg} (${remaining}s)`);

  footerCountdownTimer = setInterval(() => {
    const current = el.textContent || '';
    // se l'utente/app ha cambiato lo status, non forzare: interrompi
    if (!current.startsWith(baseMsg)) {
      stopFooterCountdown();
      return;
    }

    remaining -= 1;
    if (remaining > 0) {
      setStatus(`${baseMsg} (${remaining}s)`);
    } else {
      stopFooterCountdown();
      setStatus(resetText);
    }
  }, 1000);
}

function clearDrawerStatus() {
  const el = document.getElementById('drawer-status');
  if (!el) return;
  el.textContent = '';
  el.classList.remove('ok', 'error');
  el.classList.add('muted');
}

// Attende che pywebview.api sia pronto (max ~5s)
async function waitForPyAPI(maxMs = 5000) {
  const t0 = Date.now();
  while (!py() && (Date.now() - t0) < maxMs) {
    await new Promise(r => setTimeout(r, 100));
  }
  return !!py();
}

async function showStartupNoticeOnce() {
  try {
    await waitForPyAPI(1500);
    const api = py();
    if (!api?.get_startup_notice) return;
    const res = await api.get_startup_notice();
    const msg = res?.message || "";
    if (msg) {
      // mostra messaggio + countdown 10s e poi ripristina "Pronto"
      startFooterCountdown(msg, 10, 'Pronto');
    }
  } catch (_) {}
}

// ===== Progress bar (indeterminata) =====
function showProgressIndeterminate() {
  const wrap = document.querySelector('.progress-wrap');
  const prog = document.getElementById('progress');
  const bar  = document.querySelector('#progress .bar');
  if (wrap) wrap.removeAttribute('hidden');
  if (prog) prog.classList.add('indeterminate');
  // forza la larghezza per battere eventuale inline style width:0%
  if (bar) { bar.style.width = '28%'; } // sufficiente per far vedere l'animazione
}
function hideProgress() {
  const wrap = document.querySelector('.progress-wrap');
  const prog = document.getElementById('progress');
  const bar  = document.querySelector('#progress .bar');
  if (prog) prog.classList.remove('indeterminate');
  if (bar)  bar.style.width = '0%';
  if (wrap) wrap.setAttribute('hidden', 'true');
}

// ===== Stato scansione (per autoscroll smart) =====
let scanPoll = null;
let scanActive = false; // <— flag globale usato anche dall'autoscroll

async function startScanStatusPoll() {
  try {
    await waitForPyAPI(1200);
    const api = py();
    if (!api?.get_scan_status) return;
    // evita duplicati
    if (scanPoll) clearInterval(scanPoll);

    scanPoll = setInterval(async () => {
      try {
        const st = await api.get_scan_status(); // {active:boolean, dir:string}
        if (typeof st?.active === 'boolean') scanActive = st.active;
        if (st?.active) {
          const dir = st.dir || '';
          if (dir) setStatus('Analisi: ' + dir);
        }
      } catch (_) {}
    }, 250);
  } catch (_) {}
}
function stopScanStatusPoll() {
  if (scanPoll) {
    clearInterval(scanPoll);
    scanPoll = null;
  }
  scanActive = false; // reset sicuro
}

/* ==========================
   RENDER LIBRERIA (GRIGLIA)
========================== */
function renderGrid(games) {
  const grid  = document.getElementById('grid');
  const empty = document.getElementById('empty-state');

  // Ordina in modo case-insensitive (rete di sicurezza lato client)
  if (Array.isArray(games)) {
    games = games.slice().sort((a, b) =>
      (a.title || '').localeCompare(b.title || '', undefined, { sensitivity: 'base' })
    );
  }

  if (!games || games.length === 0) {
    if (grid)  grid.setAttribute('hidden', 'true');
    if (empty) empty.removeAttribute('hidden');
    return;
  }

  if (empty) empty.setAttribute('hidden', 'true');
  if (grid) {
    grid.removeAttribute('hidden');
    grid.innerHTML = games.map(g => {
      const cover  = (g.cover_path && g.cover_path.length > 0)
        ? g.cover_path
        : 'assets/images/empty_library.svg';
      const title  = g.title  || 'Senza titolo';
      const status = g.status || 'indexed';
      return `
        <div class="card" data-id="${g.id}">
          <img src="${cover}" alt="${title}">
          <div class="meta">
            <h4 class="title">${title}</h4>
            <div class="tags">
              <span class="badge">${status}</span>
            </div>
          </div>
        </div>
      `;
    }).join('');
  }
}

/* ==========================
   LOG DRAWER (pretty, 2 righe)
========================== */
function ellipsizeMiddle(str, max = 60) {
  if (!str || str.length <= max) return str || '';
  const keep = Math.floor((max - 3) / 2);
  return str.slice(0, keep) + '...' + str.slice(-keep);
}
function iconForStage(stage) {
  switch ((stage || '').toLowerCase()) {
    case 'start':      return '🚀'; // avvio
    case 'scan':       return '🔎'; // scansione cartella/file
    case 'scan-skip':  return '⏭️'; // cartella saltata (esclusa)
    case 'mount-iso':  return '💿'; // (se lo usi) inizio mount ISO
    case 'umount-iso': return '⏏️'; // (se lo usi) fine mount ISO
    case 'found':      return '📌'; // candidato individuato
    case 'done':       return '✅'; // completato
    case 'error':      return '❌'; // errore
    default:           return '•';  // generico
  }
}

function humanLine(e) {
  const hhmmss = e.ts?.slice(11, 19) || '';
  const p = e.payload || {};
  const stage = (p.stage || '').toLowerCase();
  const ico = iconForStage(stage);

  // RIGA 1 — intestazione compatta e leggibile
  const header = `[${hhmmss}] library/status | ${stage || 'event'}:`;

  // RIGA 2 — dettaglio per stage
  let detail = '';
  if (stage === 'start') {
    if (p.folder) detail = `folder=${ellipsizeMiddle(p.folder, 80)}`;

  } else if (stage === 'scan') {
    // mostra 'dir' o, se presente, 'item' (file) con ellissi
    const target = p.dir || p.item || '';
    detail = target ? `path=${ellipsizeMiddle(String(target), 80)}` : '(scan)';

  } else if (stage === 'scan-skip') {
    const reason = p.reason ? `, reason=${p.reason}` : '';
    detail = `path=${ellipsizeMiddle(String(p.dir || ''), 80)}${reason}`;

  } else if (stage === 'mount-iso' || stage === 'umount-iso') {
    // opzionale, se emetti questi eventi
    const target = p.item || p.dir || '';
    detail = target ? `iso=${ellipsizeMiddle(String(target), 80)}` : '(iso)';

  } else if (stage === 'found') {
    const kind = p.kind ? ` (${p.kind})` : '';
    if (p.item) detail = `item${kind}=${ellipsizeMiddle(p.item, 80)}`;
    if (p.title) detail += (detail ? ' | ' : '') + `title=${ellipsizeMiddle(p.title, 80)}`;
    if (p.note)  detail += (detail ? ' | ' : '') + `note=${p.note}`;

  } else if (stage === 'done') {
    if (typeof p.count !== 'undefined') detail = `indexed=${p.count}`;

  } else if (stage === 'error') {
    const item = p.item ? `item=${ellipsizeMiddle(p.item, 80)}` : '';
    const err  = p.error ? `error=${ellipsizeMiddle(p.error, 120)}` : '';
    detail = [item, err].filter(Boolean).join(' | ') || '(errore)';

  } else {
    // fallback generico
    const keys = Object.keys(p || {});
    detail = keys.length
      ? keys.map(k => `${k}=${ellipsizeMiddle(String(p[k]), 80)}`).join(' | ')
      : '(nessun dettaglio)';
  }

  return `${ico} ${header}\n${detail}`;
}

// ====== LOG DRAWER: live refresh + autoscroll intelligente ======
let logLiveTimer = null;

function isNearBottom(el, threshold = 16) {
  if (!el) return false;
  const delta = el.scrollHeight - el.scrollTop - el.clientHeight;
  return delta <= threshold;
}

function refreshLogsIfOpen(forceAuto = false) {
  const drawer = document.getElementById('log-drawer');
  const box = $('#logbox');
  if (drawer && drawer.classList.contains('show')) {
    // Autoscroll se forzato O se (scan attiva e siamo già vicini al fondo)
    const auto = forceAuto || (scanActive && isNearBottom(box));
    loadLogs(auto);
  }
}

function startLogLive() {
  if (logLiveTimer) clearInterval(logLiveTimer);
  logLiveTimer = setInterval(refreshLogsIfOpen, 400); // refresh leggero
}

function stopLogLive() {
  if (logLiveTimer) {
    clearInterval(logLiveTimer);
    logLiveTimer = null;
  }
}

async function loadLogs(autoscroll = false) {
  try {
    await waitForPyAPI(1200);
    const api = py();
    const box = $('#logbox');

    if (!api?.get_recent_logs) {
      if (box) box.textContent = 'Nessun log (modalità browser).';
      return;
    }

    const logs = await api.get_recent_logs('library/status', 200);
    const lines = logs.map(humanLine);
    if (box) {
      const wasNearBottom = isNearBottom(box); // per sicurezza, se serve fallback
      box.textContent = (lines.join('\n\n')) || 'Nessun evento.';
      if (autoscroll || (scanActive && wasNearBottom)) {
        // autoscroll robusto: doppio RAF (e piccolo fallback)
        requestAnimationFrame(() => {
          box.scrollTop = box.scrollHeight;
          requestAnimationFrame(() => {
            box.scrollTop = box.scrollHeight;
          });
        });
        setTimeout(() => {
          // fallback in caso di repaint lenti
          box.scrollTop = box.scrollHeight;
        }, 50);
      }
    }
  } catch (err) {
    console.warn('loadLogs error', err);
    const box = $('#logbox');
    if (box) box.textContent = 'Errore caricamento log.';
  }
}

function openLog() {
  const drawer = $('#log-drawer');
  if (!drawer) return;

  // apri drawer
  drawer.classList.add('show');

  // 🔹 resetta mini-log di stato (così ogni apertura è "pulita")
  clearDrawerStatus();

  // carica e avvia refresh
  loadLogs(true);   // autoscroll iniziale SOLO all'apertura
  startLogLive();   // poi parte il refresh periodico
}

function closeLog() {
  const drawer = $('#log-drawer');
  if (!drawer) return;

  // chiudi drawer
  drawer.classList.remove('show');

  // 🔹 ferma aggiornamento e pulisci mini-log
  stopLogLive();
  clearDrawerStatus();
}

// === Salvataggio Log in file esterno ===
function flatLine(e) {
  const ts = e.ts || '';
  const p  = e.payload || {};
  const st = (p.stage || 'event').toLowerCase();

  // Ordine leggibile e stabile per i campi più comuni
  const parts = [];
  if (st === 'start') {
    if (p.folder) parts.push(`folder=${p.folder}`);
  } else if (st === 'scan' || st === 'scan-skip') {
    if (p.dir) parts.push(`path=${p.dir}`);
    if (p.item && !p.dir) parts.push(`item=${p.item}`);
    if (p.reason) parts.push(`reason=${p.reason}`);
  } else if (st === 'found') {
    if (p.item) parts.push(`item=${p.item}`);
    if (p.kind) parts.push(`kind=${p.kind}`);
    if (p.title) parts.push(`title=${p.title}`);
    if (p.note)  parts.push(`note=${p.note}`);
  } else if (st === 'done') {
    if (typeof p.count !== 'undefined') parts.push(`indexed=${p.count}`);
  } else if (st === 'error') {
    if (p.item) parts.push(`item=${p.item}`);
    if (p.error) parts.push(`error=${p.error}`);
  } else {
    // fallback: tutto il resto (non duplicare i già messi)
    Object.keys(p).forEach(k => {
      if (['stage','folder','dir','item','kind','title','note','count','error','reason'].includes(k)) return;
      parts.push(`${k}=${String(p[k])}`);
    });
  }

  return `[${ts}] stage=${st}` + (parts.length ? ' | ' + parts.join(' | ') : '');
}

/* ==========================
   SCAN + REFRESH
========================== */
async function doScanAndRefresh(selectedPath) {
  // se era in corso un countdown (es. cleanup log), fermalo
  stopFooterCountdown();

  if (!selectedPath) {
    setStatus('Errore: percorso vuoto.');
    return;
  }

  const api = py();
  if (!api?.scan_folder || !api?.get_games) {
    setStatus('Modalità browser: scansione non disponibile');
    return;
  }

  // UI: parte UNA sola volta, non rinasce a ogni sottocartella
  showProgressIndeterminate();
  scanActive = true;        // forziamo attivo subito (prima del primo poll)
  startScanStatusPoll();
  setStatus('Analisi: ' + selectedPath);
  refreshLogsIfOpen(); // mostra subito "start" se il drawer è aperto

  try {
    const res = await api.scan_folder(selectedPath);
    if (res?.ok) {
      const games = await api.get_games();
      renderGrid(games);
      const n = typeof res.indexed === 'number' ? res.indexed : (games?.length ?? 0);
      setStatus(`Analisi Completata! | Giochi Aggiunti: ${n}`);
    } else {
      setStatus('Errore scansione');
      console.error(res?.error || 'scan_folder error');
    }
  } catch (err) {
    setStatus('Errore');
    console.error(err);
  } finally {
    stopScanStatusPoll();
    scanActive = false;         // disattiva autoscroll “automatico”
    hideProgress();
    refreshLogsIfOpen(true);    // 👈 forza autoscroll all’ultimo refresh
  }
}


/* ==========================
   VERSION INFO (sidebar+settings)
========================== */
async function loadAppInfo() {
  const setText = (id, text, title) => {
    const el = document.getElementById(id);
    if (!el) return;
    el.textContent = text;
    if (title) el.title = title; else el.removeAttribute('title');
  };

  const prettyDate = (iso) => {
    if (!iso || typeof iso !== 'string') return iso || 'N/D';
    const d = new Date(iso);
    if (isNaN(d.getTime())) return iso;
    return d.toLocaleString(undefined, { dateStyle: 'medium', timeStyle: 'short' });
  };

  const setUI = (info) => {
    setText('ui-version',      info?.ui_version || info?.tool_version || 'N/D', 'Versione UI');
    setText('status-build',    `Build ${info?.build || 'N/D'}`,        'Build');
    setText('status-channel',   info?.channel || 'N/D',                'Canale');
    setText('status-updated',   prettyDate(info?.updated_at),          'Ultimo aggiornamento');
  };

  let info = null;

  // 1) prova via pywebview
  try {
    await waitForPyAPI(1200);
    if (py()?.get_app_info) {
      const data = await py().get_app_info();
      if (data && typeof data === 'object') info = data;
    }
  } catch (_) {}

  // 2) fallback HTTP (solo se servito via web server)
  if (!info) {
    const candidates = [
      'config/appinfo.json', '../config/appinfo.json',
      '../../config/appinfo.json', '/config/appinfo.json'
    ];
    for (const url of candidates) {
      try {
        const res = await fetch(url, { cache: 'no-store' });
        if (!res.ok) continue;
        const data = await res.json().catch(()=>null);
        if (data && typeof data === 'object') { info = data; break; }
      } catch (_) {}
    }
  }

  setUI(info || {});
}

/* ==========================
   ROUTER SIDEBAR
========================== */
$$('.nav-btn').forEach(btn => {
  btn.onclick = () => {
    $$('.nav-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    const view = btn.dataset.view;
    $$('.view').forEach(v => v.classList.remove('active'));
    $('#view-' + view)?.classList.add('active');
  };
});

/* ==========================
   THEME
========================== */
const THEME_KEY = 'custominfo.theme';
function applyTheme(v){
  document.documentElement.setAttribute('data-theme', v);
  localStorage.setItem(THEME_KEY, v);
  const sel = $('#theme'); if (sel) sel.value = v;
}
function loadTheme(){
  const t = localStorage.getItem(THEME_KEY) || 'dark';
  applyTheme(t);
}

/* ==========================
   SETTINGS (binding live unificato)
========================== */

// Mappa centralizzata: idControllo -> { path, type, toUI?, fromUI? }
const SETTINGS_MAP = {
  // LIBRERIA
  'default-lib':     { path: 'library.default_folder',   type: 'string' },
  'scan-on-start':   { path: 'library.scan_on_startup',  type: 'boolean' },
  'scan-depth':      { path: 'library.max_depth',        type: 'number' },
  'dedupe-titles':   { path: 'library.dedupe_titles',    type: 'boolean' },

  // LOG
  'logs-keep':       { path: 'logs.keep_last',           type: 'number' },
  'logs-autosave':   { path: 'logs.auto_save_after_scan',type: 'boolean' },

  // UI / SCAN
  'ui-progress':     { path: 'scan.show_progress',       type: 'boolean' },
  'theme':           { path: 'ui.theme',                 type: 'string',
    toUI:  v => (v || 'dark'),
    fromUI: v => (v || 'dark')
  },

  // ESCLUSIONI (textarea con “;”)
  'dir-exclude':     { path: 'exclude.dir_keywords',     type: 'array',
    toUI:  arr => (Array.isArray(arr) ? arr.join('; ') : ''),
    fromUI: str => sanitizeList(str)
  },
  'file-exclude':    { path: 'exclude.file_keywords',    type: 'array',
    toUI:  arr => (Array.isArray(arr) ? arr.join('; ') : ''),
    fromUI: str => sanitizeList(str)
  },
  'ext-exclude':     { path: 'exclude.extensions',       type: 'array',
    toUI:  arr => (Array.isArray(arr) ? arr.join('; ') : ''),
    fromUI: str => sanitizeList(str, { lower: true, ensureDot: true })
  },

  // AVANZATE
  'sevenzip':        { path: 'paths.sevenzip',           type: 'string' },
  'alias-file':      { path: 'metadata.aliases_path',    type: 'string' },
};

// Normalizza le liste “a; b; c”
function sanitizeList(input, opts = {}) {
  const { lower=false, ensureDot=false } = opts;
  const out = (input || '')
    .split(';')
    .map(s => s.trim())
    .filter(Boolean)
    .map(s => {
      let v = lower ? s.toLowerCase() : s;
      if (ensureDot) {
        // aggiungi il punto iniziale se manca (per estensioni)
        if (!v.startsWith('.')) v = '.' + v;
      }
      return v;
    });
  // dedup preservando ordine
  return Array.from(new Set(out));
}

// Helpers di conversione
function coerceToType(value, type) {
  switch (type) {
    case 'boolean': return !!value;
    case 'number':  return Number.isFinite(+value) ? (+value) : 0;
    case 'array':   return Array.isArray(value) ? value : [];
    default:        return (value ?? '');
  }
}

async function loadSettingsIntoUI() {
  try {
    await waitForPyAPI(1200);
    const api = py();
    if (!api?.get_settings) return; // in browser
    const s = await api.get_settings(); // user.json completo

    // Popola i controlli
    Object.entries(SETTINGS_MAP).forEach(([id, meta]) => {
      const el = document.getElementById(id);
      if (!el) return;

      // estrai valore da s usando path a punti
      const parts = meta.path.split('.');
      let cur = s;
      for (const p of parts) cur = (cur && typeof cur === 'object') ? cur[p] : undefined;

      // type-safe + trasformazione toUI
      const v = meta.toUI ? meta.toUI(cur) : coerceToType(cur, meta.type);

      if (el.type === 'checkbox') el.checked = !!v;
      else el.value = (v ?? '');
    });

    // Applica anche il tema
    const themeSel = document.getElementById('theme');
    const themeVal = themeSel?.value || 'dark';
    applyTheme(themeVal);

  } catch (e) {
    console.warn('loadSettingsIntoUI error', e);
  }
}

function bindLiveSaves() {
  Object.entries(SETTINGS_MAP).forEach(([id, meta]) => {
    const el = document.getElementById(id);
    if (!el) return;

    const handler = async () => {
      const api = py();
      if (!api?.set_setting) return;

      let uiVal;
      if (el.type === 'checkbox') uiVal = el.checked;
      else uiVal = el.value;

      // trasformazione fromUI (per textarea, ecc.)
      if (meta.fromUI) {
        uiVal = meta.fromUI(uiVal);
      } else {
        // coerce leggero ai tipi base (number/bool/string)
        uiVal = coerceToType(uiVal, meta.type);
      }

      try {
        await api.set_setting(meta.path, uiVal);
        // feedback minimale nel footer (evito spam)
        // setStatus('Impostazioni aggiornate'); // opzionale
      } catch (e) {
        console.error('set_setting failed', meta.path, e);
        setStatus('Errore salvataggio impostazioni');
      }

      // caso speciale: tema → applica subito
      if (meta.path === 'ui.theme') {
        applyTheme(uiVal || 'dark');
      }
    };

    el.addEventListener('change', handler);
    // per input testuali/textarea puoi voler salvare anche su 'input'
    if (el.tagName === 'TEXTAREA' || el.tagName === 'INPUT') {
      el.addEventListener('blur', handler); // salva quando esci dal campo
    }
  });
}

// Pulsante “Ripristina default” → copia defaults.json su user.json
$('#restore')?.addEventListener('click', async () => {
  try {
    if (py()?.restore_defaults) {
      await py().restore_defaults();
      await loadSettingsIntoUI(); // ripopola i campi
      applyTheme('dark');         // default tema
    }
    setStatus('Impostazioni ripristinate');
    // Banner UX già gestito in ui-utils.js (beginSaving/endSaving se vuoi)
  } catch {
    setStatus('Errore nel ripristino delle impostazioni');
  }
});

// Pulsante “Salva” (rimane come conferma manuale opzionale)
$('#save')?.addEventListener('click', async () => {
  try {
    // Forziamo almeno il tema (gli altri sono già live)
    const theme = $('#theme')?.value || 'dark';
    applyTheme(theme);
    if (py()?.set_setting) await py().set_setting('ui.theme', theme);
    setStatus('Impostazioni salvate');
  } catch {
    setStatus('Errore salvataggio');
  }
});

// Bootstrap settings
document.addEventListener('DOMContentLoaded', async () => {
  await loadSettingsIntoUI();
  bindLiveSaves();
});


/* ==========================
   LIBRERIA: pick folder + scan
========================== */
function addHandlers(){
  $('#btn-add')?.addEventListener('click', selectFolder);
  $('#btn-empty-add')?.addEventListener('click', selectFolder);
}

async function selectFolder(){
  const btn = $('#btn-add');
  if (btn) { btn.disabled = true; btn.innerHTML = '<img src="assets/icons/plus.svg" alt=""/> Attendere…'; }

  try {
    await waitForPyAPI(1500);
    const api = py();

    if (api?.pick_folder) {
      // APP (pywebview): dialog nativo + scansione backend
      const res = await api.pick_folder();
      if (res?.ok && res.path) {
        onFolderSelected(res.path); // farà doScanAndRefresh()
      } else {
        setStatus(res?.error ? `Dialog annullato: ${res.error}` : 'Dialog annullato');
      }
    } else {
      // BROWSER: usa input hidden con webkitdirectory per scegliere una cartella
      pickFolderInBrowser();
    }
  } catch (e) {
    console.error(e);
    setStatus('Errore apertura dialog');
  } finally {
    if (btn) { btn.disabled = false; btn.innerHTML = '<img src="assets/icons/plus.svg" alt=""/> Aggiungi giochi'; }
  }
}

/* ==== Browser-only: folder picker + anteprima locale ====
   - Usa <input type="file" webkitdirectory> per ottenere l'elenco dei file
   - Applica filtri client-side per evitare update/patch
   - Mostra una "anteprima" in griglia (NON indicizza nel DB)
*/
function pickFolderInBrowser() {
  const input = document.createElement('input');
  input.type = 'file';
  // Attributi supportati da Chrome/Edge per cartelle
  input.setAttribute('webkitdirectory', '');
  input.setAttribute('directory', '');

  input.onchange = () => {
    const files = Array.from(input.files || []);
    if (!files.length) {
      setStatus('Dialog annullato');
      return;
    }

    // Deduce "root" logico dal primo file
    const first = files[0];
    const rootRel = first.webkitRelativePath?.split('/')[0] || '(cartella)';

    setStatus(`Cartella (browser): ${rootRel}`);

    // Filtri "smart" simili al backend (minimo): escludi update/patch/dlc/hotfix
    const KW_EXCLUDE = ['update', 'patch', 'hotfix', 'dlc', 'crack', 'language pack', 'langpack'];
    const shouldSkip = (p) => {
      const low = p.toLowerCase();
      return KW_EXCLUDE.some(k => low.includes(k));
    };

    // Consideriamo candidati “gioco” SOLO:
    // - file .iso
    // - cartelle che contengono setup/install (qui possiamo solo inferire dal nome file)
    const ISO = new Set(['.iso']);
    const ARCH = new Set(['.7z', '.zip', '.rar']);
    const isIso = (name) => ISO.has((name.slice(name.lastIndexOf('.')) || '').toLowerCase());
    const isArchive = (name) => ARCH.has((name.slice(name.lastIndexOf('.')) || '').toLowerCase());
    const isInstallerName = (name) => {
      const n = name.toLowerCase();
      return n.endsWith('setup.exe') || n.endsWith('install.exe') || n.includes('setup') || n.includes('install');
    };

    // Raggruppa per "cartella del gioco" (parte prima di / nel webkitRelativePath)
    const groups = new Map();
    for (const f of files) {
      const rel = f.webkitRelativePath || f.name;
      if (shouldSkip(rel)) continue;
      const top = rel.split('/')[0] || '(cartella)';
      if (!groups.has(top)) groups.set(top, []);
      groups.get(top).push(f);
    }

    // Crea “candidati” per anteprima: 1 per gruppo
    const candidates = [];
    for (const [top, list] of groups.entries()) {
      // Heuristics: preferisci ISO se presente, altrimenti un “installer-like”, altrimenti un archivio
      const iso = list.find(f => isIso(f.name));
      const inst = list.find(f => isInstallerName(f.name));
      const arch = list.find(f => isArchive(f.name));

      const chose = iso || inst || arch;
      if (!chose) continue;

      // Escludi elementi che sembrano update
      if (shouldSkip(chose.webkitRelativePath || chose.name)) continue;

      candidates.push({
        id: `browser-${top}`,
        title: top.replaceAll('_', ' ').replaceAll('.', ' ').trim() || 'Senza titolo',
        cover_path: '', // nessuna cover in browser
        status: 'preview', // badge per indicare anteprima non indicizzata
      });
    }

    renderGrid(candidates);
    // Notifica: Modalità browser, niente indicizzazione DB
    setStatus(`Modalità browser: anteprima ${candidates.length} elementi (non indicizzati)`);
  };

  input.click();
}

function onFolderSelected(path){
  setStatus('Cartella selezionata: ' + path);
  doScanAndRefresh(path);
}

/* ==========================
   SEARCH & FILTERS (placeholder)
========================== */
$('#btn-filters')?.addEventListener('click', () => {
  setStatus('Filtri: coming soon');
});

/* ==========================
   DOM READY
========================== */
document.addEventListener('DOMContentLoaded', async () => {
  loadTheme();
  addHandlers();
  loadAppInfo();
  showStartupNoticeOnce();

  // Mostra subito i giochi già a DB (niente riscansioni all’avvio)
  try {
    await waitForPyAPI(1500);
    const api = py();
    if (api?.get_games) {
      const games = await api.get_games();
      renderGrid(games);
    }
  } catch (e) {
    console.warn('get_games iniziale fallita', e);
  }

  // Log drawer open/close (pulsanti)
  $('#btn-log')?.addEventListener('click', openLog);
  $('#log-close')?.addEventListener('click', closeLog);

  // Observer: parte/ferma il live refresh anche se il drawer viene aperto/chiuso via classList altrove
  (function initLogDrawerObserver() {
    const drawer = document.getElementById('log-drawer');
    if (!drawer) return;

    const onChange = () => {
      const open = drawer.classList.contains('show');
      if (open) {
        loadLogs(true);  // primo caricamento immediato (unico autoscroll forzato)
        startLogLive();  // poi polling periodico con autoscroll smart
      } else {
        stopLogLive();
      }
    };

    // stato iniziale
    onChange();

    // osserva modifiche alla classe
    const obs = new MutationObserver(onChange);
    obs.observe(drawer, { attributes: true, attributeFilter: ['class'] });
  })();

  $('#btn-theme-toggle')?.addEventListener('click', () => {
    const cur = document.documentElement.getAttribute('data-theme') || 'dark';
    applyTheme(cur === 'dark' ? 'light' : 'dark');
  });
});

// Salvataggio log su file (runtime/Log/log_YYYYMMDD_HHMMSS.txt)
document.addEventListener('click', async (e) => {
  const btn = e.target.closest('#btn-save-log');
  if (!btn) return;

  try {
    await waitForPyAPI(1500);
    const api = py();
    if (!api?.get_recent_logs || !api?.save_log_file) {
      setDrawerStatus('Salvataggio non disponibile', 'error');
      return;
    }

    const logs = await api.get_recent_logs('library/status', 100000);
    const lines = Array.isArray(logs) ? logs.map(flatLine) : [];

    let payload = lines.join('\n');
    if (!payload) {
      const box = document.getElementById('logbox');
      if (box) payload = (box.textContent || '').replace(/\r?\n{2,}/g, '\n');
    }

    const res = await api.save_log_file(payload);

    if (res?.ok) {
      // 🔁 resetta la notifica per riavviare l'animazione
      setDrawerStatus('', 'ok'); // svuota prima
      // leggero delay per far "ripartire" l'animazione CSS
      setTimeout(() => {
        setDrawerStatus('Log salvato con successo!', 'ok');
      }, 20);
    
      // ✅ Footer: percorso
      setStatus(`Log salvato: ${res.path}`);
    } else {
      // 🔁 resetta e mostra errore (stesso meccanismo)
      setDrawerStatus('', 'error');
      setTimeout(() => {
        setDrawerStatus('Errore salvataggio log', 'error');
      }, 20);
    
      setStatus('Errore salvataggio log');
      console.error(res?.error || 'save_log_file failed');
    }
    } catch (err) {
      // 🔁 resetta e mostra errore generico
      setDrawerStatus('', 'error');
      setTimeout(() => {
        setDrawerStatus('Errore salvataggio log', 'error');
      }, 20);
    
      setStatus('Errore salvataggio log');
      console.error(err);
    }
    });  

/* ==========================
   SHORTCUT CTRL+F
========================== */
document.addEventListener('keydown', e => {
  if (e.ctrlKey && e.key.toLowerCase() === 'f') {
    e.preventDefault();
    $('#search')?.focus();
  }
});
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/frontend/js/ui-utils.js
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappfrontendjsui-utilsjs"></a>

```javascript
/*!
 * ui-utils.js  —  CustomInfo UI helpers (Settings UX pack)
 * - External number spinners (già adottati)
 * - Settings search (iniettata, sticky, highlight)
 * - Dirty state per riga + reset per campo
 * - Dirty dot sulle tab
 * - Persistenza tab e scroll
 * - Ctrl/Cmd+S → Salva con feedback
 * - Banner “bozza non salvata” e “salvataggio in corso…”
 * - PageUp/PageDown su number = step*10
 * - Drag & drop chip in #lib-list
 * - aria-live per messaggi UX
 */

const UIUtils = (() => {
  // -------------------------
  // Selettori e costanti base
  // -------------------------
  const SEL = {
    settingsView: '#view-settings',
    tabsRoot: '.settings-tabs',
    tabNav: '.settings-tabs .tab-nav',
    tabPanels: '.settings-tabs .tab-panels',
    panel: '.settings .panel',
    row: '.settings-row',
    saveBtn: '#save',
    restoreBtn: '#restore',
    draftMount: '.settings-actions',       // dove inietto “bozza non salvata”
  };

  const STORE = {
    tabKey: 'ui.settings.tab',
    scrollKey: 'ui.settings.scrollTop',
  };

  // aria-live per messaggi UX
  function ensureAriaLive() {
    if (!document.getElementById('aria-live')) {
      const live = document.createElement('div');
      live.id = 'aria-live';
      live.setAttribute('aria-live', 'polite');
      live.className = 'sr-only';
      live.style.position = 'absolute';
      live.style.left = '-9999px';
      document.body.appendChild(live);
    }
  }
  function say(msg) {
    const el = document.getElementById('aria-live');
    if (el) el.textContent = msg;
  }

  // Utility
  const $ = (sel, ctx=document) => ctx.querySelector(sel);
  const $$ = (sel, ctx=document) => Array.from(ctx.querySelectorAll(sel));
  const debounce = (fn, ms=200) => { let t; return (...a)=>{ clearTimeout(t); t=setTimeout(()=>fn(...a), ms); }; };

  // ---------------------------------------------------
  // 1) External number spinners (codice integrato tuo)
  // ---------------------------------------------------
  function initExternalSpinners() {
    const SELECTOR = 'input[type="number"][data-ui="ext-spin"]';
    const WRAP_CLASS = 'num-wrap';
    const SPIN_CLASS = 'num-spin';
    const READY_FLAG = 'extSpinReady';

    const toFloat = (x, def = 0) => {
      const n = parseFloat(x);
      return Number.isNaN(n) ? def : n;
    };
    const getStep = (el) => {
      const s = el.step?.toLowerCase?.() === 'any' ? NaN : parseFloat(el.step);
      return Number.isNaN(s) || s === 0 ? 1 : s;
    };
    const getMin = (el) => (el.min !== '' ? toFloat(el.min, null) : null);
    const getMax = (el) => (el.max !== '' ? toFloat(el.max, null) : null);
    const clamp = (val, min, max) => {
      let v = toFloat(val, 0);
      if (min != null) v = Math.max(v, min);
      if (max != null) v = Math.min(v, max);
      return v;
    };
    const decimalsOf = (num) => {
      const s = String(num);
      if (!s.includes('.')) return 0;
      return s.split('.')[1].length;
    };
    const roundToStep = (value, step, min) => {
      if (!isFinite(step) || step === 0) return value;
      const base = isFinite(min) ? min : 0;
      const k = Math.round((value - base) / step);
      return base + k * step;
    };

    function enhance(input) {
      if (!input || input.dataset[READY_FLAG]) return;

      // wrap
      const wrap = document.createElement('span');
      wrap.className = WRAP_CLASS;
      input.parentNode.insertBefore(wrap, input);
      wrap.appendChild(input);

      // spinner esterno
      const spin = document.createElement('span');
      spin.className = SPIN_CLASS;
      spin.setAttribute('aria-hidden', 'false');
      wrap.appendChild(spin);

      // bottoni
      const btnUp = document.createElement('button');
      btnUp.className = 'spin-up';
      btnUp.type = 'button';
      btnUp.setAttribute('aria-label', 'Incrementa');
      btnUp.innerHTML = `
        <svg viewBox="0 0 24 24" aria-hidden="true">
          <path d="M7 14l5-5 5 5" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>`;

      const btnDown = document.createElement('button');
      btnDown.className = 'spin-down';
      btnDown.type = 'button';
      btnDown.setAttribute('aria-label', 'Decrementa');
      btnDown.innerHTML = `
        <svg viewBox="0 0 24 24" aria-hidden="true">
          <path d="M7 10l5 5 5-5" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>`;

      spin.append(btnUp, btnDown);

      const setDisabledStates = () => {
        const val = parseFloat(input.value) || 0;
        const min = getMin(input);
        const max = getMax(input);
        btnDown.disabled = (min != null) && val <= min;
        btnUp.disabled   = (max != null) && val >= max;
      };

      const commit = (nextVal) => {
        const step = getStep(input);
        const min = getMin(input);
        const max = getMax(input);

        let v = parseFloat(nextVal);
        if (Number.isNaN(v)) v = parseFloat(input.value) || 0;

        v = roundToStep(v, step, min);
        v = clamp(v, min, max);

        const dec = Math.max(
          (String(step).split('.')[1] || '').length,
          (String(v).split('.')[1] || '').length
        );
        const out = dec > 0 ? v.toFixed(dec) : String(Math.round(v));

        if (input.value !== out) {
          input.value = out;
          input.dispatchEvent(new Event('input',  { bubbles: true }));
          input.dispatchEvent(new Event('change', { bubbles: true }));
        }
        setDisabledStates();
      };

      const doStep = (sign = +1) => {
        const cur  = parseFloat(input.value) || 0;
        const step = getStep(input);
        commit(cur + sign * step);
      };

      function makePointerRepeater(button, stepSign) {
        let holdTimer = null;
        let repeater = null;
        let activePointerId = null;

        const clearAll = () => {
          clearTimeout(holdTimer); holdTimer = null;
          clearInterval(repeater); repeater = null;
          if (activePointerId != null) {
            try { button.releasePointerCapture(activePointerId); } catch {}
          }
          activePointerId = null;
        };

        const stepOnce = () => doStep(stepSign);

        const onPointerDown = (e) => {
          if (button.disabled) return;
          e.preventDefault(); e.stopPropagation();
          activePointerId = e.pointerId;
          try { button.setPointerCapture(activePointerId); } catch {}
          stepOnce();
          holdTimer = setTimeout(() => { repeater = setInterval(stepOnce, 60); }, 350);
        };

        const onPointerUpOrCancel = (e) => {
          if (activePointerId != null && e.pointerId !== activePointerId) return;
          e.preventDefault();
          clearAll();
        };

        button.addEventListener('pointerdown', onPointerDown);
        button.addEventListener('pointerup', onPointerUpOrCancel);
        button.addEventListener('pointercancel', onPointerUpOrCancel);
        button.addEventListener('click', (e) => e.preventDefault(), true);
      }

      makePointerRepeater(btnUp,   +1);
      makePointerRepeater(btnDown, -1);

      input.addEventListener('wheel', (e) => {
        if (document.activeElement !== input) return;
        e.preventDefault();
        doStep(e.deltaY < 0 ? +1 : -1);
      }, { passive: false });

      // PageUp/PageDown = step*10
      input.addEventListener('keydown', (e) => {
        if (e.key === 'PageUp' || e.key === 'PageDown') {
          e.preventDefault();
          const cur  = parseFloat(input.value) || 0;
          const step = getStep(input) * 10;
          commit(cur + (e.key === 'PageUp' ? +step : -step));
        }
      });

      input.addEventListener('input', setDisabledStates);
      input.addEventListener('change', setDisabledStates);

      const attrObserver = new MutationObserver((muts) => {
        if (muts.some(m => ['min','max','step'].includes(m.attributeName))) setDisabledStates();
      });
      attrObserver.observe(input, { attributes: true, attributeFilter: ['min','max','step'] });

      setDisabledStates();
      input.dataset.extSpinReady = '1';
    }

    const initAll = () => document.querySelectorAll(SELECTOR).forEach(enhance);
    if (document.readyState === 'loading') document.addEventListener('DOMContentLoaded', initAll); else initAll();
    const mo = new MutationObserver(() => document.querySelectorAll(SELECTOR).forEach(enhance));
    mo.observe(document.documentElement, { childList: true, subtree: true });
  }

  // ---------------------------------------------------
  // 2) Search impostazioni (sticky, highlight, filtro)
  // ---------------------------------------------------
  function initSettingsSearch() {
    const root = $(SEL.tabsRoot);
    if (!root || $('.settings-search', root)) return;
  
    const nav = $(SEL.tabNav, root);
    const searchWrap = document.createElement('div');
    searchWrap.className = 'settings-search';
    searchWrap.innerHTML = `
      <input type="search" id="settings-search" placeholder="Cerca nelle impostazioni…">
      <div class="search-meta" id="settings-search-meta"></div>
    `;
    nav.insertAdjacentElement('afterend', searchWrap);
  
    const input = $('#settings-search', root);
    const meta  = $('#settings-search-meta', root);
  
    const getPanels = () => $$( `${SEL.tabPanels} .tab-panel`, root );
    const getActivePanel = () => {
      const checked = root.querySelector('input[type="radio"]:checked');
      if (!checked) return null;
      return root.querySelector(`${SEL.tabPanels} .tab-panel[data-tab="${checked.id}"]`);
    };
    const labelsMap = () => {
      const out = {};
      getPanels().forEach(p => {
        const id = p.getAttribute('data-tab');
        out[id] = root.querySelector(`.tab-nav label[for="${id}"]`);
      });
      return out;
    };
    const tabLabels = labelsMap();
  
    let submitted = false; // mostra "Nessun risultato" solo dopo Invio
  
    const clearMarks = (row) => {
      $$('mark', row).forEach(m => m.replaceWith(document.createTextNode(m.textContent)));
    };
    const highlightIn = (el, q) => {
      if (!el) return;
      const safe = q.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
      el.innerHTML = el.innerHTML.replace(new RegExp(`(${safe})`, 'ig'), '<mark>$1</mark>');
    };
    const clearSearchDots = () => {
      Object.values(tabLabels).forEach(l => l && l.classList.remove('tab-search-hit'));
    };
  
    const filter = () => {
      const q = input.value.trim().toLowerCase();
      const activePanel = getActivePanel();
      if (!activePanel) return;
  
      const allPanels = getPanels();
      const perTabHits = {};  // data-tab -> count
      let shownInActive = 0;
  
      // < 3 caratteri → reset
      if (q.length < 3) {
        $$(SEL.row, activePanel).forEach(row => { clearMarks(row); row.hidden = false; });
        clearSearchDots();
        meta.textContent = '';
        return;
      }
  
      allPanels.forEach(panel => {
        const rows = $$(SEL.row, panel);
        let hits = 0;
  
        rows.forEach(row => {
          const hay = row.textContent.toLowerCase();
          const hit = hay.includes(q);
  
          if (panel === activePanel) {
            clearMarks(row);
            row.hidden = !hit;
            if (hit) {
              hits++;
              [ $('label', row), $('.help', row), $('.help-label', row) ]
                .filter(Boolean)
                .forEach(el => highlightIn(el, q));
            }
          } else {
            if (hit) hits++;
          }
        });
  
        perTabHits[panel.getAttribute('data-tab')] = hits;
        if (panel === activePanel) shownInActive = hits;
      });
  
      // aggiorna puntini sulle tab “altre”
      clearSearchDots();
      Object.entries(perTabHits).forEach(([tabId, n]) => {
        const lab = tabLabels[tabId];
        if (lab && n > 0) lab.classList.add('tab-search-hit');
      });
  
      // meta
      const totalHits = Object.values(perTabHits).reduce((a,b)=>a+b,0);
      const otherHits = totalHits - shownInActive;
  
      if (shownInActive > 0) {
        meta.innerHTML = `Risultati: <b>${shownInActive}</b>`;
      } else {
        if (!submitted) { meta.innerHTML = ''; return; }
        meta.innerHTML = otherHits > 0
          ? `Nessun risultato in questa scheda, <b>${otherHits}</b> nelle altre`
          : `<span class="bad">Nessun risultato</span>`;
      }
    };
  
    // digita → filtra (debounced) e resetta "submitted"
    input.addEventListener('input', debounce(() => { submitted = false; filter(); }, 120));
    // Invio → abilita messaggio “Nessun risultato …”
    input.addEventListener('keydown', (e) => {
      if (e.key === 'Enter') { e.preventDefault(); submitted = true; filter(); }
      if (e.key === 'Escape') { input.value = ''; submitted = false; filter(); }
    });
    // “X” di cancellazione (event 'search')
    input.addEventListener('search', () => { submitted = false; filter(); });
  
    // se cambi tab con query attiva → ricalcola (NON svuotare la query)
    root.querySelectorAll('.tab-nav input[type="radio"]').forEach(r => {
      r.addEventListener('change', () => { filter(); });
    });
  }
  

// ---------------------------------------------------
// 3) Dirty state (riga + tab) & reset per campo (FIXED)
// ---------------------------------------------------
let __dirtyAPI = { markControlClean: null, resetDirtyBaselines: null };

function initDirtyTracker() {
  const settings = $(SEL.settingsView);
  if (!settings) return;

  const panelRoot = $(SEL.panel, settings) || settings;
  const rows = $$(SEL.row, panelRoot);

  // Banner “bozza non salvata” nell’area actions
  const actions = $(SEL.draftMount, settings);
  if (actions && !$('.draft-info', actions)) {
    const info = document.createElement('div');
    info.className = 'draft-info';
    info.innerHTML = `<span class="dot"></span> Bozza non salvata`;
    info.style.display = 'none';
    actions.prepend(info);
  }

  const tabForRow = (row) => {
    const panel = row.closest('.tab-panel');
    if (!panel) return null;
    const dataTab = panel.getAttribute('data-tab');
    return dataTab; // es: "tab-library"
  };

  const markTabDirty = (dataTab, on) => {
    const label = $(`.tab-nav label[for="${dataTab}"]`);
    if (!label) return;
    label.classList.toggle('tab-dirty', !!on);
  };

  const updateDraftVisibility = () => {
    const anyDirty = rows.some(r => r.classList.contains('dirty'));
    const banner = $('.draft-info', actions);
    if (banner) banner.style.display = anyDirty ? 'inline-flex' : 'none';

    const byTab = {};
    rows.forEach(r => {
      const t = tabForRow(r);
      if (t) byTab[t] = byTab[t] || r.classList.contains('dirty');
    });
    ['tab-library','tab-scan','tab-logs','tab-ui','tab-esc','tab-adv'].forEach(id => {
      markTabDirty(id, !!byTab[id]);
    });
  };

  // ---- NUOVO: tracciamo i valori originali e i reset-button per controllo
  const originals = new WeakMap();   // control -> originalValue
  const resetBtns = new WeakMap();   // control -> <button>

  const isControlChanged = (ctrl) => {
    const orig = originals.get(ctrl);
    return (ctrl.type === 'checkbox') ? (ctrl.checked !== orig)
                                      : (ctrl.value !== orig);
  };

  const computeRowDirty = (row) => {
    const controls = $$('input, select, textarea', row);
    return controls.some(isControlChanged);
  };

  const removeResetButton = (control) => {
    const btn = resetBtns.get(control);
    if (btn) {
      btn.remove();
      resetBtns.delete(control);
    }
    // se per qualche motivo esiste un field-wrap subito prima MA è vuoto → rimuovilo
    const prev = control.previousElementSibling;
    if (prev && prev.classList?.contains('field-wrap') && prev.childElementCount === 0) {
      prev.remove();
  }
    // importantissimo: consente di ricrearlo dopo nuove modifiche
    delete control.dataset.resetAttached;
  };


  // Per i number riutilizzeremo il wrapper .num-wrap (già esistente) quando presente.
  const ensureFieldWrap = (ctrl) => {
    // 1) tipologia
    const isTextarea = (ctrl.tagName === 'TEXTAREA');
    const isNumber   = (ctrl.tagName === 'INPUT' && ctrl.type === 'number');
    const isTextish  = (ctrl.tagName === 'INPUT' && /^(text|search|email|password)$/i.test(ctrl.type))
                    || (ctrl.tagName === 'SELECT');
  
    // 2) se l'input è già dentro un field-wrap → riuso
    const existingWrap = ctrl.closest('.field-wrap');
    if (existingWrap) {
      existingWrap.classList.toggle('field--textarea', isTextarea);
      existingWrap.classList.toggle('field--number',   isNumber);
      existingWrap.classList.toggle('field--text',     (isTextish && !isNumber && !isTextarea));
      return existingWrap;
    }
  
    // 3) caso speciale number: se ha il wrapper spinner esterno, quello diventa il "target"
    const baseTarget =
      (ctrl.parentElement && ctrl.parentElement.classList.contains('num-wrap'))
        ? ctrl.parentElement
        : ctrl;
  
    // 4) se subito PRIMA c'è un field-wrap "orfano" (vuoto) → riusalo invece di crearne uno nuovo
    const prev = baseTarget.previousElementSibling;
    if (prev && prev.classList?.contains('field-wrap')) {
      // se è vuoto o contiene solo spazi/commenti, lo riuso
      const hasMeaningfulChildren = Array.from(prev.childNodes)
        .some(n => n.nodeType === 1 || (n.nodeType === 3 && n.textContent.trim() !== ''));
      if (!hasMeaningfulChildren) {
        prev.classList.toggle('field--textarea', isTextarea);
        prev.classList.toggle('field--number',   isNumber);
        prev.classList.toggle('field--text',     (isTextish && !isNumber && !isTextarea));
        prev.appendChild(baseTarget);
        return prev;
      }
    }
  
    // 5) crea UN solo wrapper e sposta dentro il target
    const wrap = document.createElement('span');
    wrap.className = 'field-wrap';
    wrap.classList.toggle('field--textarea', isTextarea);
    wrap.classList.toggle('field--number',   isNumber);
    wrap.classList.toggle('field--text',     (isTextish && !isNumber && !isTextarea));
  
    baseTarget.insertAdjacentElement('beforebegin', wrap);
    wrap.appendChild(baseTarget);
  
    return wrap;
  };
  

  const addResetButton = (row, control) => {
    if (!control || control.dataset.resetAttached) return;
    const originalValue = originals.get(control);
  
    const btn = document.createElement('button');
    btn.type = 'button';
    btn.className = 'field-reset';
    btn.title = 'Ripristina valore';
    btn.textContent = '↺';
  
    const isTextLike = control.matches('input[type="text"], input[type="search"], input[type="email"], input[type="password"]');
    const isSelect   = control.matches('select');
    const isNumber   = control.matches('input[type="number"]');
  
    if (isNumber) {
      // >>> NUMERICI: reset DOPO la num-wrap (che contiene input + frecce)
      const wrap = ensureFieldWrap(control);  // se l'input è in .num-wrap, wrappa la .num-wrap
      wrap.classList.add('field--number');
      wrap.appendChild(btn); // lo mettiamo come ULTIMO figlio (dopo le frecce)
      // Mantieni la corsia aperta SOLO se al momento del click il focus è sul number
      btn.addEventListener('pointerdown', () => {
        if (document.activeElement === control) wrap.classList.add('keep-lane');
      });
      // rimuovi al termine dell'interazione
      const _closeLane = () => wrap.classList.remove('keep-lane');
      btn.addEventListener('pointerup', _closeLane);
      btn.addEventListener('pointercancel', _closeLane);
      btn.addEventListener('blur', _closeLane);

    }
    else if (isTextLike || isSelect) {
      // >>> TESTO/SELECT: reset accanto (logica già usata sul primo caso)
      const wrap = ensureFieldWrap(control);
      wrap.classList.add('field--text');
      wrap.appendChild(btn);
    }
    else {
      // fallback generico
      control.insertAdjacentElement('afterend', btn);
    }
  
    resetBtns.set(control, btn);
    control.dataset.resetAttached = '1';
  
    btn.addEventListener('click', () => {
      // ripristina
      if (control.type === 'checkbox') control.checked = !!originalValue;
      else control.value = (originalValue ?? '');
  
      control.dispatchEvent(new Event('input',  { bubbles: true }));
      control.dispatchEvent(new Event('change', { bubbles: true }));
  
      // se non è più dirty, rimuovi il reset
      if (!isControlChanged(control)) {
        btn.remove();
        resetBtns.delete(control);
        delete control.dataset.resetAttached;
      }
  
      // ricalcola stato riga e banner
      row.classList.toggle('dirty', computeRowDirty(row));
      updateDraftVisibility();
    });
  };
  


  // Setup per ogni riga/controllo
  // --- PRE-WRAP FIX: evita perdita focus spostando i campi PRIMA dei listener ---
  rows.forEach(row => {
    const controls = $$('input, select, textarea', row);
    controls.forEach(ctrl => {
      if (
        ctrl.matches('input[type="text"], input[type="search"], input[type="email"], input[type="password"]')
        || ctrl.matches('select')
        || ctrl.matches('textarea')
        || ctrl.matches('input[type="number"]')
      ) {
        ensureFieldWrap(ctrl);
      }
    });
  });

  // Setup per ogni riga/controllo
  rows.forEach(row => {
    const controls = $$('input, select, textarea', row);

    controls.forEach(ctrl => {
      // salva il valore originale iniziale
      const orig = (ctrl.type === 'checkbox') ? ctrl.checked : ctrl.value;
      originals.set(ctrl, orig);

      const onChange = () => {
        const changed = isControlChanged(ctrl);

        if (changed) {
          addResetButton(row, ctrl);
        } else {
          // se il controllo è tornato al valore originale, togli il suo reset
          removeResetButton(ctrl);
        }

        // la riga è dirty se QUALSIASI controllo dentro è cambiato
        const rowDirty = computeRowDirty(row);
        row.classList.toggle('dirty', rowDirty);

        updateDraftVisibility();
      };

      ctrl.addEventListener('input', onChange);
      ctrl.addEventListener('change', onChange);
    });
  });

  updateDraftVisibility();
}


  // ---------------------------------------------------
  // 4) Persistenza tab & scroll + Ctrl/Cmd+S + feedback
  // ---------------------------------------------------

  // Marca la label della tab attiva
  function setActiveTabLabel() {
    const tabsRoot = $(SEL.tabsRoot);
    if (!tabsRoot) return;
    const checked = tabsRoot.querySelector('input[type="radio"]:checked');
    const activeId = checked ? checked.id : null;
    $$('.tab-nav label', tabsRoot).forEach(l => {
      l.classList.toggle('tab-active', activeId && l.getAttribute('for') === activeId);
    });
  }

  function initTabAndSaveUX() {
    // memorizza tab selezionata
    $$('.settings-tabs > input[type="radio"]').forEach(r => {
      r.addEventListener('change', () => {
        if (r.checked) {
          localStorage.setItem(STORE.tabKey, r.id);
    
          // (se ora NON vuoi svuotare la query, rimuovi la riga che azzera input)
          const s = document.getElementById('settings-search');
          if (s) s.dispatchEvent(new Event('input')); // ricalcola ricerca sulla nuova tab
    
          // scroll top persistito per tab
          const cont = $('.content-scroll') || window;
          localStorage.setItem(STORE.scrollKey, '0');
          if (cont.scrollTo) cont.scrollTo({top:0, behavior:'instant'});
          else cont.scrollTop = 0;
    
          // >>> nuovo: marca la label attiva
          setActiveTabLabel();
        }
      });
    });
    

    // ripristina tab selezionata
    const savedTab = localStorage.getItem(STORE.tabKey);
    if (savedTab) {
      const r = document.getElementById(savedTab);
      if (r) r.checked = true;
    }

    // salva/restore scroll per settings
    const cont = $('.content-scroll') || window;
    const saveScroll = debounce(() => {
      localStorage.setItem(STORE.scrollKey, String(cont.scrollTop || document.documentElement.scrollTop || 0));
    }, 150);
    cont.addEventListener('scroll', saveScroll);

    const savedScroll = parseInt(localStorage.getItem(STORE.scrollKey) || '0', 10);
    if (!Number.isNaN(savedScroll)) {
      if (cont.scrollTo) cont.scrollTo({ top: savedScroll, behavior: 'instant' });
      else cont.scrollTop = savedScroll;
    }

    // Ctrl/Cmd+S
    document.addEventListener('keydown', (e) => {
      if ((e.ctrlKey || e.metaKey) && e.key.toLowerCase() === 's') {
        e.preventDefault();
        const save = $(SEL.saveBtn);
        if (save) save.click();
      }
    });

    // “Salva” → feedback banner
    const saveBtn = $(SEL.saveBtn);
    if (saveBtn && !saveBtn.dataset.saveHooked) {
      saveBtn.addEventListener('click', () => {
        beginSaving();
        // demo: termina dopo 1.2s. Integra qui la tua logica reale e poi chiama endSaving().
        setTimeout(endSaving, 1200);
      });
      saveBtn.dataset.saveHooked = '1';
    }
  }

  function beginSaving() {
    const actions = $(SEL.draftMount);
    if (!actions) return;
    const info = $('.draft-info', actions);
    if (info) {
      info.innerHTML = `<span class="loader-dot"></span><span class="loader-dot"></span><span class="loader-dot"></span> Salvataggio in corso…`;
      info.style.display = 'inline-flex';
    }
    say('Salvataggio in corso');
  }
  function endSaving() {
    const settings = $(SEL.settingsView);
    if (!settings) return;
    // azzera dirty
    $$('.settings-row.dirty', settings).forEach(r => r.classList.remove('dirty'));
    $$('.tab-nav label.tab-dirty', settings).forEach(l => l.classList.remove('tab-dirty'));
    const info = $('.draft-info', $(SEL.draftMount, settings));
    if (info) {
      info.innerHTML = `<span class="dot"></span> Impostazioni salvate`;
      setTimeout(() => { info.style.display = 'none'; }, 1400);
    }
    say('Impostazioni salvate');
  }

  // ---------------------------------------------------
  // 5) Drag & drop per chip in #lib-list (riordino)
  // ---------------------------------------------------
  function initChipDnD() {
    const list = document.getElementById('lib-list');
    if (!list) return;

    list.addEventListener('dragstart', (e) => {
      const chip = e.target.closest('.chip');
      if (!chip) return;
      chip.classList.add('dragging');
      e.dataTransfer.effectAllowed = 'move';
      e.dataTransfer.setData('text/plain', chip.dataset.id || '');
    });

    list.addEventListener('dragend', (e) => {
      const chip = e.target.closest('.chip');
      if (chip) chip.classList.remove('dragging');
    });

    list.addEventListener('dragover', (e) => {
      e.preventDefault();
      const after = getDragAfterElement(list, e.clientX, e.clientY);
      const dragging = list.querySelector('.chip.dragging');
      if (!dragging) return;
      if (after == null) list.appendChild(dragging);
      else list.insertBefore(dragging, after);
    });

    function getDragAfterElement(container, x, y) {
      const els = [...container.querySelectorAll('.chip:not(.dragging)')];
      return els.reduce((closest, child) => {
        const rect = child.getBoundingClientRect();
        const offset = y - rect.top - rect.height / 2;
        if (offset < 0 && offset > closest.offset) {
          return { offset, element: child };
        } else {
          return closest;
        }
      }, { offset: Number.NEGATIVE_INFINITY }).element || null;
    }

    // Rende i chip trascinabili (se non già)
    const observe = new MutationObserver(() => {
      $$('.chip', list).forEach(ch => {
        if (!ch.getAttribute('draggable')) ch.setAttribute('draggable', 'true');
      });
    });
    observe.observe(list, { childList: true, subtree: true });
    // init immediato
    $$('.chip', list).forEach(ch => ch.setAttribute('draggable', 'true'));
  }

  function initCollapsibles() {
    document.querySelectorAll('.collapsible').forEach(sec => {
      const head = sec.querySelector('.collapsible-header');
      const body = sec.querySelector('.collapsible-body');
      if (!head || !body) return;
  
      head.addEventListener('click', () => {
        const open = sec.classList.toggle('open');
        localStorage.setItem('ui.collapse.' + sec.id, open ? '1' : '0');
      });
  
      // ripristina stato
      const state = localStorage.getItem('ui.collapse.' + sec.id);
      if (state === '1') sec.classList.add('open');
    });
  }
  
  
  // ---------------------------------------------------
  // bootstrap generale (una sola volta)
  // ---------------------------------------------------
  function init() {
    ensureAriaLive();
    initExternalSpinners();
    initSettingsSearch();
    initDirtyTracker();
    initTabAndSaveUX();
    initChipDnD();
    initCollapsibles();
  }

  // avvio
  if (document.readyState === 'loading') document.addEventListener('DOMContentLoaded', init);
  else init();

  // API minime esposte
  return { init, beginSaving, endSaving };
})();
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/installers/templates/generic_installer.yaml
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappinstallerstemplatesgeneric-installeryaml"></a>

```yaml

# Piano di installazione generico per un gioco
steps:
  - type: 7zip_extract
    archive: "D:/Downloads/GameArchive.7z"
    destination: "D:/Games/GameFolder"
    sevenzip: "7z"
  - type: mount_iso
    path: "D:/ISOs/Game.iso"
  - type: run_setup
    exe: "D:/Games/GameFolder/setup.exe"
    args: ["/S"]
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/app/main.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCappmainpy"></a>

```python
# app/main.py
from pathlib import Path
import webview
from backend.app import create_backend

def _find_frontend_index() -> Path:
    """
    Trova il percorso assoluto di app/frontend/index.html in modo robusto.
    """
    here = Path(__file__).resolve()
    candidates = [
        here.parent / "frontend" / "index.html",               # app/frontend/index.html (più probabile)
        here.parent.parent / "app" / "frontend" / "index.html",# <root>/app/frontend/index.html
        here.parent.parent / "frontend" / "index.html",        # <root>/frontend/index.html (fallback)
    ]
    for c in candidates:
        if c.exists():
            return c
    tried = "\n - ".join(str(c) for c in candidates)
    raise FileNotFoundError("index.html non trovato. Percorsi provati:\n - " + tried)

def start():
    backend = create_backend()

    index_file = _find_frontend_index()
    index_uri = index_file.as_uri()   # -> file:///F:/.../app/frontend/index.html

    window = webview.create_window(
        title="CustomInfo Game Library",
        url=index_uri,        # carichiamo direttamente via file://
        width=1280,
        height=800,
        resizable=True,
        confirm_close=True,
    )

    # NIENTE http_server: carichiamo da file:// per evitare 404
    # Se vuoi forzare Edge WebView2: aggiungi gui="edgechromium"
    webview.start(func=backend.expose_api, debug=False)  # , gui="edgechromium"

if __name__ == "__main__":
    start()
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/config/appinfo.json
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCconfigappinfojson"></a>

```json
{
  "schema": 1,
  "tool_version": "v0.3.0",
  "ui_version": "v0.6 — Visual & Interaction Refinement (M2+)",
  "build": "2025.10.05.001",
  "channel": "dev",
  "updated_at": "2025-10-05T22:00:00Z",
  "notes": [
    "M2+ completata: unificazione stili bottoni (primary, ghost, danger, violet)",
    "Drawer log evoluto con footer, animazioni blur e transizioni migliorate",
    "Progress bar indeterminata con sfumature e glow dinamico",
    "Status log animato con effetto sheen e comparsa morbida",
    "Topbar, bottombar e sidebar uniformate con glow e blur coerenti",
    "Rifiniture generali su padding, box-sizing e allineamenti"
  ]
}

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/config/defaults.json
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCconfigdefaultsjson"></a>

```json
{
  "library": {
    "folders": [],
    "default_folder": "",
    "scan_on_startup": true,
    "max_depth": 2,
    "dedupe_titles": true
  },
  "scan": {
    "show_progress": true
  },
  "logs": {
    "keep_last": 10,
    "auto_save_after_scan": false
  },
  "exclude": {
    "dir_keywords": [],
    "file_keywords": [],
    "extensions": []
  },
  "paths": {
    "sevenzip": "7z"
  },
  "metadata": {
    "aliases_path": ""
  },
  "ui": {
    "theme": "dark"
  }
}

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/config/schema.json
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCconfigschemajson"></a>

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "CustomInfo Game Library – Settings",
  "type": "object",
  "properties": {
    "library": {
      "type": "object",
      "properties": {
        "folders": { "type": "array", "items": { "type": "string" } },
        "default_folder": { "type": "string" },
        "scan_on_startup": { "type": "boolean" },
        "max_depth": { "type": "integer", "minimum": 0, "maximum": 20 },
        "dedupe_titles": { "type": "boolean" }
      }
    },
    "scan": {
      "type": "object",
      "properties": {
        "show_progress": { "type": "boolean" }
      }
    },
    "logs": {
      "type": "object",
      "properties": {
        "keep_last": { "type": "integer", "minimum": 0, "maximum": 500 },
        "auto_save_after_scan": { "type": "boolean" }
      }
    },
    "exclude": {
      "type": "object",
      "properties": {
        "dir_keywords": { "type": "array", "items": { "type": "string" } },
        "file_keywords": { "type": "array", "items": { "type": "string" } },
        "extensions": { "type": "array", "items": { "type": "string" } }
      }
    },
    "paths": {
      "type": "object",
      "properties": {
        "sevenzip": { "type": "string", "description": "Percorso eseguibile 7-Zip (o '7z' nel PATH)" }
      }
    },
    "metadata": {
      "type": "object",
      "properties": {
        "aliases_path": { "type": "string", "description": "File JSON alias titoli; vuoto = default modulo" }
      }
    },
    "ui": {
      "type": "object",
      "properties": {
        "theme": { "type": "string", "enum": ["dark", "light"] }
      }
    }
  }
}

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/config/user.json
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCconfiguserjson"></a>

```json
{
  "library": {
    "folders": [],
    "default_folder": "C:\\Ciruzzo",
    "scan_on_startup": true,
    "max_depth": 3,
    "dedupe_titles": true
  },
  "scan": {
    "show_progress": false
  },
  "logs": {
    "keep_last": 10,
    "auto_save_after_scan": true
  },
  "exclude": {
    "dir_keywords": [],
    "file_keywords": [],
    "extensions": []
  },
  "paths": {
    "sevenzip": "7z"
  },
  "metadata": {
    "aliases_path": ""
  },
  "ui": {
    "theme": "dark"
  }
}
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/requirements.txt
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCrequirementstxt"></a>

```text
pywebview>=5.0
PyYAML>=6.0
watchdog>=4.0.0
rich>=13.0.0
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/scripts/conflict_cleaner.py
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCscriptsconflict-cleanerpy"></a>

```python
# conflict_cleaner.py
# Mini-tool GUI per rimuovere marker di conflitto Git mantenendo backup .bak
# - scegli la cartella di partenza
# - log in tempo reale
# - evita di toccare questa app se vuoi (default ON)

import os, re, sys, time
from pathlib import Path
from PyQt5 import QtWidgets, QtCore

APP_DIR = Path(__file__).resolve().parent

EXCLUDE_DIRS = {
    ".git", ".github", "__pycache__", ".idea", ".vscode",
    "node_modules", ".venv", "venv", "env", "dist", "build"
}

ALLOWED_EXTS = {
    ".py", ".pyw", ".html", ".css", ".js", ".txt", ".md",
    ".json", ".ini", ".cfg", ".yaml", ".yml", ".xml", ".csv",
}

CONFLICT_RE = re.compile(
    r"<<<<<<< .*?\n(.*?)\n=======\n(.*?)\n>>>>>>> .*$",
    re.DOTALL | re.MULTILINE
)

class CleanerWorker(QtCore.QObject):
    log = QtCore.pyqtSignal(str)
    progress = QtCore.pyqtSignal(int)
    finished = QtCore.pyqtSignal(int, int)  # (ripuliti, analizzati)

    def __init__(self, root: Path, prefer_head: bool = True, exclude_app: bool = True):
        super().__init__()
        self.root = Path(root)
        self.prefer_head = prefer_head  # True = tiene la parte HEAD
        self.exclude_app = exclude_app

    def _allowed_file(self, p: Path) -> bool:
        return p.suffix.lower() in ALLOWED_EXTS

    def _skip_dir(self, dirpath) -> bool:
        """Ritorna True se la directory va saltata (cartelle standard o la cartella dell'app)."""
        dp = Path(dirpath).resolve()

        # parts è già una tupla di stringhe (es: ('C:\\', 'Progetti', 'mioapp', '__pycache__'))
        parts = set(dp.parts)

        # escludi cartelle note presenti in qualunque livello del path
        if EXCLUDE_DIRS & parts:
            return True

        # escludi questa app (se richiesto)
        if self.exclude_app:
            try:
                app = APP_DIR.resolve()
                # salta la cartella dell'app e tutte le sue sottocartelle
                if dp == app or str(dp).startswith(str(app) + os.sep):
                    return True
            except Exception:
                pass

        return False

    def _clean_text(self, txt: str) -> str:
        # sostituisce blocchi di conflitto tenendo la metà scelta
        if self.prefer_head:
            return CONFLICT_RE.sub(lambda m: m.group(1), txt)
        else:
            return CONFLICT_RE.sub(lambda m: m.group(2), txt)

    @QtCore.pyqtSlot()
    def run(self):
        start = time.time()
        analyzed = 0
        fixed = 0

        # conta i file totali (solo ammessi)
        files = []
        for root, dirs, fs in os.walk(self.root):
            # filtra dirs in-place
            if self._skip_dir(Path(root)):
                dirs[:] = []
                continue
            dirs[:] = [d for d in dirs if d not in EXCLUDE_DIRS]
            for f in fs:
                p = Path(root) / f
                if self._allowed_file(p):
                    files.append(p)
        total = len(files)
        if total == 0:
            self.progress.emit(100)
            self.log.emit("⚠️ Nessun file corrispondente alla whitelist trovato.")
            self.finished.emit(0, 0)
            return

        self.log.emit(f"🔎 Analizzo {total} file in: {self.root}")
        for idx, p in enumerate(files, 1):
            analyzed += 1
            try:
                txt = p.read_text(encoding="utf-8", errors="ignore")
            except Exception as e:
                self.log.emit(f"⚠️ Impossibile leggere: {p} → {e}")
                self.progress.emit(int(idx / total * 100))
                continue

            if "<<<<<<< " in txt:
                # backup
                try:
                    (p.with_suffix(p.suffix + ".bak")).write_text(txt, encoding="utf-8")
                except Exception as e:
                    self.log.emit(f"⚠️ Backup fallito {p}.bak → {e}")
                    # continuiamo comunque

                # clean
                new_txt = self._clean_text(txt)
                try:
                    p.write_text(new_txt, encoding="utf-8")
                    fixed += 1
                    self.log.emit(f"🧼 Ripulito: {p.relative_to(self.root)}")
                except Exception as e:
                    self.log.emit(f"❌ Scrittura fallita {p} → {e}")
            else:
                # opzionale: log minimal per file senza conflitti
                pass

            self.progress.emit(int(idx / total * 100))

        elapsed = time.time() - start
        self.log.emit(f"✅ Completato in {elapsed:.1f}s. Ripuliti {fixed}/{analyzed} file.")
        self.finished.emit(fixed, analyzed)


class MainWin(QtWidgets.QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Conflict Cleaner")
        self.resize(720, 460)

        v = QtWidgets.QVBoxLayout(self)
        v.setSpacing(10)
        v.setContentsMargins(10, 10, 10, 10)

        # riga selezione cartella
        row = QtWidgets.QHBoxLayout()
        self.path_edit = QtWidgets.QLineEdit(str(Path.cwd()))
        btn_browse = QtWidgets.QPushButton("Sfoglia…")
        row.addWidget(QtWidgets.QLabel("Cartella da pulire:"))
        row.addWidget(self.path_edit)
        row.addWidget(btn_browse)
        v.addLayout(row)

        # opzioni
        opts = QtWidgets.QHBoxLayout()
        self.chk_exclude_app = QtWidgets.QCheckBox("Escludi questa app")
        self.chk_exclude_app.setChecked(True)
        self.rad_keep_head = QtWidgets.QRadioButton("Tieni blocco HEAD (locale)")
        self.rad_keep_incoming = QtWidgets.QRadioButton("Tieni blocco incoming (remoto)")
        self.rad_keep_head.setChecked(True)
        opts.addWidget(self.chk_exclude_app)
        opts.addStretch()
        opts.addWidget(self.rad_keep_head)
        opts.addWidget(self.rad_keep_incoming)
        v.addLayout(opts)

        # pulsanti
        btns = QtWidgets.QHBoxLayout()
        self.btn_start = QtWidgets.QPushButton("Avvia pulizia")
        self.btn_start.setStyleSheet("font-weight:600;")
        btns.addStretch()
        btns.addWidget(self.btn_start)
        v.addLayout(btns)

        # progress + console
        self.progress = QtWidgets.QProgressBar()
        self.progress.setValue(0)
        self.progress.setTextVisible(True)
        self.progress.setAlignment(QtCore.Qt.AlignCenter)
        self.progress.setFormat("%p%")  # mostra "NN%"
        self.progress.setStyleSheet("""
            QProgressBar {
                border: 1px solid #333;
                border-radius: 4px;
                text-align: center;
                color: white;
                background-color: #A1A1A1;
                height: 20px;
            }
            QProgressBar::chunk {
                background-color: #0078D7;  /* blu stile Windows 10 */
                width: 10px;
            }
        """)
        v.addWidget(self.progress)

        self.log = QtWidgets.QTextEdit()
        self.log.setReadOnly(True)
        self.log.setStyleSheet("font-family: Consolas, monospace;")
        v.addWidget(self.log, 1)

        # signals
        btn_browse.clicked.connect(self.pick_folder)
        self.btn_start.clicked.connect(self.start_clean)

        # threading
        self.thread = None
        self.worker = None

    def pick_folder(self):
        d = QtWidgets.QFileDialog.getExistingDirectory(self, "Seleziona cartella")
        if d:
            self.path_edit.setText(d)

    def start_clean(self):
        root = Path(self.path_edit.text().strip())
        if not root.exists() or not root.is_dir():
            QtWidgets.QMessageBox.warning(self, "Errore", "Seleziona una cartella valida.")
            return
        self.log.clear()
        self.progress.setValue(0)

        prefer_head = self.rad_keep_head.isChecked()
        exclude_app = self.chk_exclude_app.isChecked()

        # thread worker
        self.thread = QtCore.QThread()
        self.worker = CleanerWorker(root, prefer_head, exclude_app)
        self.worker.moveToThread(self.thread)
        self.thread.started.connect(self.worker.run)
        self.worker.log.connect(self.append_log)
        self.worker.progress.connect(self.progress.setValue)
        self.worker.finished.connect(self.thread.quit)
        self.worker.finished.connect(self.worker.deleteLater)
        self.thread.finished.connect(self.thread.deleteLater)
        self.thread.start()

    def append_log(self, s: str):
        self.log.append(s)
        self.log.ensureCursorVisible()


def main():
    app = QtWidgets.QApplication(sys.argv)
    w = MainWin()
    w.show()
    sys.exit(app.exec_())

if __name__ == "__main__":
    main()

```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/scripts/dev_test_scan.pyw
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCscriptsdev-test-scanpyw"></a>

```python
# -*- coding: utf-8 -*-
# Script: probe_installer.py
# Scopo: selezionare un EXE/MSI o una ISO, analizzare l'installer e stimare il titolo
# Note: Windows-only. Nessuna dipendenza esterna (usa ctypes + PowerShell per ISO).
import os, sys, re, ctypes, hashlib, subprocess, shlex, time
from ctypes import wintypes
import tkinter as tk
from tkinter import filedialog, messagebox
from tkinter.scrolledtext import ScrolledText

# ===================== WinAPI - Version Info (ctypes) =========================
ver = ctypes.windll.version

LPVOID = ctypes.c_void_p
PUINT  = ctypes.POINTER(ctypes.c_uint)

def _get_file_version_block(path):
    size = ver.GetFileVersionInfoSizeW(path, None)
    if not size:
        return None
    buf = ctypes.create_string_buffer(size)
    ok = ver.GetFileVersionInfoW(path, 0, size, buf)
    if not ok:
        return None
    return buf, size

def _ver_query(buf, sub_block):
    lp = LPVOID()
    lsize = ctypes.c_uint()
    ok = ver.VerQueryValueW(buf, ctypes.c_wchar_p(sub_block), ctypes.byref(lp), ctypes.byref(lsize))
    if not ok:
        return None, 0
    return lp, lsize.value

def read_version_info(path):
    """
    Legge la VersionInfo di un PE (EXE/DLL ecc.) ed estrae il maggior numero possibile di stringhe.
    Ritorna un dict {chiave: valore}. Tenta più lingue/codepage e una lista estesa di chiavi note.
    """
    res = {}
    blk = _get_file_version_block(path)
    if not blk:
        return res
    buf, _ = blk

    # 1) Tabella Translation + fallback comuni
    lp, lsize = _ver_query(buf, r"\VarFileInfo\Translation")
    translations = []
    if lp and lsize >= 4:
        count_words = lsize // 2
        arr_type = ctypes.c_ushort * count_words
        arr = ctypes.cast(lp, ctypes.POINTER(arr_type)).contents
        for i in range(0, count_words - 1, 2):
            lang = arr[i]
            code = arr[i + 1]
            if lang == 0 and code == 0:
                break
            translations.append((lang, code))

    # Fallback: coppie (lang, codepage) molto comuni
    fallbacks = [
        (0x0409, 0x04E4),  # en-US, CP1252
        (0x0409, 0x04B0),  # en-US, Unicode
        (0x0000, 0x04B0),  # neutra, Unicode
        (0x0000, 0x04E4),  # neutra, CP1252
    ]
    for fb in fallbacks:
        if fb not in translations:
            translations.append(fb)

    # 2) Lista estesa di chiavi note (copriamo casi "esotici")
    extended_keys = [
        # classici
        "ProductName","FileDescription","CompanyName","ProductVersion","FileVersion",
        "OriginalFilename","InternalName","LegalCopyright","LegalTrademarks",
        "PrivateBuild","SpecialBuild","Comments","Trademark",

        # varianti spesso usate
        "Title","ProductTitle","AppName","ApplicationName","BundleName",
        "DisplayName","DisplayVersion","PackageName","ProjectName","BuildID",
        "BuildNumber","BuildVersion","Revision","SvnRevision","GitCommit",
        "Assembly Version","AssemblyVersion","CLR Version","NetVersion",
        "Copyright",
        "Author","Publisher","Manufacturer","Vendor",
        "CompileDate","LinkerDate",

        # a volte specifici di tool
        "Inno Setup: App Name","Inno Setup: App Version",
        "Inno Setup: App Publisher","Inno Setup: App Comments",
        "NSIS: Name","NSIS: Version",
        "Installer Engine","Installer Version",
        "Product","Summary","Subject","Description",
    ]

    # 3) Estrazione: fermati alla prima translation che “rende” parecchio
    rich = {}
    for lang, code in translations:
        base = f"\\StringFileInfo\\{lang:04X}{code:04X}\\"
        any_found = False
        for k in extended_keys:
            sub = base + k
            lp2, _ = _ver_query(buf, sub)
            if lp2:
                s = ctypes.wstring_at(lp2)
                if s:
                    rich[k] = s
                    any_found = True
        if any_found:
            # unisci e esci (teniamo la prima tabella "ricca")
            res.update(rich)
            break

    # 4) Aggiungi anche VS_FIXEDFILEINFO (root "\"), utile per versioni numeriche
    lp_root, _ = _ver_query(buf, "\\")
    if lp_root:
        class VS_FIXEDFILEINFO(ctypes.Structure):
            _fields_ = [
                ("dwSignature", ctypes.c_uint),
                ("dwStrucVersion", ctypes.c_uint),
                ("dwFileVersionMS", ctypes.c_uint),
                ("dwFileVersionLS", ctypes.c_uint),
                ("dwProductVersionMS", ctypes.c_uint),
                ("dwProductVersionLS", ctypes.c_uint),
                ("dwFileFlagsMask", ctypes.c_uint),
                ("dwFileFlags", ctypes.c_uint),
                ("dwFileOS", ctypes.c_uint),
                ("dwFileType", ctypes.c_uint),
                ("dwFileSubtype", ctypes.c_uint),
                ("dwFileDateMS", ctypes.c_uint),
                ("dwFileDateLS", ctypes.c_uint),
            ]
        ffi = ctypes.cast(lp_root, ctypes.POINTER(VS_FIXEDFILEINFO)).contents
        if ffi.dwSignature == 0xFEEF04BD:
            fv = ( (ffi.dwFileVersionMS >> 16) & 0xFFFF,
                   (ffi.dwFileVersionMS      ) & 0xFFFF,
                   (ffi.dwFileVersionLS >> 16) & 0xFFFF,
                   (ffi.dwFileVersionLS      ) & 0xFFFF )
            pv = ( (ffi.dwProductVersionMS >> 16) & 0xFFFF,
                   (ffi.dwProductVersionMS      ) & 0xFFFF,
                   (ffi.dwProductVersionLS >> 16) & 0xFFFF,
                   (ffi.dwProductVersionLS      ) & 0xFFFF )
            res.setdefault("FileVersionFixed", ".".join(map(str,fv)))
            res.setdefault("ProductVersionFixed", ".".join(map(str,pv)))

    return res


# ===================== Fingerprint installers =================================
INSTALLER_PATTERNS = {
    "NSIS": [b"Nullsoft", b"NSIS "],
    "Inno Setup": [b"Inno Setup", b"innocallback", b"ISPPBuilt"],
    "InstallShield": [b"InstallShield", b"ISSetup.dll", b"isscript.msi"],
    "Wise": [b"Wise Installation Wizard", b"Wise Solutions"],
    "Squirrel": [b"Squirrel", b"RELEASES"],
    "Advanced Installer": [b"Advanced Installer"],
    "DotNetBootstrapper": [b"BootstrapperCore", b"dotnet", b"Burn by WiX"],
    "WiX Toolset / Burn": [b"Burn by WiX", b"wixstdba"],
}

def detect_installer_signature(path, max_scan_mb=8):
    size = os.path.getsize(path)
    scan = bytearray()
    try:
        with open(path, "rb") as f:
            head = f.read(min(size, max_scan_mb * 1024 * 1024))
            scan += head
            if size > len(head):
                f.seek(max(0, size - max_scan_mb * 1024 * 1024))
                tail = f.read(max_scan_mb * 1024 * 1024)
                scan += tail
    except Exception:
        return {"type": None, "matches": []}

    matches = []
    for name, pats in INSTALLER_PATTERNS.items():
        if any(p in scan for p in pats):
            matches.append(name)

    dedup = []
    for m in matches:
        if m not in dedup:
            dedup.append(m)

    return {"type": dedup[0] if dedup else None, "matches": dedup}

def suggest_silent_hint(installer_type):
    if installer_type == "NSIS":
        return r'/S'
    if installer_type == "Inno Setup":
        return r'/VERYSILENT /SUPPRESSMSGBOXES /NORESTART /SP-'
    if installer_type == "InstallShield":
        return r'/s /v"/qn"  (oppure crea un response file: setup.exe /r)'
    if installer_type == "Wise":
        return r'/s'
    if installer_type == "Squirrel":
        return r'--silent'
    if installer_type in ("Advanced Installer", "WiX Toolset / Burn", "DotNetBootstrapper"):
        return r'/quiet (o) /passive'
    return "N/D"

# ===================== Utilità ===============================================
def sha1_of_file(path, chunk=1024*1024):
    h = hashlib.sha1()
    with open(path, "rb") as f:
        while True:
            b = f.read(chunk)
            if not b: break
            h.update(b)
    return h.hexdigest()

_CLEAN_RX = re.compile(
    r'\b(setup|installer|install|launcher|config|dxsetup|redistributable|update|patch|vc_redist|vcredist|directx|physx|dotnet|runtime)\b',
    re.IGNORECASE
)
_VERSION_RX = re.compile(r'v?\s*\d+(\.\d+){0,3}([^\w]|$)', re.IGNORECASE)

def clean_title(s: str) -> str:
    if not s: return s
    s = _CLEAN_RX.sub('', s)
    s = _VERSION_RX.sub(' ', s)
    s = re.sub(r'\s{2,}', ' ', s).strip(' -—_.\t')
    return s.strip()

def guess_title(path, vi: dict):
    candidates = []
    for k in ("ProductName", "FileDescription"):
        if vi.get(k):
            candidates.append(vi[k])
    parent = os.path.basename(os.path.dirname(path)) or ''
    if parent: candidates.append(parent)
    if vi.get("OriginalFilename"):
        candidates.append(vi["OriginalFilename"])
    cleaned = [clean_title(c) for c in candidates if c]
    cleaned = [c for c in cleaned if c]
    if not cleaned:
        return None
    cleaned.sort(key=lambda x: (-len(x.split()), len(x)))
    guess = cleaned[0][:120].strip()
    guess = re.sub(r'\.(exe|msi)$', '', guess, flags=re.IGNORECASE)
    return guess or None

# ===================== ISO: mount/dismount + scan =============================
# Eseguirà PowerShell nascosto (senza finestre) per montare l'ISO e ottenere la lettera unità.
CREATE_NO_WINDOW = 0x08000000

def _run_ps(cmd):
    # Esegue powershell -NoProfile -Command "<cmd>" senza mostrare finestra
    full = ['powershell.exe', '-NoProfile', '-ExecutionPolicy', 'Bypass', '-Command', cmd]
    # Evita aperture di console
    si = subprocess.STARTUPINFO()
    si.dwFlags |= subprocess.STARTF_USESHOWWINDOW
    si.wShowWindow = 0
    try:
        out = subprocess.check_output(full, stderr=subprocess.STDOUT, startupinfo=si, creationflags=CREATE_NO_WINDOW, text=True)
        return 0, out.strip()
    except subprocess.CalledProcessError as e:
        return e.returncode, (e.output or '').strip()

def mount_iso(image_path):
    # Monta e ritorna "E:\" (drive) oppure None
    image_path = os.path.abspath(image_path)
    ps = (
        f"$img = '{image_path}'.Replace(\"'\",\"''\"); "
        f"Mount-DiskImage -ImagePath $img -ErrorAction Stop | Out-Null; "
        f"$d = (Get-DiskImage -ImagePath $img | Get-Volume | Select -Expand DriveLetter); "
        f"Write-Output ($d + ':\\')"
    )
    code, out = _run_ps(ps)
    if code != 0 or not out or not re.match(r'^[A-Z]:\\$', out):
        return None
    # attesa breve per assegnazione file system
    time.sleep(0.5)
    return out

def dismount_iso(image_path):
    image_path = os.path.abspath(image_path)
    ps = f"$img = '{image_path}'.Replace(\"'\",\"''\"); Dismount-DiskImage -ImagePath $img -ErrorAction SilentlyContinue"
    _run_ps(ps)

EXE_EXCLUDE_NAMES = re.compile(
    r'(autorun|quicksfv|dxsetup|vcredist|vc_redist|directx|physx|dotnet|netfx|'
    r'unins|uninstall|depots|redistributable|update|patch)',
    re.IGNORECASE
)


EXE_EXTS = ('.exe', '.msi')

def find_installers_in_drive(root_drive):
    """
    Ritorna lista di candidati: [{'path':..., 'size':..., 'depth':..., 'name':...}, ...]
    depth = profondità cartelle (0 = root dell'unità)
    """
    candidates = []
    root_drive = os.path.abspath(root_drive)
    for dirpath, dirnames, filenames in os.walk(root_drive):
        depth = os.path.relpath(dirpath, root_drive).count(os.sep)
        for fn in filenames:
            ext = os.path.splitext(fn)[1].lower()
            if ext not in EXE_EXTS:
                continue
            name_noext = os.path.splitext(fn)[0]
            if EXE_EXCLUDE_NAMES.search(fn) or EXE_EXCLUDE_NAMES.search(name_noext):
                continue
            fp = os.path.join(dirpath, fn)
            try:
                size = os.path.getsize(fp)
            except Exception:
                size = 0
            candidates.append({
                "path": fp,
                "size": size,
                "depth": depth,
                "name": fn
            })
    return candidates

def score_candidate(c):
    import math, os
    s = 0.0
    size = max(c["size"], 1)

    # 1) Più grande = meglio
    s += math.log2(size + 1)

    # 2) Vicino alla root = meglio (depth 0,1)
    s += max(0, 4 - c["depth"]) * 2

    name = c["name"].lower()
    base = os.path.basename(name)

    # 3) Leggera preferenza a .exe
    if c["path"].lower().endswith('.exe'):
        s += 1.5

    # 4) Bonus forte per "setup.exe" (ma NON i redistributable già esclusi)
    if base == 'setup.exe':
        s += 8

    # 5) Bonus moderato per nomi che contengono "setup" o "install"
    # (escludiamo i redistributable via regex, quindi qui va bene)
    if 'setup' in name or 'install' in name:
        s += 3

    # 6) Penalizza file minuscoli
    if size < 5 * 1024 * 1024:   # < 5 MB
        s -= 4

    return s


def pick_best_installer(candidates):
    if not candidates:
        return None, []
    scored = [(score_candidate(c), c) for c in candidates]
    scored.sort(key=lambda x: x[0], reverse=True)
    return scored[0][1], [ (round(s,2), c["path"], c["size"], c["depth"]) for s,c in scored[:30] ]

# ===================== GUI ====================================================
class App(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Probe Installer - CustomInfo")
        self.geometry("940x680")
        self.minsize(820, 560)

        top = tk.Frame(self)
        top.pack(fill="x", padx=10, pady=10)

        self.path_var = tk.StringVar()
        tk.Label(top, text="File selezionato:").pack(side="left")
        tk.Entry(top, textvariable=self.path_var, width=90).pack(side="left", padx=6)
        tk.Button(top, text="Scegli EXE/MSI…", command=self.pick_exe).pack(side="left", padx=(6,0))
        tk.Button(top, text="Scegli ISO…", command=self.pick_iso).pack(side="left", padx=(6,0))

        mid = tk.Frame(self)
        mid.pack(fill="x", padx=10, pady=(0,8))
        self.title_var = tk.StringVar()
        tk.Label(mid, text="Titolo stimato:").pack(side="left")
        tk.Entry(mid, textvariable=self.title_var, width=60).pack(side="left", padx=6)
        tk.Button(mid, text="Copia", command=self.copy_title).pack(side="left")

        self.txt = ScrolledText(self, wrap="word", font=("Consolas", 10))
        self.txt.pack(fill="both", expand=True, padx=10, pady=6)

        self.status = tk.Label(self, text="Pronto", anchor="w")
        self.status.pack(fill="x", padx=10, pady=(0,8))

        self._mounted_from = None  # path ISO montata (per dismount in caso di eccezioni)

        # Gestione chiusura: smonta se necessario
        self.protocol("WM_DELETE_WINDOW", self.on_close)

    def on_close(self):
        try:
            if self._mounted_from:
                dismount_iso(self._mounted_from)
        finally:
            self.destroy()

    def log(self, s=""):
        self.txt.insert("end", s + "\n")
        self.txt.see("end")

    def set_status(self, s):
        self.status.config(text=s)
        self.update_idletasks()

    def copy_title(self):
        t = self.title_var.get().strip()
        if not t:
            messagebox.showinfo("Copia", "Nessun titolo da copiare.")
            return
        self.clipboard_clear()
        self.clipboard_append(t)
        self.set_status("Titolo copiato negli appunti.")

    # ----------------- workflow EXE/MSI ---------------------------------------
    def pick_exe(self):
        path = filedialog.askopenfilename(
            title="Seleziona eseguibile",
            filetypes=[("Eseguibili", "*.exe *.msi"), ("Tutti i file", "*.*")]
        )
        if not path:
            return
        self.path_var.set(path)

        # ⬇️ pulisci console come “prima”
        self.txt.delete("1.0", "end")
        self.title_var.set("")
        self.set_status("Pronto")

        # analizza l’eseguibile con console già pulita
        self.analyze_file(path, clear_console=False)

    # ----------------- workflow ISO -------------------------------------------
    def pick_iso(self):
        iso = filedialog.askopenfilename(
            title="Seleziona immagine ISO",
            filetypes=[("Immagini ISO", "*.iso"), ("Tutti i file", "*.*")]
        )
        if not iso: return
        self.path_var.set(iso)
        self.analyze_iso(iso)

    def analyze_iso(self, iso_path):
        self.txt.delete("1.0", "end")
        self.title_var.set("")
        self.set_status("Montaggio ISO in corso…")

        self.log("=== ISO ======================================================")
        self.log(f"Percorso ISO:    {iso_path}")

        drive = mount_iso(iso_path)
        if not drive:
            self.log("[ERRORE] Impossibile montare l'ISO (serve Windows 10+).")
            self.set_status("Errore montaggio ISO.")
            return

        self._mounted_from = iso_path
        self.log(f"Montata su:      {drive}")
        self.set_status("Scansione contenuto ISO…")

        try:
            cands = find_installers_in_drive(drive)
            self.log("")
            self.log("=== CANDIDATI INSTALLER TROVATI =============================")
            self.log(f"(debug) Totale file candidati: {len(cands)}")
            if not cands:
                self.log("(nessun candidato .exe/.msi valido trovato)")
                return
            best, top = pick_best_installer(cands)
            for s, p, sz, d in top:
                self.log(f"[score {s:>5}] depth={d:<2} size={sz:>12}  {p}")

            self.log("")
            self.log("=== SELEZIONATO =============================================")
            self.log(best["path"])
            self.analyze_file(best["path"])
        finally:
            self.set_status("Smontaggio ISO…")
            dismount_iso(iso_path)
            self._mounted_from = None
            self.log("")
            self.log("ISO smontata.")
            self.set_status("Analisi completata.")

    # ----------------- analisi file generica ----------------------------------
    def analyze_file(self, path, clear_console=False):
        # pulizia opzionale (NON viene usata da ISO, solo quando serve)
        if clear_console:
            self.txt.delete("1.0", "end")
            self.title_var.set("")
            self.set_status("Pronto")
    
        self.set_status("Analisi eseguibile…")
        if not os.path.isfile(path):
            self.log("[ERRORE] Percorso non valido.")
            self.set_status("Errore.")
            return

        # Dati base
        self.txt.insert("end", "=== FILE =====================================================\n")
        try:
            size = os.path.getsize(path)
            sha1 = sha1_of_file(path)
        except Exception as e:
            size, sha1 = 0, "N/D"
            self.log(f"[WARN] Impossibile calcolare hash/size: {e}")

        self.log(f"Percorso:        {path}")
        self.log(f"Dimensione:      {size:,} bytes".replace(",", "."))
        self.log(f"SHA1:            {sha1}")
        self.log(f"Cartella padre:  {os.path.basename(os.path.dirname(path))}")
        self.log("")

        # Version Info
        vi = read_version_info(path)
        self.log("=== VERSION INFO =============================================")
        if not vi:
            self.log("(nessuna VersionInfo disponibile)")
        else:
            for k in ("ProductName","FileDescription","CompanyName","ProductVersion",
                      "FileVersion","OriginalFilename","InternalName","LegalCopyright"):
                v = vi.get(k)
                if v:
                    self.log(f"{k:16}: {v}")
        self.log("")

        # Fingerprint installer
        fp = detect_installer_signature(path)
        self.log("=== INSTALLER FINGERPRINT ====================================")
        if fp["matches"]:
            self.log("Tipi rilevati:   " + ", ".join(fp["matches"]))
        else:
            self.log("(nessuna firma tipica trovata)")
        hint = suggest_silent_hint(fp["type"])
        self.log(f"Tipo principale: {fp['type'] or 'N/D'}")
        self.log(f"Hint /silent:    {hint}")
        self.log("")

        # Titolo stimato
        title = guess_title(path, vi)
        self.title_var.set(title or "")
        self.log("=== TITOLO STIMATO ===========================================")
        self.log(title or "(non determinato)")
        self.log("==============================================================")

        self.set_status("Analisi completata.")

def main():
    if os.name != "nt":
        messagebox.showerror("Ambiente non supportato", "Questo tool è pensato per Windows.")
        return
    app = App()
    app.mainloop()

if __name__ == "__main__":
    # Evita crash se lanciato da shell senza GUI subsystem
    try:
        main()
    except Exception as e:
        print("Errore:", e)
```


### F:/Programmi/Programmazione/Tool Libreria Giochi PC/start.pyw
<a name="F:ProgrammiProgrammazioneTool Libreria Giochi PCstartpyw"></a>

```python
# -*- coding: utf-8 -*-
"""
CustomInfo Game Library — GUI Starter (PySide6) — FIXED
- Aggiornamenti UI thread-safe via Signal/Slot (niente più errori QObject/QThread)
- Fast path: se tutto soddisfatto, non mostra la GUI e avvia subito main.py
- La GUI compare solo se serve installare/aggiornare pacchetti
- Dopo l'installazione, avvia automaticamente main.py e si chiude
"""

from __future__ import annotations
import sys, subprocess, re, os, time
from pathlib import Path
from typing import List, Tuple, Optional
import importlib.util
import importlib.metadata as md

# ---------- Utility: trova project root (requirements + app/main.py) ----------
def find_project_root(start: Path) -> Path:
    start = start.resolve()
    for base in [start, start.parent, start.parent.parent, start.parent.parent.parent]:
        req = base / "requirements.txt"
        main = base / "app" / "main.py"
        if req.exists() and main.exists():
            return base
    for base in [start, start.parent, start.parent.parent]:
        if (base / "requirements.txt").exists():
            return base
    return start

SCRIPT_DIR = Path(__file__).resolve().parent
PROJECT_ROOT = find_project_root(SCRIPT_DIR)
REQ_FILE = PROJECT_ROOT / "requirements.txt"
MAIN_FILE = PROJECT_ROOT / "app" / "main.py"

# ---------- Bootstrap: assicura PySide6 per la GUI (solo se serve mostrarla) ----------
def _has_pkg(mod_name: str) -> bool:
    return importlib.util.find_spec(mod_name) is not None

def _pip_install_pkg(arg: str) -> int:
    try:
        return subprocess.call([sys.executable, "-m", "pip", "install", arg])
    except Exception:
        return 1

# ---------- Parse requirements + plan ----------
_spec_re = re.compile(r'^\s*([A-Za-z0-9_.\-]+)\s*([<>=!~]=?\s*[^;,\s]+)?')

def parse_requirements(req_text: str) -> List[Tuple[str, Optional[str]]]:
    out = []
    for line in req_text.splitlines():
        line = line.strip()
        if not line or line.startswith("#"):
            continue
        m = _spec_re.match(line)
        if not m:
            continue
        name = m.group(1)
        spec = m.group(2).strip() if m.group(2) else None
        out.append((name, spec))
    return out

def ensure_packaging():
    if not _has_pkg("packaging"):
        _pip_install_pkg("packaging")

def is_satisfied(name: str, spec: Optional[str]) -> bool:
    try:
        ver = md.version(name)
    except md.PackageNotFoundError:
        return False
    if not spec:
        return True
    ensure_packaging()
    from packaging.version import Version
    from packaging.specifiers import SpecifierSet
    try:
        s = SpecifierSet(spec)
        return Version(ver) in s
    except Exception:
        return False

def build_install_plan(reqs: List[Tuple[str, Optional[str]]]) -> List[Tuple[str, str]]:
    """
    Elenco (display_name, pip_arg) SOLO per pacchetti mancanti o che non rispettano i vincoli.
    """
    plan = []
    for name, spec in reqs:
        if is_satisfied(name, spec):
            continue
        pip_arg = name + (spec.replace(" ", "") if spec else "")
        plan.append((name, pip_arg))
    return plan

# ---------- Fast path: se non serve GUI, lancia direttamente main ----------
def _launch_main_and_exit():
    if MAIN_FILE.exists():
        # Avvia e termina questo processo
        subprocess.Popen([sys.executable, str(MAIN_FILE)])
    else:
        print(f"[ATTENZIONE] main.py non trovato: {MAIN_FILE}")
    sys.exit(0)

# Carica e valuta piano
reqs: List[Tuple[str, Optional[str]]] = []
total_reqs = 0
if REQ_FILE.exists():
    text = REQ_FILE.read_text(encoding="utf-8")
    reqs = parse_requirements(text)
    total_reqs = len(reqs)
plan = build_install_plan(reqs)

# Se non c'è niente da installare, nessuna GUI: vai diretto al main
if len(plan) == 0:
    _launch_main_and_exit()

# Da qui in poi SERVE mostrare GUI → assicuriamo PySide6
if not _has_pkg("PySide6"):
    print("[INFO] PySide6 non trovato. Installazione in corso...")
    rc = _pip_install_pkg("PySide6>=6.6")
    if rc != 0:
        print("[ERRORE] Impossibile installare PySide6. Esco.")
        sys.exit(1)

from PySide6.QtCore import Qt, QPoint, QThread, Signal, QObject
from PySide6.QtWidgets import (
    QApplication, QMainWindow, QWidget, QLabel, QVBoxLayout, QHBoxLayout,
    QProgressBar, QPushButton, QFrame, QGraphicsDropShadowEffect
)

# ---------- Worker su QThread (emette segnali, nessun tocco diretto alla UI) ----------
class InstallerWorker(QObject):
    sig_sub = Signal(str)
    sig_progress = Signal(int, int)       # done, total
    sig_logline = Signal(str)
    sig_finished = Signal()

    def __init__(self, plan: List[Tuple[str, str]], total: int, parent=None):
        super().__init__(parent)
        self.plan = plan
        self.total = total

    def run(self):
        total = len(self.plan)
        if total == 0:
            self.sig_progress.emit(1, 1)
            self.sig_sub.emit("Tutte le dipendenze già soddisfatte ✔")
            self.sig_finished.emit()
            return

        for idx, (name, pip_arg) in enumerate(self.plan, start=1):
            self.sig_sub.emit(f"Installazione/aggiornamento {idx}/{total} → {name}")
            cmd = [sys.executable, "-m", "pip", "install", "--upgrade", pip_arg]
            rc = self._pip_stream(cmd)
            self.sig_progress.emit(idx, total)
            if rc != 0:
                self.sig_logline.emit(f"[ERRORE] {name} (exit {rc})")

        self.sig_sub.emit("Completato ✔ Avvio programma…")
        self.sig_finished.emit()

    def _pip_stream(self, args: list[str]) -> int:
        self.sig_logline.emit("")
        try:
            p = subprocess.Popen(args, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, text=True, bufsize=1)
        except Exception as e:
            self.sig_logline.emit(f"[ERRORE avvio pip] {e}")
            return 1
        last = ""
        for line in p.stdout:
            line = line.rstrip()
            last = line
            self.sig_logline.emit(last)
        p.wait()
        return p.returncode

# ---------- GUI Starter (solo se servono installazioni) ----------
class StarterWindow(QMainWindow):
    def __init__(self, plan: List[Tuple[str, str]], total_reqs: int):
        super().__init__()
        self.plan = plan
        self.total_reqs = total_reqs

        # Finestra frameless, traslucida, trascinabile
        self.setWindowFlags(Qt.FramelessWindowHint | Qt.Tool)
        self.setAttribute(Qt.WA_TranslucentBackground, True)
        self.resize(600, 260)

        # Maschera/card
        root = QWidget(objectName="root")
        self.setCentralWidget(root)
        wrap = QVBoxLayout(root); wrap.setContentsMargins(18, 18, 18, 18)

        card = QFrame(objectName="card")
        card_layout = QVBoxLayout(card); card_layout.setContentsMargins(18, 16, 18, 16)
        wrap.addWidget(card)

        # Ombra soft
        shadow = QGraphicsDropShadowEffect(self)
        shadow.setBlurRadius(32)
        shadow.setXOffset(0)
        shadow.setYOffset(12)
        shadow.setColor(Qt.black)
        card.setGraphicsEffect(shadow)

        # Header
        header = QHBoxLayout()
        self.title = QLabel("Installazione dipendenze", objectName="title")
        header.addWidget(self.title)
        header.addStretch(1)
        self.btn_close = QPushButton("✕", objectName="icon")
        self.btn_close.clicked.connect(self.close)
        header.addWidget(self.btn_close)
        card_layout.addLayout(header)

        # Sub info
        self.sub = QLabel(f"{len(self.plan)} dipendenze da verificare / {self.total_reqs} totali", objectName="sub")
        card_layout.addWidget(self.sub)

        # Progress
        self.progress = QProgressBar()
        self.progress.setRange(0, 100)
        self.progress.setValue(0)
        self.progress.setTextVisible(True)
        self.progress.setFormat("%p%")
        card_layout.addWidget(self.progress)

        # --- Spacer al posto della logline ---
        spacer2 = QWidget()
        spacer2.setFixedHeight(22)  # margine sotto la progress bar (puoi regolare questo valore)
        card_layout.addWidget(spacer2)

        # Footer
        footer = QHBoxLayout()
        self.hint = QLabel("Suggerimento: trascina in alto per spostare la finestra", objectName="hint")
        footer.addWidget(self.hint)
        footer.addStretch(1)

        self.btn_start = QPushButton("Avvia")
        self.btn_start.setObjectName("btn_start")
        self.btn_start.clicked.connect(self.start_work)
        footer.addWidget(self.btn_start)

        card_layout.addLayout(footer)

        # --- Stile bottone Avvia ---
        self.btn_start.setStyleSheet("""
            QPushButton#btn_start {
                background: #7c5cff;
                color: #ffffff;
                font-weight: bold;        /* testo in grassetto */
                font-size: 14px;          /* dimensione font */
                border-radius: 12px;
                padding: 5px 1px;       /* altezza/larghezza regolabili con padding */
                min-width: 100px;         /* larghezza minima, non si stringe troppo */
            }
            QPushButton#btn_start:hover {
                background: #6f4def;
            }
        """)

        # Stile
        self.setStyleSheet("""
            QWidget#root { background: transparent; }
            QFrame#card {
                background: qlineargradient(x1:0,y1:0,x2:0,y2:1, stop:0 #111826, stop:1 #0b101a);
                border: 1px solid #1b2334; border-radius: 16px;
            }
            QLabel#title { color: #65a2ff; font: 700 16px "Segoe UI"; }
            QLabel#sub   { color: #8a94a8; font: 12px "Segoe UI"; margin-top: 4px; }
            QLabel#hint  { color: #8a94a8; font: 11px "Segoe UI"; }
            QLabel#logline { color: #e6ebf3; font: 12px "Consolas, Cascadia Mono, Courier New"; margin-top: 8px; }
            QPushButton#icon {
                background: transparent; border: 1px solid #27324a; color: #e6ebf3;
                border-radius: 10px; padding: 6px 8px;
            }
            QPushButton#icon:hover { background: #1a2233; }
            QPushButton {
                background: #7c5cff; color: #fff; border: none; border-radius: 12px; padding: 10px 16px;
            }
            QPushButton:hover { background: #6f4def; }
            QProgressBar {
                background: #0f1422; border: 1px solid #1b2334; border-radius: 12px; text-align: center;
                color: #ffffff; font: 600 12px "Segoe UI"; padding: 3px;
            }
            QProgressBar::chunk {
                background-color: #65a2ff; border-radius: 12px;
            }
        """)

        # Thread + worker
        self.thread = QThread(self)
        self.worker = InstallerWorker(plan=self.plan, total=len(self.plan))
        self.worker.moveToThread(self.thread)
        self.thread.started.connect(self.worker.run)
        self.worker.sig_sub.connect(self.on_sub)
        self.worker.sig_progress.connect(self.on_progress)
        self.worker.sig_finished.connect(self.on_finished)
        # cleanup
        self.worker.sig_finished.connect(self.thread.quit)
        self.thread.finished.connect(self.thread.deleteLater)

    # --- UI slots (thread-safe) ---
    def on_sub(self, text: str):
        self.sub.setText(text)

    def on_progress(self, done: int, total: int):
        pct = int((done / max(1, total)) * 100)
        self.progress.setValue(pct)

    def on_finished(self):
        # Avvia main e chiudi
        if MAIN_FILE.exists():
            subprocess.Popen([sys.executable, str(MAIN_FILE)])
        else:
            print(f"[ATTENZIONE] main.py non trovato: {MAIN_FILE}")
            time.sleep(1.2)
        QApplication.instance().quit()

    # --- Actions ---
    def start_work(self):
        self.btn_start.setEnabled(False)
        self.thread.start()

# --- Drag window ---
    def mousePressEvent(self, e):
        if e.button() == Qt.LeftButton:
            self._drag_offset = e.globalPosition().toPoint() - self.frameGeometry().topLeft()

    def mouseMoveEvent(self, e):
        if self._drag_offset is not None and e.buttons() & Qt.LeftButton:
            self.move(e.globalPosition().toPoint() - self._drag_offset)

    def mouseReleaseEvent(self, e):
        self._drag_offset = None


# ---------- Main ----------
def main():
    # packaging serve per confronti versione
    ensure_packaging()

    app = QApplication(sys.argv)
    win = StarterWindow(plan=plan, total_reqs=total_reqs)
    win.show()
    return app.exec()

if __name__ == "__main__":
    sys.exit(main())

```
