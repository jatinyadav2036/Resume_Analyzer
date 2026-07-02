# Resume Anomaly Analyzer

A Streamlit web app that uses Google's Gemini LLM to scan resumes (PDF/DOCX) for common recruiter red flags — grammar issues, vague filler language, employment gaps, skill mismatches, and more — and turns the findings into a visual dashboard and downloadable PDF report.

## Features

- **Document parsing** — accepts PDF and DOCX resumes and extracts raw text
- **13-point anomaly analysis** via Gemini, covering:
  - Grammar/spelling mistakes
  - Filler or vague phrases
  - Repeated/copy-pasted bullet points
  - Missing contact information
  - Missing key sections (experience, education, projects, certifications)
  - Unexplained employment gaps
  - Frequent job switching (tenure < 8 months)
  - Experience/skills mismatch
  - Outdated technologies
  - Lack of measurable achievements
  - Education/experience mismatch
  - Irrelevant work experience
  - Role-skill mismatch
- **Severity scoring** (1–10) for every failed check, with only significant issues (severity ≥ 4) counted against the overall score
- **Resume strengths extraction** — top skills and standout ("wow factor") achievements
- **Interactive dashboard**:
  - Overall resume health gauge
  - Category radar chart (Content, Format, Consistency, Relevance, Credibility)
  - Severity breakdown chart
  - Recruiter insights (critical issues, red flags, interview questions, strengths)
- **Downloadable PDF report** summarizing the full analysis

## Project Structure

```
.
├── app.py               # Streamlit UI and main application flow
├── analysis.py           # Gemini prompt, scoring logic, insight generation
├── parsers.py             # PDF/DOCX text extraction
├── visualization.py        # Plotly charts (gauge, radar, severity breakdown)
├── pdf_generator.py         # ReportLab PDF report builder
└── requirements.txt          # Python dependencies
```

## Requirements

- Python 3.9+
- A Google Gemini API key ([Google AI Studio](https://aistudio.google.com/app/apikey))

## Installation

1. **Clone/download the project** and navigate into the folder.

2. **Create a virtual environment** (recommended):
   ```bash
   python -m venv venv
   source venv/bin/activate   # Windows: venv\Scripts\activate
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

## Configuration

The app needs a Gemini API key. Create a `.env` file in the project root:

```
GEMINI_API_KEY="your-api-key-here"
```

> **Security note:** The current version of `app.py` has an API key hardcoded directly in the source (`genai.configure(api_key="...")`) instead of loading it from the environment. Before deploying or sharing this repo, update that line to:
> ```python
> from dotenv import load_dotenv
> load_dotenv()
> genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
> ```
> and install `python-dotenv` (add it to `requirements.txt`). Also **rotate any key that has previously been committed or shared in plaintext** — treat it as compromised.

## Running the App

```bash
streamlit run app.py
```

The app will open in your browser at `http://localhost:8501`.

## Usage

1. Upload a resume in **PDF** or **DOCX** format.
2. Review the extracted text to confirm parsing worked correctly.
3. Click **Analyze Resume**.
4. Explore the dashboard:
   - Health score gauge and category radar chart
   - Key insights (critical issues, red flags, strengths, suggested interview questions)
   - Full breakdown of all 13 checks, filterable by "Failed Checks Only" or "All Checks"
5. Click **Download Detailed Report (PDF)** to save a shareable summary.

## How Scoring Works

- Each failed check has a severity from 1–10; checks with severity < 4 are treated as passed (minor/non-issues) and excluded from the score.
- **Overall score** = `100 - (sum of significant severities × 0.77)`, clamped to 0–100.
- **Category scores** are calculated per category (Content, Format, Consistency, Relevance, Credibility) based on pass rate and severity-weighted deductions.

## Tech Stack

| Component | Library |
|---|---|
| Web UI | [Streamlit](https://streamlit.io/) |
| LLM analysis | [google-generativeai](https://ai.google.dev/) (Gemini 1.5 Flash) |
| PDF parsing | PyPDF2 |
| DOCX parsing | python-docx |
| Charts | Plotly |
| PDF report generation | ReportLab, Matplotlib |

## Known Limitations

- Analysis quality depends on the LLM's JSON output being well-formed; malformed responses fall back to raw text display.
- Text extraction from scanned/image-based PDFs will not work (no OCR).
- Currently only supports single-resume analysis per run (no batch mode).

## License

Add your preferred license here (e.g., MIT).