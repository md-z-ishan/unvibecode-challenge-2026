# UnvibeCode Run Notes & Evaluation

## 1. Developer Visual Experience (What the Developer Sees)

Based on `target_repo-unvibecode-full.html` and `report-support/business-workflows.html`:

- **Master Navigation Interface:** `target_repo-unvibecode-full.html` renders a tabbed dashboard with three primary views: **"Business Workflows"**, **"Connected Code"** (interactive code graph with search/filters), and **"LLM Context"** (download button and sample LLM prompts for `target_repo-repository-context.zip`).
- **Grouping into Business Workflow Areas:** The Business Workflows report partitions the identified workflows into two distinct functional groupings: **"Workflow area 1: Request workflows"** and **"Workflow area 2: Blueprint workflows"**.
- **Four Reconstructed Workflows:** Exactly 4 numbered business workflow cards are displayed:
  1. *Workflow 1:* "Manage request session data with signed cookies"
  2. *Workflow 2:* "HTTP request dispatch and response production"
  3. *Workflow 3:* "Register Blueprint routes, callbacks, and nested blueprints"
  4. *Workflow 4:* "Serve a file from a Blueprint static folder"
- **Structured Card Metadata:** Each workflow card presents a standardized structure containing: a high-level summary paragraph ("What this workflow does"), a 3-part context grid (**Purpose**, **What it produces**, **Who relies on it**), and a business impact section ("Why this workflow matters").
- **Collapsible Implementation Evidence:** At the bottom of each workflow card is an expandable dropdown (`<details class="supporting-code">` titled "Implementation references") linking directly to the specific symbols, file paths, and line spans responsible for that behavior.

---

## 2. Spot-Check Against Flask Source Code

| # | Claim from UnvibeCode | Target File & Claimed Line Span | Verdict | Evidence / Source Code Check |
|---|-----------------------|---------------------------------|---------|------------------------------|
| **1** | `SecureCookieSessionInterface.open_session()` opens/extracts session data from signed cookie: `src/flask/sessions.py · lines 323–335` | `src/flask/sessions.py` [lines 323–336] | **Correct** | Lines 323–336 define `def open_session(self, app: Flask, request: Request) -> SecureCookieSession | None:`, extracting cookie `request.cookies.get(self.get_cookie_name(app))` and decoding it via `s.loads(val, max_age=max_age)`. |
| **2** | `Flask.wsgi_app()` serves as the WSGI entrypoint and request lifecycle orchestrator: `src/flask/app.py · lines 1627–1677` | `src/flask/app.py` [lines 1627–1677] | **Correct** | Lines 1627–1677 define `def wsgi_app(self, environ: WSGIEnvironment, start_response: StartResponse) -> cabc.Iterable[bytes]:` handling request context pushing, error catching, and WSGI response dispatching. |
| **3** | `Blueprint.send_static_file()` handles serving files from a Blueprint's static folder: `src/flask/blueprints.py · lines 82–102` | `src/flask/blueprints.py` [lines 82–100] | **Correct** | Lines 82–100 implement `def send_static_file(self, filename: str) -> Response:`, checking `self.has_static_folder` and delegating to `send_from_directory(...)`. |

---

## 3. Friction Log (Facts Only)

- **Number of Steps to Install & Run:**
  - 5 explicit CLI commands required:
    1. Virtual environment creation: `python -m venv .venv`
    2. Virtual environment activation: `.\.venv\Scripts\Activate.ps1`
    3. Package installation: `python -m pip install --upgrade unvibecode`
    4. Code repository setup: `git clone https://github.com/pallets/flask ./target_repo`
    5. Execution: `python -m unvibecode review --repository "./target_repo"`
- **Wait Time:**
  - `unvibecode` package installation: ~55 seconds (network fetch & wheel install).
  - Target repo clone: ~10 seconds.
  - Review execution runtime: **1 minute 48 seconds** total process duration (internal analysis engine logged `1m 29s`).
- **Output Size & Format:**
  - 17 total filesystem artifacts generated across nested directories:
    - Interactive HTML reports: `target_repo-unvibecode-full.html` (25 KB), `business-workflows.html` (25 KB), `connected-code.html` (1.11 MB).
    - Data store / graph script: `fast_lane_context_store.js` (1.47 MB).
    - Repository bundle: `target_repo-repository-context.zip` (262 KB compressed).
    - Technical logs and status trackers: `technical-logs/` (5 files, ~13 KB total).
    - Total bundle footprint on disk: ~2.9 MB.
- **Manual Repetition Required for Developers:**
  - **No local auto-server or launcher:** The CLI prints local file paths (`file:///...`) rather than serving them over localhost or opening a browser automatically. Developers must manually navigate to and open `unvibecode_results\<run-timestamp>\target_repo-unvibecode-full.html` in a web browser every time.
  - **Timestamped subfolder sprawl:** Every invocation generates a new folder timestamped down to the second (e.g. `target_repo-unvibecode-analysis-YYYYMMDD_HHMMSS`), requiring the developer to find the newest folder path manually rather than having a fixed `latest` symlink or predictable output directory.
  - **No CLI output export flag (e.g., Markdown/JSON stdout):** The review summary in terminal only lists high-level bullet titles. To inspect full workflow details or symbol linkages programmatically without parsing HTML/JS files, additional custom parsing is needed.
