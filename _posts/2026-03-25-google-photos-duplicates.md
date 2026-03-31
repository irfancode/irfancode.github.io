---
title: "Finding Duplicate Photos with Google Photos API"
date: 2025-07-12
category: "Python"
tags: ["Python", "Google Photos API", "Automation", "API", "Duplicate Detection"]
---

Like most people, I have thousands of photos spread across Google Photos. And like most people, I have duplicates. Lots of them. Screenshots, multiple uploads, WhatsApp backups—the mess is real. So I built a Python script to solve it: [google-photos-duplicate-finder](https://github.com/irfancode/google-photos-duplicate-finder).

## The Problem

Google Photos is great, but:
- Multiple devices = multiple uploads
- Screenshot accumulation
- WhatsApp/WhatsApp Business duplicates
- No built-in duplicate detection
- Manual deletion is painful

## The Solution

A Python script that:
1. Connects to Google Photos API
2. Identifies duplicates using multiple methods
3. Provides detailed reports
4. Helps you clean up safely

## Installation

```bash
git clone https://github.com/irfancode/google-photos-duplicate-finder
cd google-photos-duplicate-finder
pip install -r requirements.txt
```

## Setup

### 1. Google Cloud Console

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project
3. Enable "Photos Library API"
4. Create OAuth 2.0 credentials
5. Download `credentials.json`

### 2. First Run

```python
python main.py --auth
```

This opens browser for OAuth authentication.

## Detection Methods

### Method 1: Filename Matching

```python
# Find files with same name
matches = find_by_filename(media_items)
```

Good for: Quick scans, obvious duplicates

### Method 2: Metadata Comparison

```python
# Compare creation time, device info
matches = find_by_metadata(media_items)
```

Good for: Same photo from different devices

### Method 3: Content Hashing

```python
# Download and hash image content
matches = find_by_hash(media_items)
```

Good for: Exact duplicates, compressed copies

## Usage

### Quick Scan (Filenames Only)

```bash
python main.py --method filename --report
```

### Deep Scan (Content Hash)

```bash
python main.py --method hash --report --download
```

### Interactive Review

```bash
python main.py --interactive
```

## Code Walkthrough

### Authentication

```python
from google_photos import GooglePhotos

photos = GooglePhotos('credentials.json')
photos.authenticate()

# Get all media items
media_items = photos.list_media()
```

### Duplicate Detection

```python
def find_duplicates(media_items, method='hash'):
    if method == 'filename':
        return find_by_filename(media_items)
    elif method == 'metadata':
        return find_by_metadata(media_items)
    elif method == 'hash':
        return find_by_hash(media_items)
```

### Hash Comparison

```python
import hashlib

def compute_hash(media_item):
    # Download image
    content = download_image(media_item['baseUrl'])
    # Compute SHA256
    return hashlib.sha256(content).hexdigest()
```

## Results

Typical scan findings:
- 500-1000 duplicates in average library
- 20-30% storage savings
- Most common: Screenshots, WhatsApp images

## Safety Features

1. **Read-Only Mode** — Scan without deleting
2. **Reports First** — Review before acting
3. **Batch Processing** — Delete in chunks
4. **Trash, Not Delete** — Items go to trash first

## Future Enhancements

- [ ] Face detection for grouping
- [ ] AI-powered similarity detection
- [ ] Cloud function deployment
- [ ] Mobile app companion

## Conclusion

Google Photos is an excellent service, but it lacks native duplicate management. With [google-photos-duplicate-finder](https://github.com/irfancode/google-photos-duplicate-finder), you can reclaim storage and organize your memories.

The best part? It's open source, so the community can improve it together.

Questions? Let's connect!

---

**Connect**: [LinkedIn](https://linkedin.com/in/sirfan98cs) | [GitHub](https://github.com/irfancode)
