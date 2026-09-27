# ComicCraft — AI Comic Story Creator

ComicCraft is a FastAPI + Jinja2 web app that turns a story idea into a multi-panel comic. The pipeline follows the project documentation: structured outline → detailed narration/dialogue → image generation → layout → PDF export.

## Architecture

- **Frontend:** HTML, CSS, Jinja2
- **Backend:** FastAPI + Uvicorn
- **Story AI:** Google Gemini via the current `google-genai` SDK and structured Pydantic output
- **Image AI:** Hugging Face `InferenceClient` with a configurable diffusion model/provider
- **Export:** FPDF2 + Pillow
- **API:** `/generate-comic/json`, `/test-image`, `/health`, plus the browser form at `/generate`

## Windows setup

```powershell
cd C:\path\to\ComicCraft
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
```

If PowerShell blocks activation, use:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

## API keys

Open `.env` and set:

```text
GEMINI_API_KEY=your_key
HF_TOKEN=your_token
```

You can first run with both blank because `ALLOW_DEMO_MODE=true` creates deterministic placeholder panels. Once keys are added, the same UI automatically uses the remote AI services.

## Run

```powershell
uvicorn app.main:app --reload
```

Open http://127.0.0.1:8000

API docs: http://127.0.0.1:8000/docs
Health: http://127.0.0.1:8000/health

## JSON API example

POST `/generate-comic/json`:

```json
{
  "story_prompt": "A brave fox explores an enchanted forest and discovers a forgotten clock tower.",
  "character_name": "Aria",
  "setting": "Enchanted Forest",
  "tone": "Mysterious",
  "art_style": "Comic Book",
  "panels": 5
}
```

## Testing

```powershell
pytest -q
```

The included tests verify the health endpoint, demo-mode comic pipeline, PDF creation, and image output. Real API calls are not required for tests.

## Notes

The original documentation specifies Gemini Flash + Gemini Pro and Stable Diffusion. The project keeps those logical stages, but makes the model IDs configurable because provider/model availability changes over time. The default image backend uses Hugging Face Inference Providers with a current diffusion model. You can change `IMAGE_MODEL` to another compatible diffusion model without changing application code.
