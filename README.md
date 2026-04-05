# ResumeAI — Analyzer + Job Matcher

A full-stack AI-powered resume analyzer built with **React** (frontend) + **Django** (backend) + **OpenAI GPT-4o** (AI engine).

---

## Features

- **Resume Upload** — PDF, DOCX, or paste plain text
- **AI Analysis** — Skills detection, ATS scoring, keyword gap analysis
- **Suggestions** — Personalized improvement tips powered by GPT-4o
- **Job Matching** — Match resume against 7+ job profiles with ranked scores
- **Dashboard** — Radar chart, score bars, skill cloud, suggestion panel

---

## Project Structure

```
resumeai/
├── backend/                  # Django project
│   ├── config/               # Settings, URLs, WSGI
│   ├── apps/
│   │   ├── resume/           # Upload, analyze, AI engine
│   │   └── jobs/             # Job matching
│   ├── requirements.txt
│   └── manage.py
├── frontend/                 # React + Vite project
│   ├── src/
│   │   ├── api/              # Axios API client
│   │   ├── components/       # Reusable UI components
│   │   ├── pages/            # Upload, Dashboard, Jobs pages
│   │   ├── store/            # Zustand global state
│   │   └── utils/            # Helper functions
│   ├── package.json
│   └── vite.config.js
├── .env.example
└── README.md
```

---

## Setup Instructions

### 1. Clone and configure environment

```bash
git clone https://github.com/yourname/resumeai.git
cd resumeai
cp .env.example .env
# Edit .env and add your OPENAI_API_KEY
```

---

### 2. Backend Setup (Django)

```bash
cd backend

# Create virtual environment
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run migrations
python manage.py migrate

# Create superuser (optional, for admin panel)
python manage.py createsuperuser

# Start Django server
python manage.py runserver
```

Django will run at: **http://localhost:8000**

---

### 3. Frontend Setup (React)

```bash
cd frontend

# Install dependencies
npm install

# Start dev server
npm run dev
```

React will run at: **http://localhost:5173**

> API calls are automatically proxied to Django via `vite.config.js`.

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/resume/upload/` | Upload PDF/DOCX or paste text |
| `POST` | `/api/resume/analyze/` | Run AI analysis on uploaded resume |
| `GET`  | `/api/resume/<id>/` | Get saved resume + analysis |
| `GET`  | `/api/resume/list/` | List all resumes (auth required) |
| `GET`  | `/api/jobs/matches/<id>/` | Get job matches for a resume |
| `GET`  | `/api/jobs/` | List all available job profiles |
| `POST` | `/api/auth/token/` | Get JWT token (login) |
| `POST` | `/api/auth/token/refresh/` | Refresh JWT token |

---

## How the AI Engine Works

```
Resume text
    │
    ├── detect_skills()       → Keyword regex matching against 80+ known skills
    │                           Returns skill name + level (strong/moderate/basic)
    │
    ├── compute_scores()      → Calculates 5 sub-scores:
    │                           Skills (30%), Experience (25%),
    │                           Keywords (25%), Format (20%)
    │
    ├── analyze_with_openai() → GPT-4o generates:
    │                           4-6 actionable suggestions
    │                           Extracted profile info
    │                           Strengths + improvement areas
    │
    └── match_jobs()          → Cosine-style matching:
                                Required skills (50%) +
                                Bonus skills (30%) +
                                Keyword hits (20%)
```

---

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18, Vite, Tailwind CSS |
| State | Zustand |
| Charts | Recharts |
| File Upload | react-dropzone |
| Backend | Django 4.2, Django REST Framework |
| Auth | JWT (djangorestframework-simplejwt) |
| AI | OpenAI GPT-4o |
| NLP | regex + keyword matching (spaCy optional) |
| PDF parsing | pdfminer.six |
| DOCX parsing | python-docx |
| Database | SQLite (dev) → PostgreSQL (production) |

---

## Production Deployment

### Backend (e.g., Railway, Render, DigitalOcean)

```bash
# Install production deps
pip install gunicorn psycopg2-binary whitenoise

# Set environment variables on your host:
SECRET_KEY=<strong-random-key>
DEBUG=False
OPENAI_API_KEY=<your-key>
DATABASE_URL=postgresql://...

# Run with gunicorn
gunicorn config.wsgi:application --bind 0.0.0.0:8000
```

### Frontend (e.g., Vercel, Netlify)

```bash
npm run build
# Deploy the dist/ folder
# Set VITE_API_BASE_URL to your Django server URL
```

---

## Adding spaCy (Optional Enhancement)

For more accurate NLP entity extraction:

```bash
pip install spacy
python -m spacy download en_core_web_sm
```

Then in `ai_engine.py`, replace the regex skill detector with:

```python
import spacy
nlp = spacy.load("en_core_web_sm")

def detect_skills_spacy(text):
    doc = nlp(text)
    # Extract named entities, noun chunks, etc.
    ...
```

---

## License

MIT — use freely for learning and projects.
