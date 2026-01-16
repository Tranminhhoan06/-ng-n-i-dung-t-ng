# Social Media Auto Uploader (TikTok, Facebook, YouTube, Threads, Instagram)

An automated tool to manage, schedule, and upload video content to **TikTok**, **Facebook Pages**, **YouTube**, **Threads**, and **Instagram**. It integrates with **Google Drive** for seamless cloud workflows and uses **Google Gemini AI** to generate viral captions and titles.

## 🌟 Features

### Multi-Platform Support
- **TikTok**: Upload videos using session ID (no Selenium required).
- **Facebook**: Upload videos and images to Facebook Pages.
- **YouTube**: Upload videos with Titles and Descriptions (resumable upload).
- **Threads**: Upload text, images, and videos with carousel support.
- **Instagram**: Upload images and Reels to Instagram Business/Creator accounts.

### 🚀 Parallel Upload (NEW!)
- **Multi-Account Posting**: Select multiple accounts/platforms and upload simultaneously.
- **Smart Processing**: Download, convert, and generate caption **once** for all platforms.
- **ThreadPoolExecutor**: Uploads run in parallel, not sequentially.
- **Example**: Upload to Facebook + Threads + YouTube at the same time!

### Smart Content Management
- **Video Title Support**: Specialized input for video titles (synced to YouTube/Facebook).
- **Enhanced AI Captions**: 
  - Uses **Gemini 2.0 Flash Exp** to analyze video content.
  - Generates **3 distinct caption options** for you to choose from.
  - Auto-extracts engaging titles.
- **Versatile Sources**:
  - **Google Drive Link**: Single video URL.
  - **Google Drive Folder**: Batch upload all files from a folder.
  - **Local File**: Direct upload from your computer.
- **Multi-Image Carousel**: Upload multiple images as Facebook Album or Threads Carousel.

### Advanced Workflow
- **Database Encryption (NEW!)**: Sensitive credentials (API keys, session IDs, tokens) are encrypted at rest using AES-256.
- **Server-Side Pagination & Filtering**: Large task lists are paginated on the server for performance. Filter tasks by status (All, Scheduled, Completed, Failed).
- **Batch Processing**: Select a Drive folder to queue multiple posts at once.
- **Auto-Conversion**: Optional "Convert to MP4 (H.264)" to ensure compatibility.
- **R2 Offload**: Large files (>30MB) automatically upload to Cloudflare R2 before posting.
- **Smart Scheduling**: Schedule posts for future publication.
- **Retry Mechanism**: One-click retry for failed tasks (auto re-downloads file).
- **LAN/Tailscale Access**: Diagnostic panel for network debugging.
- **Standardized Configuration**: Centralized environment variables in `backend/.env`.

## 🛠️ Tech Stack

- **Backend**: Python (FastAPI), SQLAlchemy (SQLite), APScheduler, ThreadPoolExecutor
- **Frontend**: React (Vite), TailwindCSS
- **AI**: Google Gemini API
- **Storage**: Cloudflare R2 (for large file offloading)
- **Integrations**: Google Drive API, YouTube Data API v3, Facebook Graph API, Threads API, Instagram Graph API

## 📋 Prerequisites

1.  **TikTok Session ID**:
    - Log in to [TikTok.com](https://www.tiktok.com).
    - Copy `sessionid` cookie from DevTools -> Application -> Cookies.
2.  **Facebook Access Token**:
    - Get a Page Access Token with `pages_manage_posts`, `pages_read_engagement`, `public_profile` permissions.
3.  **YouTube Credentials**:
    - `client_secret.json` for OAuth2 authentication.
4.  **Instagram Credentials**:
    - Instagram Business/Creator account connected to a Facebook Page.
    - Page Access Token with `instagram_basic`, `instagram_content_publish` permissions.
    - Instagram User ID (Business Account ID).
5.  **Gemini API Key**: From [Google AI Studio](https://aistudio.google.com/).
6.  **Google Drive**: Shared/Public links or authenticated access.

## 🚀 Installation

### Backend

1.  Create a `.env` file inside the `backend/` directory based on the configuration required (Database URL, R2, GDrive, and `ENCRYPTION_KEY`).
2.  Run the following commands:

```bash
cd backend
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn backend.main:app --host 0.0.0.0 --port 8001 --reload
```

> [!IMPORTANT]
> If you are setting up for the first time, ensure you generate an `ENCRYPTION_KEY` using `cryptography.fernet.Fernet.generate_key()`. Failing to provide this key will prevent the backend from starting.

## ⚙️ Environment Variables Configuration

### File Location
Create a `.env` file in the `backend/` directory: `backend/.env`

### Required Variables

```bash
# Database (PostgreSQL - recommended for production)
DATABASE_URL=postgresql://postgres:password@db:5432/kpn_social_manager

# Security
ENCRYPTION_KEY=your-fernet-encryption-key-here

# Google Drive OAuth (for private file access)
GDRIVE_CLIENT_ID=your-client-id.apps.googleusercontent.com
GDRIVE_CLIENT_SECRET=your-client-secret
GDRIVE_REDIRECT_URI=http://localhost:8001/api/auth/gdrive/callback
GDRIVE_API_KEY=your-google-api-key  # Optional: for public files

# Cloudflare R2 Storage (for large files)
R2_ENDPOINT=https://your-account-id.r2.cloudflarestorage.com
R2_ACCESS_KEY_ID=your-r2-access-key
R2_SECRET_ACCESS_KEY=your-r2-secret-key
R2_BUCKET_NAME=your-bucket-name
R2_PUBLIC_DOMAIN=https://your-r2-public-domain.com

# PostgreSQL Configuration (if using docker-compose)
POSTGRES_USER=postgres
POSTGRES_PASSWORD=password
POSTGRES_DB=kpn_social_manager
```

> [!NOTE]
> For development/testing, you can use SQLite instead: `DATABASE_URL=sqlite:///./social_manager.db`

### How to Obtain Credentials

#### 1. Encryption Key
Generate using Python:
```python
from cryptography.fernet import Fernet
print(Fernet.generate_key().decode())
```

#### 2. Google Drive OAuth Credentials
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one
3. Enable **Google Drive API**
4. Go to **APIs & Services** → **Credentials**
5. Click **Create Credentials** → **OAuth 2.0 Client ID**
6. Application type: **Web application**
7. Add authorized redirect URI: `http://localhost:8001/api/auth/gdrive/callback`
8. Copy **Client ID** and **Client Secret**

> [!NOTE]
> Use `GDRIVE_CLIENT_ID` and `GDRIVE_CLIENT_SECRET` (NOT `GOOGLE_CLIENT_ID`)

#### 3. Google Drive API Key (Optional)
1. In Google Cloud Console → **Credentials**
2. Click **Create Credentials** → **API Key**
3. Copy the API key to `GDRIVE_API_KEY`
4. Used as fallback for public files

#### 4. Cloudflare R2 Storage
1. Go to [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. Navigate to **R2** → **Create Bucket**
3. Go to **Manage R2 API Tokens**
4. Create a new API token with read/write permissions
5. Copy **Access Key ID** and **Secret Access Key**
6. Set public domain in bucket settings for `R2_PUBLIC_DOMAIN`

### Frontend

```bash
cd frontend
npm install
npm run dev -- --host 0.0.0.0 --port 5173
```

## 💡 Usage

1.  **Access Dashboard**: Open `http://localhost:5173`.
2.  **Manage Accounts**: Go to the "Manage Accounts" tab to add your TikTok, Facebook, and YouTube credentials.
3.  **Create Post**:
    - **Select Accounts**: Choose which platforms to post to.
    - **Select Source**: Paste a Drive Link, Folder URL, or upload a Local File.
    - **Media Type**: Video, Image, or Text (depending on platform support).
    - **Metadata**: 
        - Enter a **Video Title** (for YouTube/Facebook).
        - Click **Generate Caption** to let AI watch the video and suggest 3 options.
    - **Schedule**: Pick a date/time or leave blank to post immediately.
4.  **Monitor**: Watch progress in the task list. Edit pending tasks if needed.

## 📡 API Documentation

Base URL: `http://localhost:8001`

### 1. Task Management

#### List All Tasks
- **URL**: `/api/tasks`
- **Method**: `GET`
- **Response**: Array of Task objects.

#### Create a Task
- **URL**: `/api/tasks`
- **Method**: `POST`
- **Content-Type**: `application/json`
- **request Body**:
  ```json
  {
    "video_url": "https://drive.google.com/...", // Optional if local_path is set
    "local_path": "/absolute/path/to/file.mp4", // Optional
    "account_ids": ["acc_1", "acc_2"],          // Required: List of Account IDs
    "media_type": "video",                      // "video", "image", "text"
    "title": "My Viral Video",                  // Video Title (YouTube/Facebook)
    "caption": "Check this out! #viral",        // Caption/Message
    "schedule_time": 1735689600,                // Unix Timestamp. 0 = Immediate.
    "convert_to_mp4": false,                    // Auto-convert to H.264 MP4
    "tiktok_session_id": "...",                 // Optional override
    "gemini_api_key": "..."                     // Optional override
  }
  ```

#### Update a Task
- **URL**: `/api/tasks/{task_id}`
- **Method**: `PUT`
- **Description**: Updates task. For completed tasks, platform-specific restrictions apply (e.g., YouTube only allows Title/Caption edits).
- **Request Body**:
  ```json
  {
    "caption": "Updated caption...",
    "title": "Updated Title",
    "schedule_time": 1735689600,
    "video_url": "..."
  }
  ```

#### Retry a Failed Task
- **URL**: `/api/tasks/{task_id}/retry`
- **Method**: `POST`
- **Description**: Resets status to `PENDING` and re-queues execution.

#### Delete a Task
- **URL**: `/api/tasks/{task_id}`
- **Method**: `DELETE`
- **Query Params**: `delete_remote=true` (Attempt to delete post from platform, e.g., Facebook).

### 2. Account Management

#### List Accounts
- **URL**: `/api/accounts`
- **Method**: `GET`

#### Create Account
- **URL**: `/api/accounts`
- **Method**: `POST`
- **Request Body**:
  ```json
  {
    "platform": "tiktok", // "tiktok", "facebook", "youtube"
    "name": "My Account",
    "credentials": { ... } // Platform-specific JSON
  }
  ```

#### Update Account
- **URL**: `/api/accounts/{account_id}`
- **Method**: `PUT`

#### Delete Account
- **URL**: `/api/accounts/{account_id}`
- **Method**: `DELETE`

### 3. Utilities & AI

#### Generate Caption Preview
- **URL**: `/api/tasks/generate-caption`
- **Method**: `POST`
- **Request Body**:
  ```json
  {
    "video_url": "https://drive.google.com/...",
    "api_key": "YOUR_GEMINI_KEY",
    "convert_to_mp4": false
  }
  ```
- **Response**:
  ```json
  {
    "title": "Suggested Viral Title",
    "captions": [
       "Option 1: ...",
       "Option 2: ...",
       "Option 3: ..."
    ]
  }
  ```

#### List Drive Folder
- **URL**: `/api/gdrive/list`
- **Method**: `POST`
- **Body**: `{ "url": "https://drive.google.com/drive/folders/..." }`

#### Upload Local File
- **URL**: `/api/upload/local`
- **Method**: `POST`
- **Body**: `Multipart Form Data` (file field).
- **Response**: `{ "local_path": "/absolute/path/..." }`

## ⚠️ Disclaimer

This tool uses unofficial APIs for TikTok. Use responsibly and avoid spamming. We recommend using test accounts for automation.
