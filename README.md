# Supabase ingestion helper

This directory contains an Edge Function you can deploy to Supabase so the web annotator can stream JSON submissions directly into a Storage bucket. It keeps the service role credentials server-side and gives you a single HTTPS endpoint to place in `web_annotator/clip-config.js`.

## Deploying the function

1. Install the Supabase CLI and authenticate with your account.
2. Copy `annotator-ingest.ts` into your Supabase project (e.g., `supabase/functions/annotator-ingest/index.ts`).
3. Run `supabase functions deploy annotator-ingest --project-ref <PROJECT_REF>`.
4. Configure the function environment variables:
   - `SUPABASE_URL`: Project URL (`https://your-project.supabase.co`).
   - `SUPABASE_SERVICE_ROLE_KEY`: Service role key (store securely; never expose to clients).
   - `SUPABASE_STORAGE_BUCKET`: Name of the bucket where JSON files should land.
   - `ANNOTATOR_SHARED_SECRET`: Shared secret presented in the `Authorization` header from the annotator.
   - `ANNOTATOR_ALLOW_ORIGIN`: URL that hosts your annotator (for CORS).
5. In `web_annotator/clip-config.js`, set `window.ANNOTATION_SUBMISSION.endpoint` to the deployed function URL (e.g., `https://your-project.functions.supabase.co/annotator-ingest`) and add `Authorization: "Bearer <shared secret>"` to the headers.

Each annotation will upload as `participant-id/timestamp_filename.json` in your Storage bucket. Adjust the Edge Function if you prefer to insert rows into Postgres or trigger additional workflows.
