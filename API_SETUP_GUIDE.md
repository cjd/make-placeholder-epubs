# API Keys Setup for Book Scanner

## The Network Error

You're getting "network error" because the app needs API keys to fetch book metadata.

## Required API Keys

### 1. Hardcover API Token (Required)

**Get your token:**
1. Go to https://hardcover.app
2. Create account / Sign in
3. Go to **Account Settings**
4. Click on **Hardcover API**
5. Copy your API token

### 2. Gemini API Key (Optional - for cover scanning)

**Get your key:**
1. Go to https://aistudio.google.com/
2. Sign in with Google account
3. Click **Get API key**
4. Copy your API key

## Setup

Create a `.env` file in the same directory as your `docker-compose.yml`:

```bash
# Create the file
touch .env

# Edit it
nano .env
```

Add this content:

```env
HARDCOVER_BEARER_TOKEN=your_hardcover_token_here
GEMINI_API_KEY=your_gemini_key_here
DEBUG_LOG_FILE=debug.log
EPUB_DIR=epubs/
ISBN_LIST_FILE=processed_isbns.txt
```

Replace `your_hardcover_token_here` and `your_gemini_key_here` with your actual keys.

## Restart the Container

After creating the `.env` file:

```bash
docker-compose down
docker-compose up -d
```

## Test

1. Scan an ISBN barcode
2. Should now fetch book metadata successfully
3. Generate EPUB file

## If Still Getting Network Error

Check the logs:

```bash
docker-compose logs -f
```

Or check if environment variables are set:

```bash
docker exec your-container-name env | grep HARDCOVER
docker exec your-container-name env | grep GEMINI
```

## Alternative: Set in docker-compose.yml

If you don't want to use a .env file, you can set them directly in `docker-compose.yml`:

```yaml
services:
  placeholder-epubs:
    environment:
      - HARDCOVER_BEARER_TOKEN=your_token_here
      - GEMINI_API_KEY=your_key_here
```

Then restart:
```bash
docker-compose down
docker-compose up -d
```
