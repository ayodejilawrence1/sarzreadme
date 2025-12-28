---
title: Authentication
excerpt: 'The API supports two authentication methods: JWT Bearer Tokens and API Keys.'
---

# Authentication

The API supports two authentication methods:

1. **JWT Bearer Tokens** - For user-based access
2. **API Keys** - For programmatic/integration access

## Register

Create a new user account.

<details>
<summary><code>POST</code> <code><b>/api/auth/register</b></code></summary>

**Request Body:**

```json
{
  "username": "your-username",
  "password": "secure-password",
  "email": "user@example.com"
}
```

**Response:** `201 Created`

```json
{
  "success": true,
  "data": {
    "id": "user-uuid",
    "username": "your-username",
    "email": "user@example.com",
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

**cURL Example:**

```bash
curl -X POST https://api.firs-einvoice.com/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "your-username",
    "password": "secure-password",
    "email": "user@example.com"
  }'
```

</details>

## Login

Authenticate and receive access tokens.

<details>
<summary><code>POST</code> <code><b>/api/auth/login</b></code></summary>

**Request Body:**

```json
{
  "username": "your-username",
  "password": "your-password"
}
```

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "user-uuid",
      "username": "your-username",
      "email": "user@example.com",
      "role": "USER"
    }
  }
}
```

**cURL Example:**

```bash
curl -X POST https://api.firs-einvoice.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "your-username",
    "password": "your-password"
  }'
```

> **💡 Tip:** Store the `accessToken` securely. It expires after 1 hour. Use `refreshToken` to get a new access token.

</details>

## Refresh Token

Get a new access token using your refresh token.

<details>
<summary><code>POST</code> <code><b>/api/auth/refresh</b></code></summary>

**Request Body:**

```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

**cURL Example:**

```bash
curl -X POST https://api.firs-einvoice.com/api/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{
    "refreshToken": "your-refresh-token"
  }'
```

</details>

## Using Authentication

Include your token in the `Authorization` header:

```bash
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Or use an API key:

```bash
X-API-Key: your-api-key-here
```

