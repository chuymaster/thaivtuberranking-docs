# API Documentation for Thai VTuber Channel Ranking Data

This repository hosts API documentation for Thai VTuber channel ranking data. The data is used in [Thai VTuber Ranking](https://vtuber.chuysan.com/) site which I operate.

## Swagger API Documentation

See the API specification at https://chuymaster.github.io/thaivtuberranking-docs/

Do note that the specification does not represent all interfaces perfectly. Please verify the API by yourself.

## Architecture Overview

### Public Data API (Storage JSON)
All public data is served as static JSON files from Google Cloud Storage. No authentication required.

**Base URL:** `https://storage.googleapis.com/thaivtuberranking.appspot.com/`

| Endpoint | Description | Update Frequency |
|----------|-------------|-----------------|
| `v2/channel_data/list.json` | Full channel data list | Every 12 hours |
| `v2/channel_data/simple_list.json` | Simplified channel list | Every 12 hours |
| `v2/channel_data/detail/{id}.json` | Individual channel detail | Every 12 hours |
| `v2/channel_data/chart_data/{id}.json` | Channel history chart data | Daily |
| `v2/channel_data/one_day_ranking.json` | Top videos (1 day) | Every 12 hours |
| `v2/channel_data/three_days_ranking.json` | Top videos (3 days) | Every 12 hours |
| `v2/channel_data/seven_days_ranking.json` | Top videos (7 days) | Every 12 hours |
| `v2/channel_data/live_videos.json` | Currently live videos | Every hour |
| `v2/channel_data/upcoming_videos.json` | Upcoming live videos | Every hour |

### Backend Services

#### Firebase Functions (API Endpoints)
Handles user-facing API requests:
- `postChannelRequest` — Submit new channel registration request
- `getChannelList` — Get registered channel list
- Channel management APIs (authenticated)

#### GitHub Actions (Scheduled Jobs)
All data crawling and processing runs as GitHub Actions cron jobs:

| Job | Schedule | Description |
|-----|----------|-------------|
| Get YouTube Data | Every 12 hours | Crawl channel statistics from YouTube API |
| Get YouTube Videos Info | Every 12 hours | Fetch video details |
| Update Live Videos Info | Every hour | Update live/upcoming video status |
| Generate Video Ranking | Daily | Generate video ranking JSONs |
| Generate Chart Data | Daily at 21:30 UTC | Generate channel history chart data |
| Delete Obsolete Channels | Every 5 days | Clean up inactive channels |
| Approve/Deny Channel | Manual (workflow_dispatch) | Process channel registration requests |

#### Data Stores
- **Firestore** — Channel list, channel info, channel requests
- **BigQuery** — Channel history (daily statistics, used for chart data)
- **Cloud Storage** — Public JSON files served to frontend

### Channel Request Flow
1. User submits channel request via website → `postChannelRequest` API
2. Webhook notification sent to Discord
3. Admin reviews and approves/denies via GitHub Actions workflow or Discord

## API Usage Guideline

- Please do not abuse it. All requests and responses cost money.
- Please contact me [@chuymaster](https://twitter.com/chuymaster) if you want to use the API publicly
- Please create an issue if you find something you can improve.
- Feature requests are also welcome. We may be able to develop together!

## Editor

Use https://editor.swagger.io/ to edit `swagger.yaml`.

## Remarks

Thanks https://github.com/peter-evans/swagger-github-pages for providing a template for using Swagger UI easily.
