# Twitter Tweet Scraper API examples for SocQ

[![Twitter Tweet Scraper API](https://img.shields.io/badge/API-Twitter%20Tweet%20Scraper%20API-F64C31)](https://socq.ai/apis/x?utm_source=github&utm_medium=repository&utm_campaign=twitter-tweet-scraper-api)
[![API documentation](https://img.shields.io/badge/Docs-docs.socq.ai-111827)](https://docs.socq.ai)
[![License: MIT](https://img.shields.io/badge/License-MIT-2563EB)](LICENSE)
[![Check examples](https://github.com/SocQAPI/twitter-tweet-scraper-api/actions/workflows/check.yml/badge.svg)](https://github.com/SocQAPI/twitter-tweet-scraper-api/actions/workflows/check.yml)

Turn known public x.com or twitter.com status URLs into structured post records with full text, author context, engagement counts, media, entities, and conversation relationships.

[Try Twitter Tweet Scraper API](https://socq.ai/apis/x?utm_source=github&utm_medium=repository&utm_campaign=twitter-tweet-scraper-api)
· [Get an API key](https://socq.ai/dashboard/api-key?utm_source=github&utm_medium=repository&utm_campaign=twitter-tweet-scraper-api)
· [Documentation](https://docs.socq.ai)
· [All SocQ examples](https://github.com/SocQAPI/socq-examples)

## Use cases

- **Complete a post-link catalog:** Replace bare X links with searchable text, author identity, media references, entities, publication time, and stable post IDs.
- **Document campaign posts:** Capture the exact content, author, media, relationship context, and collection-time engagement for an approved set of campaign URLs.
- **Compare selected post snapshots:** Resolve the same post IDs at defined intervals and compare likes, replies, reposts, quotes, views, and bookmarks by collected_at.
- **Preserve conversation context:** Retain available reply, quote, repost, and parent identifiers so selected posts remain connected to their public discussion structure.

## API behavior

- Submit one or more public `x.com` or `twitter.com` URLs containing a numeric post ID in the `/status/` path.
- Normalize and deduplicate accepted URLs by post ID rather than by the full URL string.
- Author, media, entity, and conversation-relation fields are optional and should be handled defensively.
- Engagement values are point-in-time observations captured at `collected_at`.

All requests use the shared asynchronous flow:

```text
submit -> task_id -> poll task -> read every cursor page -> save results
```

## Quick start

```bash
cp .env.example .env
export SOCQ_API_KEY="your-api-key"
```

Run the complete Node.js workflow:

```bash
cd node
npm start
```

Run the complete Python workflow:

```bash
python3 -m pip install -r python/requirements.txt
python3 python/main.py
```

Both examples load `payload.example.json`, retry transient API responses, wait
for task completion, read every cursor page, and save an enriched public X post-link dataset with conversation and engagement context to
`output/results.json`.

Never expose `SOCQ_API_KEY` in browser code, mobile apps, public repositories,
screenshots, fixtures, or logs.

## Request

```http
POST https://api.socq.ai/v1/x/posts
Authorization: Bearer <SOCQ_API_KEY>
Content-Type: application/json
```

```json
{
  "urls": [
    "https://x.com/example/status/1234567890123456789"
  ]
}
```

The submit response contains `data.task_id`. Poll the task endpoint until
`data.status` becomes `succeeded` or `failed`, then continue with
`data.results.next_cursor` while `data.results.has_more` is `true`.

## Complete workflow example

The Node.js and Python programs implement the production-shaped happy path:

1. Load and validate configuration.
2. Submit the endpoint-specific payload.
3. Retry rate-pressure and transient server responses with bounded backoff.
4. Poll the asynchronous task with a ten-minute application timeout.
5. Stop cleanly on a failed task and surface the public error message.
6. Read all cursor pages instead of silently returning only the first page.
7. Write a stable JSON artifact containing task metadata and normalized records.

Use the synthetic files in `fixtures/` for tests and documentation. They do
not contain customer, account, or production data.

## Production notes

See [`docs/production-notes.md`](docs/production-notes.md) for validation,
retry, timeout, pagination, deduplication, logging, and endpoint-specific
guidance.

## Responsible use and platform scope

- Use only publicly accessible X profiles, posts, account timelines, search results, and fields supported by the selected endpoint.
- Do not use the examples to access private accounts, restricted content, deleted posts, login-only surfaces, or authentication controls.
- SocQ is not an official API of the represented social platform and is not affiliated with or endorsed by that platform.
- Before production use, assess the laws, platform terms, privacy obligations, and retention requirements that apply to your organization and use case.
- Collect only the fields needed for a defined purpose, restrict access, set retention periods, and support correction or deletion workflows where required.
- Platform names and trademarks belong to their respective owners.

This section describes the public-data boundary; it is not legal advice or a
guarantee that every use case is permitted in every jurisdiction.

## Repository contents

| Path | Purpose |
| --- | --- |
| [`curl/request.md`](curl/request.md) | Copy-paste submit, poll, and pagination requests |
| [`node/index.mjs`](node/index.mjs) | Complete Node.js workflow |
| [`python/main.py`](python/main.py) | Complete Python workflow |
| [`payload.example.json`](payload.example.json) | Safe endpoint-specific request body |
| [`fixtures/`](fixtures) | Synthetic submit and task response shapes |
| [`docs/production-notes.md`](docs/production-notes.md) | Production integration guidance |
