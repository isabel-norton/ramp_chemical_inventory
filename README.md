# Lab Chemical Inventory — Supabase Edition

A mobile-friendly Streamlit app for adding chemicals, recording use, viewing remaining stock, archiving chemicals, and exporting CSV records. Data is stored in Supabase PostgreSQL, not the Streamlit server's temporary disk. No email notifications.

## 1. Create Supabase database
1. Sign in at https://supabase.com and create a new project.
2. Choose a project name, region, and **database password**. Save that password securely. It is **not** your college Outlook password.
3. Wait until the database is ready.
4. Open your project's **Connect** panel and choose the **Session pooler** connection string (URI). Prefer Session pooler for compatibility with Streamlit Community Cloud networking. Copy the **exact URI provided by Supabase**, including host, username, and port; replace `[YOUR-PASSWORD]` with your database password. If your password contains special URL characters (`@`, `#`, `/`, `:`, `%`, etc.), percent-encode it in the URI or generate a URL-safe database password. Do not use a Supabase anon/publishable API key in place of the database password.
5. Tables are created automatically the first time the app starts. Alternatively, paste `schema.sql` into Supabase **SQL Editor** and run it.

## 2. Configure local secrets
Create `.streamlit/secrets.toml` by copying `.streamlit/secrets.toml.example`, then replace the sample URI with the actual **Session pooler** URI:

```toml
SUPABASE_DB_URL = "postgresql://YOUR_POOLER_USER:YOUR_DATABASE_PASSWORD@YOUR_POOLER_HOST:5432/postgres"
```

Never commit `.streamlit/secrets.toml` or paste the connection string into public files. `.gitignore` excludes it.

## 3. Run locally
Install Python 3.10 or later, then run:

```bash
python -m venv .venv
# Windows PowerShell:
.venv\Scripts\Activate.ps1
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
streamlit run app.py
```

## 4. Deploy on Streamlit Community Cloud
1. Push `app.py`, `requirements.txt`, `schema.sql`, `README.md`, `.gitignore`, and `.streamlit/secrets.toml.example` to a GitHub repository. **Do not push `.streamlit/secrets.toml`.**
2. At https://share.streamlit.io, deploy the repository with `app.py` as the entry point.
3. In the app's settings, open **Secrets** and paste your actual TOML setting:

```toml
SUPABASE_DB_URL = "postgresql://YOUR_POOLER_USER:YOUR_DATABASE_PASSWORD@YOUR_POOLER_HOST:5432/postgres"
```

4. Save and restart/reboot the app if necessary. Add a test chemical, log some usage, then reboot or redeploy. The records should remain because they are in Supabase.
5. Copy the app's HTTPS URL and create a QR code pointing to it; post it in the lab. Phones can open the same shared inventory.

Official guide: https://docs.streamlit.io/deploy/streamlit-community-cloud/deploy-your-app/secrets-management

## Notes and security
- The app supports mg/g/kg, µL/mL/L, and items; conversions only occur within the same measurement group.
- The starting amount is the initial stock. Remaining stock is calculated from saved usage logs. To add new stock later, add another bottle/chemical record; restocking an existing record is not yet supported.
- The app uses a database login with write privileges. Protect it as a secret and do not expose it in frontend code or a public repository.
- **No built-in user login, permissions, or audit log.** Anyone who can open the app can edit data and delete logs. For a real shared laboratory, add authentication/role restrictions and coordinate with your institution's IT/security requirements before distributing the QR code.
- Back up your data and confirm your Supabase plan's backup/retention features. Usage deletions are permanent unless recovered from backups.
- The database is shared across app instances. Usage submissions lock the chemical row to avoid concurrent negative inventory.
