---
title: Getting Started
excerpt: >-
  Get started with the FIRS E-Invoice API. Learn about base URLs, API
  versioning, response formats, and rate limits.
---

# Getting Started

## Base URL

```
Production: https://api.firs-einvoice.com
Sandbox:     https://sandbox.firs-einvoice.com
```

## API Version

All endpoints are prefixed with `/api/v1` (or just `/api` for current version).

## Response Format

All responses follow a consistent structure:

```json
{
  "success": true,
  "data": {
    // Response data here
  },
  "message": "Optional success message"
}
```

Error responses:

```json
{
  "success": false,
  "error": "Error message",
  "code": "ERROR_CODE"
}
```

## Rate Limits

- **JWT Authentication**: 100 requests per minute
- **API Key Authentication**: Custom limits per key (default: 1000 requests per minute)
- **Authentication Endpoints**: 10 requests per minute

