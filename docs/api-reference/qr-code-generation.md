---
title: QR Code Generation
excerpt: >-
  Generate FIRS-compliant QR codes for your invoices. QR codes are encrypted
  using RSA encryption.
---

# QR Code Generation

Generate FIRS-compliant QR codes for your invoices. QR codes are encrypted using RSA encryption and contain all required invoice information.

## Generate QR Code

<details>
<summary><code>POST</code> <code><b>/api/qr/generate</b></code></summary>

**Authentication Required:** ✅ JWT or API Key

**Request Body:**

```json
{
  "IRN": "TSP0110134-B17E2F91-20251122"
}
```

**Query Parameters:**

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `format` | string | Output format: `json`, `png`, `svg` | `json` |
| `size` | string | QR code size (width x height) | `200x200` |

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "qrCodeDataUrl": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
    "encryptedData": "encrypted-qr-data-string",
    "irnWithTimestamp": "TSP0110134-B17E2F91-20251122-1705312800",
    "specification": "FIRS E-Invoice QR Code v1.0"
  }
}
```

**cURL Example:**

```bash
curl -X POST https://api.firs-einvoice.com/api/qr/generate?format=json \
  -H "Authorization: Bearer your-access-token" \
  -H "Content-Type: application/json" \
  -d '{
    "IRN": "TSP0110134-B17E2F91-20251122"
  }'
```

**Get PNG Image:**

```bash
curl -X POST https://api.firs-einvoice.com/api/qr/generate?format=png&size=300x300 \
  -H "Authorization: Bearer your-access-token" \
  -H "Content-Type: application/json" \
  -d '{
    "IRN": "TSP0110134-B17E2F91-20251122"
  }' \
  --output qrcode.png
```

> **🔒 Security Note:** Certificate and PublicKey are automatically fetched from your tenant configuration. You don't need to provide them in the request.

> **🎯 Tenant Auto-Detection:** Your tenant is automatically determined from your authentication. Only specify `tenant_key` if you have access to multiple tenants.

</details>

