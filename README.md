# Vinted Listing Assistant

Local web application for preparing product photos, structured product data, and listing drafts. The application does not log in to Vinted, publish listings, send messages, scrape the platform, or automate user actions on Vinted.

The interface is currently in Polish.

[Polska wersja README](README.pl.md)

## Features

- Import up to 10 product photos.
- Preserve original files and create optimized publishing copies.
- Correct EXIF orientation, resize images, remove metadata, and apply conservative image enhancement.
- Store products, AI generations, and status history in a local SQLite database.
- Use two separate local models through an OpenAI-compatible API:
  - a vision-language model for photo analysis,
  - a text model for listing generation and language correction.
- Optionally use Azure OpenAI through the Responses API.
- Generate structured JSON, titles, descriptions, category suggestions, warnings, and optional price suggestions.
- Apply Polish-language cleanup rules to reduce typos, awkward diminutives, and unsupported claims.
- Export the final text and processed photos as a ZIP archive.

## Recommended local model setup

The application works with any compatible models, but the tested workflow is:

- Photo analysis: `InternVL3.5-14B Q6_K_M`
- Listing generation: `Qwen3.5-9B` or another instruction-tuned Qwen model

Configure LM Studio to load one model at a time. The application calls the vision model first and the listing model later, so both models do not need to fit in VRAM simultaneously.

## Requirements

- Windows 10 or Windows 11 for the included `.cmd` scripts
- Python 3.12
- An optional OpenAI-compatible API server, such as LM Studio
- Or an Azure OpenAI deployment that supports the Responses API and image input

The FastAPI application itself is platform-independent and can also be started manually on Linux or macOS.

## Windows installation

1. Install Python 3.12 and enable `Add Python to PATH`.
2. Clone or download this repository.
3. Run:

```text
Instaluj-Vinted-Assistant.cmd
```

4. Start the application:

```text
Uruchom-Vinted-Assistant.cmd
```

The launcher selects the first available local port starting at `8092` and opens the application in your browser.

## Manual installation

```bash
python -m venv .venv
```

Windows:

```powershell
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m uvicorn app.main:app --host 127.0.0.1 --port 8092
```

Linux or macOS:

```bash
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m uvicorn app.main:app --host 127.0.0.1 --port 8092
```

## Local OpenAI-compatible configuration

The default example address is:

```text
http://127.0.0.1:1234/v1
```

In `Ustawienia AI`, configure:

- API address
- optional API key
- photo-analysis model
- listing-generation model

Model identifiers must exactly match the identifiers returned by the server's `/v1/models` endpoint.

## Azure OpenAI configuration

In `Ustawienia AI`, select `Azure OpenAI` and provide:

- Azure endpoint, for example `https://your-resource.cognitiveservices.azure.com`
- API key
- deployment name
- API version, currently defaulted to `2025-04-01-preview`

Azure uses one deployment for photo analysis and listing generation. The current integration uses the Azure OpenAI Responses API.

Do not commit real keys to `.env`, source files, screenshots, issues, or logs.

## Data and privacy

Runtime data is stored locally:

```text
data/app.db
data/products/
data/exports/
logs/
```

These paths are excluded from Git. The application stores configured API keys in the local SQLite database in plain text. Treat the application directory as sensitive and restrict access to the local user account.

When using a local model server, data is sent to the configured local or network endpoint. When using Azure OpenAI, product data and the selected image are sent to the configured Azure resource.

## Safety and platform compliance

This project intentionally does not automate Vinted itself. It does not include browser automation, credential handling for Vinted, automatic publishing, automated messaging, scraping, or listing refresh bots.

Generated descriptions are suggestions. Review every title, description, category, detected brand, condition, defect, material, and price before publication. Image analysis cannot verify authenticity or hidden defects.

## Tests

Windows:

```text
Testuj-Vinted-Assistant.cmd
```

Manual:

```bash
python -m pytest -q
```

## Project structure

```text
app/
  main.py
  database.py
  config.py
  services/
  static/
  templates/
data/
logs/
tests/
```

## Known limitations

- The user interface is Polish-only.
- Photo analysis currently uses the first processed image.
- API keys are stored locally without encryption.
- The project has no user authentication and should not be exposed directly to the public internet.
- AI output can be incomplete or incorrect and requires manual review.
- Model availability, capabilities, and Azure API compatibility can change.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Security issues should be reported according to [SECURITY.md](SECURITY.md), not through a public issue.

## License

Licensed under the MIT License. See [LICENSE](LICENSE).

## Trademark notice

This independent project is not affiliated with, endorsed by, or sponsored by Vinted, Microsoft, OpenAI, Qwen, OpenGVLab, or LM Studio. Product and company names are trademarks of their respective owners.
