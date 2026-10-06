# Bulk PDF Link & Image Replacer — Vercel

This project converts the original Gradio/Colab application into a Vercel-ready web app.

## Architecture

Browser → Vercel Blob direct upload → `/api/process` Python serverless function → temporary filesystem → PyMuPDF processing → ZIP → Vercel Blob → browser download.

The browser uploads PDFs and the replacement image directly to Blob, so the main processing request contains only small JSON metadata rather than the PDF bytes.

## Deploy

1. Create a new GitHub repository and copy this project into it.
2. Import the repository into Vercel.
3. In Vercel, create/connect a **Blob** store for the project. This provides `BLOB_READ_WRITE_TOKEN`.
4. Deploy.
5. Open `/api/health` to confirm the Python function is running.

## Local development

Install Node.js and Python 3.12+, then:

```bash
npm install
npx vercel dev
```

A local Blob token/store is required for real uploads and output ZIP storage.

## Important Vercel constraints

- This is designed for moderate-size batches, not unlimited bulk processing.
- The UI limits a run to 100 PDFs.
- The upload-token endpoint currently allows 50 MB per individual uploaded object.
- Serverless execution time and memory depend on the Vercel plan. Very large PDFs or hundreds of large files should be processed by a dedicated worker/server instead.
- Output ZIP files are uploaded to Blob so they do not need to be returned as a large serverless response.
- Because the current result upload uses public Blob URLs, do not use this version for confidential/sensitive documents without changing storage to a private/authenticated design.

## Files

- `public/index.html` — frontend UI
- `public/style.css` — responsive styling
- `public/script.js` — Blob uploads + API orchestration
- `api/process.py` — PyMuPDF/Pillow processing API
- `api/upload-token.js` — Vercel Blob browser upload token
- `api/health.py` — health endpoint
- `vercel.json` — Vercel routing/runtime config
- `requirements.txt` — Python dependencies
- `original_colab_code.md` — original uploaded source preserved for reference
