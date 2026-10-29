# 📄 ATS Resume Analyzer

Upload a resume (PDF or DOCX) and get an **ATS score out of 100**, plus prioritized, concrete
improvements. Built with **Streamlit** and **Google Gemini Flash**.

## Features

- Reads text-based **PDF** and **DOCX** resumes
- **ATS score** = 40% rule-based checks + 60% AI content score
- Optional **job description** input for role-specific keyword matching
- Prioritized improvements (High / Medium / Low) with example rewrites
- Matched vs. missing keywords, per-section feedback, strengths
- Downloadable Markdown report
- Friendly handling of scanned PDFs, corrupted files, bad API keys and rate limits

## How the score works

| Part | Weight | What it measures |
|------|--------|------------------|
| Rule-based checks | 40% | Email/phone/links, standard section headings, length, action verbs, quantified achievements, parser-friendly formatting (e.g. tables) |
| AI content score (Gemini) | 60% | Keyword relevance, clarity, impact, structure, and fit to the job description if provided |

> ⚠️ **Honest disclaimer:** there is no single universal "ATS score". Real systems (Workday, Greenhouse,
> Lever, ...) differ. This score is a practical heuristic that highlights real, fixable problems - not a guarantee
> of how any specific employer's system will rank you.

## Project structure

```
.
├── app.py              # Streamlit app (UI, parsing, scoring, Gemini call)
├── requirements.txt    # Python dependencies
├── README.md
└── .gitignore          # keeps your API key out of Git
```

## Run locally

**1. Get a free Gemini API key** at <https://aistudio.google.com/apikey>.

**2. Install and run** (Python 3.9+; tested on 3.12):

```bash
git clone https://github.com/<your-username>/ats-resume-analyzer.git
cd ats-resume-analyzer

python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

**3. Provide your API key** (pick one):

- **Option A – secrets file (recommended):** create `.streamlit/secrets.toml`

  ```toml
  GEMINI_API_KEY = "your-key-here"
  # GEMINI_MODEL = "gemini-3.7-flash"   # optional
  ```

- **Option B – environment variable:**
  `export GEMINI_API_KEY="your-key-here"` (Windows PowerShell: `$env:GEMINI_API_KEY="your-key-here"`)
- **Option C – paste it in the app sidebar** at runtime.

**4. Start the app:**

```bash
streamlit run app.py
```

## Deploy on Streamlit Community Cloud (free)

1. Push this project to a **GitHub** repository (see below).
2. Go to <https://share.streamlit.io> and sign in with GitHub.
3. Click **Create app** → choose your repo, branch `main`, and main file `app.py`.
4. Open **Advanced settings → Secrets** and paste:
   ```toml
   GEMINI_API_KEY = "your-key-here"
   ```
5. Click **Deploy**. You'll get a public `*.streamlit.app` URL.

## Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit: ATS resume analyzer"
git branch -M main
git remote add origin https://github.com/<your-username>/ats-resume-analyzer.git
git push -u origin main
```

(Create the empty repository on github.com first, without a README, so the push doesn't conflict.)
**Never commit your API key.** `.gitignore` already excludes `.streamlit/secrets.toml` and `.env`.

## Configuration

| Setting | Where | Default |
|---------|-------|---------|
| `GEMINI_API_KEY` | secrets / env / sidebar | – (required) |
| `GEMINI_MODEL` | secrets / env / sidebar | `gemini-3.7-flash` |
| `GEMINI_FALLBACK_MODELS` | secrets / env / sidebar | `gemini-3.6-flash,gemini-3.5-flash` |

Model names change over time. If you see *"Model not found"*, check the current list at
<https://ai.google.dev/gemini-api/docs/models> and update the model name in the sidebar or secrets.

## Troubleshooting

- **"503 UNAVAILABLE / high demand"** – Google's servers are overloaded (not a bug in the app). The app retries 3 times
  with backoff, then tries the fallback models automatically. If everything is busy, wait a minute and retry.
  Newer models are busiest; switching the main model to an older Flash model also helps.
- **"Almost no text could be extracted"** – the resume is a scanned image. Export a text-based PDF/DOCX.
- **"Gemini rejected the API key"** – re-check the key and that the Gemini API is enabled for it.
- **"Rate limit / quota reached"** – wait a minute, or check your quota in Google AI Studio.
- **Streamlit Cloud build fails** – confirm `requirements.txt` is in the repo root.

## Privacy

Resume text is sent to the Gemini API for analysis. The app does not store files or text on disk.
Be mindful of this if you deploy it publicly for other people's resumes.

## Possible next steps

OCR for scanned resumes, side-by-side comparison of multiple resumes, exporting an improved resume,
and rate limiting / login if you make the app public.

## License

MIT (or your choice).
