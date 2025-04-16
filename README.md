# YouTube Access Token Generator and Anime YouTube Content Farm Automation

This tool helps you generate and save access tokens for multiple YouTube accounts using Google OAuth 2.0 client IDs, and automates the process of uploading anime-themed short videos from Google Drive to multiple YouTube channels, with tracking and notifications.

## How It Works (Token Generation)

1. The script reads your Gmail client ID JSON files
2. It authenticates each YouTube account (2 per Gmail account)
3. Saves access tokens in a `tokens` folder with the YouTube channel name and identifier

## Handling OAuth Verification Errors

If you see the error "Access blocked: youtube upload has not completed the Google verification process", follow these steps:

1. **Add yourself as a test user in Google Cloud Console**:
   - Go to [Google Cloud Console](https://console.cloud.google.com/)
   - Navigate to your project
   - Go to "APIs & Services" > "OAuth consent screen"
   - Scroll down to "Test users" section
   - Click "Add users" and add the email address you're using for YouTube
   - Repeat for all projects (gmail1-gmail5)

2. **Authentication process**:
   - When prompted with "Google hasn't verified this app" warning
   - Click "Advanced" link at the bottom
   - Click "Go to [Project Name] (unsafe)"
   - This is normal for apps in development/testing mode

## How to Use (Token Generation)

1. Install requirements:
   ```
   pip install -r requirements.txt
   ```

2. Run the generator script:
   ```
   python generate_tokens.py
   ```

3. For each YouTube account:
   - A browser window will open
   - The script provides detailed instructions for handling verification warnings
   - When prompted, authorize the application and copy the authorization code
   - Paste the code back into the terminal when prompted
   - After each authorization, you'll be asked to switch YouTube accounts

4. All tokens are saved in the `tokens` folder with descriptive filenames:

## Accessing the Tokens Later

To use these tokens in your upload scripts later, you can load them using:

```python
import pickle
import os
from google.auth.transport.requests import Request
from googleapiclient.discovery import build

def get_youtube_service(token_file):
    with open(token_file, 'rb') as token:
        creds = pickle.load(token)
        
    if creds.expired and creds.refresh_token:
        creds.refresh(Request())
        
    return build('youtube', 'v3', credentials=creds)

# Example usage
youtube = get_youtube_service('tokens/CHANNEL_NAME.pickle')
```

This allows you to upload videos without re-authorization.

## Anime YouTube Content Farm Automation

This tool automates the process of uploading anime-themed short videos from Google Drive to multiple YouTube channels, with tracking and notifications.

### Features

- Randomly selects videos from Google Drive "Anime_Short" folder
- Uploads videos to 10 different anime-themed YouTube channels
- Tracks all uploads in a Google Spreadsheet
- Sends Telegram notifications after each upload
- GitHub Actions workflow for scheduled daily uploads
- Handles YouTube token management and refresh

### Anime Channels Managed

1. AnimeClash
2. AniSnap
3. KawaiiCuts
4. OtakuPulse
5. SenpaiSnaps
6. ShinobiSnips
7. ShortsCrazy
8. UltraInstinctEdits
9. VoidNoJutsu
10. ZenkaiZone

### How It Works

1. The script connects to Google Drive using OAuth credentials
2. Randomly selects videos from a specified folder
3. Downloads video to local temp directory
4. Uploads to specified YouTube channel (or random if not specified)
5. Records upload details in tracking spreadsheet
6. Sends Telegram notification with upload status
7. Automatically runs via GitHub Actions workflow

### Requirements

- Python 3.6+
- Required Python packages (install using `pip install -r requirements.txt`):
  - google-api-python-client
  - google-auth-httplib2
  - google-auth-oauthlib
  - requests
  - pickle-mixin

### Usage

#### Manual Upload

```bash
# Upload 1 random video to a specific channel
python upload_gdrive_videos.py --channel-name "AnimeClash" --limit 1

# List available channels
python upload_gdrive_videos.py --list-channels

# Upload multiple videos
python upload_gdrive_videos.py --channel-name "ShinobiSnips" --limit 5
```

#### Automated Uploads via GitHub Actions

The included GitHub workflow `.github/workflows/upload_youtube_videos.yml` handles:
- Daily scheduled uploads at 2PM UTC
- Sequential processing of all 10 channels
- Randomly uploads 7-10 videos per channel
- Retries on failure and continues to next channel if limits are reached

### Token Management

This project includes `generate_tokens.py` for managing YouTube OAuth tokens:

1. Reads Gmail client ID JSON files
2. Authenticates YouTube accounts
3. Saves access tokens for use by the upload script

See the token generator section above for more details.
