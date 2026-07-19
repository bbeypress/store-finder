# Store Finder

Finds newly-created Shopify stores using public data only (certificate
transparency logs + each store's own public storefront/contact pages),
optionally finds a public contact email, and drafts a personalized
outreach note.

## Data sources (all public, no ToS from any single platform involved)
- **crt.sh** — public certificate transparency logs. Catches new
  `*.myshopify.com` stores fast, often before a custom domain exists.
- **`<store>.myshopify.com/products.json`** — Shopify's own intentional
  public storefront API.
- **The store's own public pages** — only reads a contact email the
  store owner already displays publicly for people to reach them.

## Run it locally
```bash
pip install -r requirements.txt

# Basic search
python store_finder.py --keyword pet --limit 20

# Find contacts too
python store_finder.py --keyword pet --limit 20 --find-contact

# Full pipeline: find stores, verify live, find contact, draft outreach
python store_finder.py --keyword pet --limit 20 --draft-outreach \
    --your-name "Your Name" \
    --your-offer "a one-line description of what you're offering"

# Save everything to a file
python store_finder.py --limit 50 --draft-outreach --json results.json
```

## Run it automatically via GitHub Actions
This repo includes `.github/workflows/find-stores.yml`, which:
- Runs automatically every day at 09:00 UTC
- Can also be triggered manually anytime from the **Actions** tab → **Find New Shopify Stores** → **Run workflow**, where you can set your own keyword/limit for that run
- Saves each day's results as `results/YYYY-MM-DD.json`, committed straight into the repo, plus as a downloadable workflow artifact

### One-time setup after pushing this to GitHub
1. Go to your repo → **Settings** → **Actions** → **General** → under "Workflow permissions," select **Read and write permissions** (needed so the workflow can commit results back to the repo).
2. Optional: go to **Settings** → **Secrets and variables** → **Actions** → **Variables** tab, and add:
   - `YOUR_NAME` — your name, used in drafted outreach notes
   - `YOUR_OFFER` — one-line description of what you're offering
   
   If you skip this, it falls back to generic defaults ("Alex" / "a tool that helps small store owners") — you'll want to set these.
3. That's it — the schedule will start running automatically. Check the **Actions** tab to see run history, or the `results/` folder in your repo for the JSON output.

### Adjusting the schedule
Edit the `cron` line in `.github/workflows/find-stores.yml`. Cron times are always UTC. For example, `"0 14 * * *"` runs daily at 14:00 UTC.

## A note on outreach
The contact emails this finds are ones store owners chose to display
publicly. If you email them, be upfront about who you are and make it
easy to say no — the draft template already does both, but worth
keeping in mind if you customize it further.
