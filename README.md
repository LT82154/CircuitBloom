# CircuitBloom Project

Automated scrapers for CircuitBloom-managed categories on **sheeel.com**.

## Categories

### 1. Only On Sheeel
- **URL:** https://www.sheeel.com/ar/only-on-sheeel.html
- **Folder:** `only_on_sheeel/`
- **S3 Folder:** `only_on_sheeel/`
- **Type:** Hierarchical (with subcategories)
- **Optimization:** Async concurrent scraping (~3x faster)
- **Features:**
  - Concurrent subcategory scraping (3 at a time)
  - Incremental S3 image upload
  - Multi-sheet Excel output
  - Excel character sanitization for Arabic text

## Technical Stack

- **Scraping:** Playwright (async) with Chromium
- **Concurrency:** asyncio.Semaphore pattern
- **Data Processing:** pandas with Excel sanitization
- **Storage:** AWS S3 with date partitioning
- **Runtime:** Python 3.11, Ubuntu 22.04

## GitHub Actions Workflow

- **Schedule:** Every 2 days at 6:00 AM UTC
- **Manual Trigger:** Available via workflow_dispatch
- **Matrix Strategy:** Parallel execution of all categories
- **Artifact Backup:** 7-day retention

## Local Testing

```bash
cd CircuitBloom/only_on_sheeel

# Install dependencies
pip install -r requirements.txt
playwright install chromium

# Set environment variables
export AWS_ACCESS_KEY_ID="your_key"
export AWS_SECRET_ACCESS_KEY="your_secret"
export S3_BUCKET_NAME="your_bucket"

# Optional: Configure concurrency (default: 3)
export MAX_CONCURRENT_SUBCATEGORIES=3

# Run scraper
python scraper.py
```

## S3 Structure

```
s3://{bucket}/sheeel_data/
    └── year=YYYY/
        └── month=MM/
            └── day=DD/
                └── only_on_sheeel/
                    ├── images/
                    │   ├── {product_id}_0.jpg
                    │   ├── {product_id}_1.jpg
                    │   └── ...
                    └── excel-files/
                        └── only_on_sheeel_{timestamp}.xlsx
```

## Adding New Categories

To add a new category to CircuitBloom:

1. Create category folder: `CircuitBloom/{category_name}/`
2. Copy and modify scraper from `only_on_sheeel/scraper.py`
   - Update `base_url`
   - Update `category` name
   - Update class name
   - Update URL filter in `get_subcategories()`
3. Copy `requirements.txt`
4. Create category `README.md`
5. Create `.gitignore`
6. Update workflow matrix in `.github/workflows/main.yml`

## Performance

- **Concurrent Scraping:** ~2.5x faster than sequential
- **Incremental Upload:** ~30% faster than batch upload
- **Combined:** ~3x overall speedup
- **Configurable:** MAX_CONCURRENT_SUBCATEGORIES environment variable

## Required GitHub Secrets

```
AWS_ACCESS_KEY_ID       → AWS IAM access key
AWS_SECRET_ACCESS_KEY   → AWS IAM secret key
S3_BUCKET_NAME          → Target S3 bucket name
```