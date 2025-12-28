# FIRS E-Invoice API Documentation

> **Modern e-invoicing API for Nigeria's Federal Inland Revenue Service (FIRS) compliance**

Welcome to the FIRS E-Invoice API! This comprehensive guide will help you integrate e-invoicing capabilities into your application, with full support for ERP systems like Microsoft Dynamics, Oracle, SAP, and more.

---

## Table of Contents

- [Overview](#overview)
- [Getting Started](#getting-started)
- [Authentication](#authentication)
- [QR Code Generation](#qr-code-generation)
- [Invoice Management](#invoice-management)
- [ERP Integration](#erp-integration)
- [Error Handling](#error-handling)
- [Best Practices](#best-practices)

---

## Overview

The FIRS E-Invoice API provides a complete solution for generating, signing, and managing electronic invoices compliant with Nigeria's FIRS requirements. Our API supports both manual invoice creation and seamless integration with popular ERP systems.

### Key Features

✨ **Multi-Tenant Support** - Isolated configurations per tenant  
🔐 **Dual Authentication** - JWT tokens and API keys  
📊 **ERP Integration** - Connect with Dynamics, Oracle, SAP, Sage, and more  
🔍 **QR Code Generation** - FIRS-compliant QR codes with RSA encryption  
📝 **Invoice Lifecycle** - Sign, track, and update invoice status  
🎯 **Smart Tenant Detection** - Automatic tenant resolution from authentication  
⚡ **Rate Limiting** - Built-in protection and fair usage policies  

### How It Works

```
┌─────────────┐      ┌──────────────┐      ┌─────────────┐
│   Your App  │─────▶│  FIRS API    │─────▶│    FIRS     │
│             │      │   Service    │      │   Platform  │
└─────────────┘      └──────────────┘      └─────────────┘
                            │
                            ▼
                     ┌──────────────┐
                     │  ERP System  │
                     │  (Optional)  │
                     └──────────────┘
```

1. **Authenticate** - Get your JWT token or use an API key
2. **Configure ERP** (Optional) - Connect your ERP system for automatic invoice data extraction
3. **Generate QR Codes** - Create FIRS-compliant QR codes for invoices
4. **Sign Invoices** - Submit invoices directly or fetch from ERP
5. **Track Status** - Monitor invoice status and update payment information

---

## Getting Started

### Base URL

```
Production: https://api.firs-einvoice.com
Sandbox:     https://sandbox.firs-einvoice.com
```

### API Version

All endpoints are prefixed with `/api/v1` (or just `/api` for current version).

### Response Format

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

### Rate Limits

- **JWT Authentication**: 100 requests per minute
- **API Key Authentication**: Custom limits per key (default: 1000 requests per minute)
- **Authentication Endpoints**: 10 requests per minute

---

## Authentication

The API supports two authentication methods:

1. **JWT Bearer Tokens** - For user-based access
2. **API Keys** - For programmatic/integration access

### Register

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

### Login

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

### Refresh Token

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

### Using Authentication

Include your token in the `Authorization` header:

```bash
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Or use an API key:

```bash
X-API-Key: your-api-key-here
```

---

## QR Code Generation

Generate FIRS-compliant QR codes for your invoices. QR codes are encrypted using RSA encryption and contain all required invoice information.

### Generate QR Code

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

---

## Invoice Management

Manage your invoices through the complete lifecycle: signing, tracking, and updating status.

### Sign Invoice

Sign an invoice directly with FIRS by providing all invoice details.

<details>
<summary><code>POST</code> <code><b>/api/invoices/sign</b></code></summary>

**Authentication Required:** ✅ JWT or API Key  
**Required Scope:** `invoice:sign`

**Request Body:**

```json
{
  "irn": "TSP0110134-B17E2F91-20251122",
  "issue_date": "2024-01-15",
  "invoice_type_code": "388",
  "document_currency_code": "NGN",
  "tax_currency_code": "NGN",
  "invoice_line": [
    {
      "hsn_code": "8703.24.00",
      "product_category": "PART",
      "invoiced_quantity": 10,
      "item_name": "Brake Pad Set",
      "item_description": "Front Brake Pad Set - Premium Quality",
      "price_amount": 15000.00,
      "price_unit": "C62",
      "base_quantity": 1,
      "tax_category": "LOCAL_SALES_TAX",
      "tax_percent": 7.5,
      "discount_amount": 0,
      "discount_rate": 0
    }
  ]
}
```

**Field Descriptions:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `irn` | string | ✅ | Invoice Reference Number |
| `issue_date` | date | ✅ | Invoice issue date (YYYY-MM-DD) |
| `invoice_type_code` | string | ✅ | Invoice type (388 = Commercial Invoice) |
| `document_currency_code` | string | ✅ | Currency code (NGN, USD, etc.) |
| `tax_currency_code` | string | ✅ | Tax currency code |
| `invoice_line` | array | ✅ | Array of invoice line items |

**Invoice Line Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `hsn_code` | string | ✅ | Harmonized System Nomenclature code |
| `product_category` | string | ✅ | Product category (PART, SERVICE, etc.) |
| `invoiced_quantity` | number | ✅ | Quantity of items |
| `item_name` | string | ✅ | Short item name |
| `item_description` | string | ✅ | Detailed item description |
| `price_amount` | number | ✅ | Unit price (excluding tax) |
| `price_unit` | string | ✅ | Unit code (C62 = "one") |
| `base_quantity` | number | ✅ | Base quantity unit |
| `tax_category` | string | ✅ | Tax category (LOCAL_SALES_TAX, etc.) |
| `tax_percent` | number | ✅ | Tax percentage |
| `discount_amount` | number | ❌ | Discount amount |
| `discount_rate` | number | ❌ | Discount rate percentage |

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "irn": "TSP0110134-B17E2F91-20251122",
    "status": "SIGNED",
    "firs_response": {
      "status": "success",
      "message": "Invoice signed successfully"
    }
  }
}
```

**cURL Example:**

```bash
curl -X POST https://api.firs-einvoice.com/api/invoices/sign \
  -H "Authorization: Bearer your-access-token" \
  -H "Content-Type: application/json" \
  -d '{
    "irn": "TSP0110134-B17E2F91-20251122",
    "issue_date": "2024-01-15",
    "invoice_type_code": "388",
    "document_currency_code": "NGN",
    "tax_currency_code": "NGN",
    "invoice_line": [
      {
        "hsn_code": "8703.24.00",
        "product_category": "PART",
        "invoiced_quantity": 10,
        "item_name": "Brake Pad Set",
        "item_description": "Front Brake Pad Set - Premium Quality",
        "price_amount": 15000.00,
        "price_unit": "C62",
        "base_quantity": 1,
        "tax_category": "LOCAL_SALES_TAX",
        "tax_percent": 7.5
      }
    ]
  }'
```

> **💡 Note:** Supplier party information (business ID, TIN, address, etc.) is automatically fetched from your tenant configuration. You don't need to provide it in the request.

</details>

### Sign Invoice from ERP Order

Automatically fetch invoice data from your configured ERP system and sign it. This is the easiest way to sign invoices if you have ERP integration set up.

<details>
<summary><code>POST</code> <code><b>/api/invoices/sign-from-order</b></code></summary>

**Authentication Required:** ✅ JWT or API Key  
**Required Scope:** `invoice:sign-from-order`

**Request Body (Minimal - Recommended):**

```json
{
  "order_number": "TSP0082373"
}
```

That's it! All invoice details are automatically extracted from your ERP system.

**Request Body (With Optional Overrides):**

```json
{
  "order_number": "TSP0082373",
  "irn": "CUSTOM-IRN-12345",
  "irn_suffix": "4",
  "invoice_type_code": "388",
  "document_currency_code": "NGN",
  "tax_currency_code": "NGN"
}
```

**Field Descriptions:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `order_number` | string | ✅ | Order/Document number in your ERP system |
| `irn` | string | ❌ | Override IRN (only if ERP doesn't have it) |
| `irn_suffix` | string | ❌ | Suffix to append to IRN first part |
| `invoice_type_code` | string | ❌ | Invoice type (default: "388") |
| `document_currency_code` | string | ❌ | Currency code (default: from ERP or "NGN") |
| `tax_currency_code` | string | ❌ | Tax currency (default: from ERP or "NGN") |

**What Gets Extracted Automatically:**

- ✅ **Header Information** (from first row):
  - Document number
  - Posting date
  - IRN (if available)
  - Currency information
  - Customer details

- ✅ **Line Items** (from all rows):
  - Item codes and descriptions
  - Quantities and prices
  - Tax information
  - Discounts

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "order_number": "TSP0082373",
    "erp_type": "DYNAMICS",
    "dynamics_data": {
      "document_no": "TSP0082373",
      "posting_date": "2024-01-15",
      "line_count": 5
    },
    "irn": "TSP0110134-B17E2F91-20251122",
    "status": "SIGNED"
  }
}
```

**cURL Example:**

```bash
curl -X POST https://api.firs-einvoice.com/api/invoices/sign-from-order \
  -H "Authorization: Bearer your-access-token" \
  -H "Content-Type: application/json" \
  -d '{
    "order_number": "TSP0082373"
  }'
```

> **🚀 Pro Tip:** This is the recommended method if you have ERP integration. Just provide the order number, and everything else is handled automatically!

> **⚠️ Prerequisite:** You must configure your ERP system first using the `/api/tenants/{tenantId}/erp-configs` endpoint.

</details>

### Get Invoice Details

Retrieve invoice details from FIRS by IRN.

<details>
<summary><code>GET</code> <code><b>/api/invoices/{irn}</b></code></summary>

**Authentication Required:** ✅ JWT or API Key  
**Required Scope:** `invoice:read`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `irn` | string | ✅ | Invoice Reference Number |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tenant_key` | string | ❌ | Tenant key (auto-detected from auth if not provided) |

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "irn": "TSP0110134-B17E2F91-20251122",
    "status": "SIGNED",
    "issue_date": "2024-01-15",
    "total_amount": 150000.00,
    "tax_amount": 11250.00,
    "firs_data": {
      // Full FIRS invoice data
    }
  }
}
```

**cURL Example:**

```bash
curl -X GET https://api.firs-einvoice.com/api/invoices/TSP0110134-B17E2F91-20251122 \
  -H "Authorization: Bearer your-access-token"
```

</details>

### Update Invoice Status

Update the payment status of an invoice.

<details>
<summary><code>PATCH</code> <code><b>/api/invoices/{irn}/status</b></code></summary>

**Authentication Required:** ✅ JWT or API Key  
**Required Scope:** `invoice:update`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `irn` | string | ✅ | Invoice Reference Number |

**Request Body:**

```json
{
  "payment_status": "PAID",
  "reference": "PAYMENT-REF-12345"
}
```

**Status Values:**

- `PENDING` - Invoice is pending payment
- `PAID` - Invoice has been paid
- `REJECTED` - Invoice was rejected

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "irn": "TSP0110134-B17E2F91-20251122",
    "payment_status": "PAID",
    "reference": "PAYMENT-REF-12345",
    "updated_at": "2024-01-16T10:30:00Z"
  }
}
```

**cURL Example:**

```bash
curl -X PATCH https://api.firs-einvoice.com/api/invoices/TSP0110134-B17E2F91-20251122/status \
  -H "Authorization: Bearer your-access-token" \
  -H "Content-Type: application/json" \
  -d '{
    "payment_status": "PAID",
    "reference": "PAYMENT-REF-12345"
  }'
```

</details>

---

## ERP Integration

Connect your ERP system to automatically fetch invoice data. Supported systems include Microsoft Dynamics, Oracle, SAP, Sage, and more.

### Configure ERP System

Set up your ERP system connection. This enables the `/api/invoices/sign-from-order` endpoint to automatically fetch invoice data.

<details>
<summary><code>POST</code> <code><b>/api/tenants/{tenantId}/erp-configs</b></code></summary>

**Authentication Required:** ✅ JWT  
**Required Scope:** User must have access to the tenant

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tenantId` | string | ✅ | Your tenant ID (UUID) |

**Request Body (Microsoft Dynamics - OAuth):**

```json
{
  "erpType": "DYNAMICS",
  "baseUrl": "https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/{environment}/ODataV4/Company('COMPANY%20NAME')",
  "authType": "OAUTH",
  "authConfig": {
    "erpClientId": "your-azure-ad-client-id",
    "erpClientSecret": "your-azure-ad-client-secret",
    "erpTenantId": "your-azure-ad-tenant-id",
    "erpADAuthority": "https://login.microsoftonline.com/",
    "erpScope": "https://api.businesscentral.dynamics.com/.default"
  },
  "erpConfig": {
    "invoiceEndpoint": "FIRSSalesInvoice"
  },
  "fieldMappings": {
    "header": {
      "documentNumber": "Document_No",
      "postingDate": "Posting_Date",
      "irn": "IRN"
    },
    "line": {
      "itemCode": "No",
      "itemCategoryCode": "Item_Category_Code",
      "quantity": "Quantity",
      "description": "Description",
      "unitPrice": "Unit_Price",
      "vatPercent": "VAT_Percent",
      "taxCategory": "Tax_Category",
      "lineDiscountAmount": "Line_Discount_Amount"
    }
  }
}
```

**Request Body (Microsoft Dynamics - Basic Auth):**

```json
{
  "erpType": "DYNAMICS",
  "baseUrl": "http://your-server:port/BC140/ODataV4/Company('COMPANY')",
  "authType": "BASIC",
  "authConfig": {
    "username": "your-dynamics-username",
    "password": "your-dynamics-password"
  },
  "erpConfig": {
    "invoiceEndpoint": "FIRSSalesInvoice"
  },
  "fieldMappings": {
    "header": {
      "documentNumber": "Document_No",
      "postingDate": "Posting_Date",
      "irn": "IRN"
    },
    "line": {
      "itemCode": "No",
      "itemCategoryCode": "Item_Category_Code",
      "quantity": "Quantity",
      "description": "Description",
      "unitPrice": "Unit_Price",
      "vatPercent": "VAT_Percent",
      "taxCategory": "Tax_Category",
      "lineDiscountAmount": "Line_Discount_Amount"
    }
  }
}
```

**Field Descriptions:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `erpType` | string | ✅ | ERP system type: `DYNAMICS`, `ORACLE`, `SAP`, `SAGE`, `VANILLA` |
| `baseUrl` | string | ✅ | Base URL of your ERP OData service endpoint |
| `authType` | string | ✅ | Authentication type: `OAUTH`, `BASIC`, `API_KEY` |
| `authConfig` | object | ✅ | Authentication configuration (varies by authType) |
| `erpConfig` | object | ❌ | ERP-specific configuration |
| `erpConfig.invoiceEndpoint` | string | ❌ | OData entity set name (default: `FIRSSalesInvoice`) |
| `fieldMappings` | object | ❌ | Custom field mappings (null = use defaults) |

**OAuth Configuration (for Dynamics 365 Business Central Online):**

| Field | Description |
|-------|-------------|
| `erpClientId` | Azure AD Application (client) ID |
| `erpClientSecret` | Azure AD Application client secret |
| `erpTenantId` | Azure AD Directory (tenant) ID |
| `erpADAuthority` | Azure AD authority URL (usually `https://login.microsoftonline.com/`) |
| `erpScope` | OAuth scope (usually `https://api.businesscentral.dynamics.com/.default`) |

**Basic Auth Configuration (for Dynamics NAV/BC On-premise):**

| Field | Description |
|-------|-------------|
| `username` | Dynamics username |
| `password` | Dynamics password |

> **💡 Alternative:** For Basic Auth, you can set `NAVBC140_USERNAME` and `NAVBC140_PASSWORD` environment variables instead of providing them in `authConfig`.

**Custom Field Mappings:**

If your ERP uses different field names, you can customize the mappings:

```json
{
  "fieldMappings": {
    "header": {
      "documentNumber": "Invoice_No",
      "postingDate": "Date",
      "irn": "FIRS_IRN"
    },
    "line": {
      "itemCode": "Item_No",
      "itemCategoryCode": "Category",
      "quantity": "Qty",
      "description": "Item_Description",
      "unitPrice": "Price",
      "vatPercent": "VAT_Rate",
      "taxCategory": "Tax_Type",
      "lineDiscountAmount": "Discount"
    }
  }
}
```

**Response:** `201 Created`

```json
{
  "success": true,
  "data": {
    "id": "erp-config-uuid",
    "tenantId": "tenant-uuid",
    "erpType": "DYNAMICS",
    "baseUrl": "https://api.businesscentral.dynamics.com/...",
    "authType": "OAUTH",
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

**cURL Example:**

```bash
curl -X POST https://api.firs-einvoice.com/api/tenants/{tenantId}/erp-configs \
  -H "Authorization: Bearer your-access-token" \
  -H "Content-Type: application/json" \
  -d '{
    "erpType": "DYNAMICS",
    "baseUrl": "https://api.businesscentral.dynamics.com/v2.0/.../Company('\''COMPANY'\'')",
    "authType": "OAUTH",
    "authConfig": {
      "erpClientId": "your-client-id",
      "erpClientSecret": "your-client-secret",
      "erpTenantId": "your-tenant-id",
      "erpADAuthority": "https://login.microsoftonline.com/",
      "erpScope": "https://api.businesscentral.dynamics.com/.default"
    },
    "erpConfig": {
      "invoiceEndpoint": "FIRSSalesInvoice"
    }
  }'
```

> **🎯 Pro Tip:** After configuring your ERP, you can use `/api/invoices/sign-from-order` with just the order number. All invoice details will be automatically fetched!

</details>

### Supported ERP Systems

| ERP System | Status | Authentication Methods |
|------------|--------|------------------------|
| **Microsoft Dynamics 365 Business Central** | ✅ Supported | OAuth 2.0, Basic Auth |
| **Microsoft Dynamics NAV** | ✅ Supported | Basic Auth |
| **Oracle ERP** | 🚧 Coming Soon | TBD |
| **SAP** | 🚧 Coming Soon | TBD |
| **Sage** | 🚧 Coming Soon | TBD |
| **Vanilla (Custom)** | ✅ Supported | Custom configuration |

### How ERP Integration Works

1. **Configure Connection** - Set up your ERP system using the `/api/tenants/{tenantId}/erp-configs` endpoint
2. **Fetch Invoice Data** - Call `/api/invoices/sign-from-order` with just the order number
3. **Automatic Extraction** - The system:
   - Connects to your ERP using configured credentials
   - Fetches invoice data from the OData endpoint
   - Maps ERP-specific fields to standard format
   - Extracts header info from the first row
   - Extracts all line items from all rows
4. **Sign with FIRS** - Invoice is automatically signed and submitted to FIRS

**Data Flow:**

```
Your Request (order_number)
    ↓
ERP Adapter (connects to ERP)
    ↓
Field Mapper (maps ERP fields → standard format)
    ↓
Invoice Service (transforms → FIRS format)
    ↓
FIRS API (signs invoice)
    ↓
Response (signed invoice with IRN)
```

---

## Error Handling

The API uses standard HTTP status codes and provides detailed error messages.

### HTTP Status Codes

| Code | Meaning | Description |
|------|---------|-------------|
| `200` | OK | Request successful |
| `201` | Created | Resource created successfully |
| `400` | Bad Request | Invalid request parameters |
| `401` | Unauthorized | Authentication required or invalid |
| `403` | Forbidden | Insufficient permissions |
| `404` | Not Found | Resource not found |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Server error |

### Error Response Format

```json
{
  "success": false,
  "error": "Detailed error message",
  "code": "ERROR_CODE",
  "details": {
    // Additional error details (if available)
  }
}
```

### Common Error Codes

| Error Code | Description | Solution |
|------------|-------------|----------|
| `VALIDATION_ERROR` | Request validation failed | Check required fields and data types |
| `AUTHENTICATION_ERROR` | Invalid credentials | Verify username/password or API key |
| `AUTHORIZATION_ERROR` | Insufficient permissions | Check user role and scopes |
| `TENANT_NOT_FOUND` | Tenant not found | Verify tenant ID or key |
| `ERP_CONFIG_NOT_FOUND` | ERP not configured | Configure ERP using `/api/tenants/{tenantId}/erp-configs` |
| `ERP_CONNECTION_ERROR` | Cannot connect to ERP | Verify ERP credentials and base URL |
| `FIRS_API_ERROR` | FIRS API error | Check FIRS API status and invoice data |
| `RATE_LIMIT_EXCEEDED` | Too many requests | Wait before retrying or upgrade rate limits |

### Example Error Responses

**Validation Error:**

```json
{
  "success": false,
  "error": "Validation failed",
  "code": "VALIDATION_ERROR",
  "details": {
    "field": "irn",
    "message": "irn is required"
  }
}
```

**Authentication Error:**

```json
{
  "success": false,
  "error": "Invalid credentials",
  "code": "AUTHENTICATION_ERROR"
}
```

**ERP Connection Error:**

```json
{
  "success": false,
  "error": "Failed to connect to ERP system",
  "code": "ERP_CONNECTION_ERROR",
  "details": {
    "erpType": "DYNAMICS",
    "message": "Authentication failed. Please verify your credentials."
  }
}
```

---

## Best Practices

### Security

1. **🔐 Store Tokens Securely**
   - Never commit tokens to version control
   - Use environment variables or secure secret management
   - Rotate API keys regularly

2. **🛡️ Use HTTPS**
   - Always use HTTPS in production
   - Never send credentials over unencrypted connections

3. **🔑 API Key Management**
   - Use API keys for server-to-server communication
   - Use JWT tokens for user-based access
   - Implement proper key rotation policies

### Performance

1. **⚡ Use ERP Integration**
   - Prefer `/api/invoices/sign-from-order` over manual signing
   - Reduces data entry errors and saves time

2. **📊 Batch Operations**
   - Process invoices in batches when possible
   - Respect rate limits

3. **💾 Cache Tokens**
   - Cache access tokens until expiration
   - Use refresh tokens to get new access tokens

### Data Quality

1. **✅ Validate Before Sending**
   - Ensure all required fields are present
   - Validate data types and formats
   - Check IRN format before submission

2. **📝 Complete Invoice Data**
   - Provide complete line item details
   - Include accurate tax information
   - Use correct HSN codes

3. **🔄 Handle Errors Gracefully**
   - Implement retry logic for transient errors
   - Log errors for debugging
   - Provide user-friendly error messages

### Tenant Management

1. **🎯 Auto-Detection**
   - Let the system auto-detect your tenant from authentication
   - Only specify `tenant_key` when you have multiple tenants

2. **⚙️ ERP Configuration**
   - Configure ERP once per tenant
   - Test connection before going live
   - Keep field mappings up to date

### Code Examples

**JavaScript/Node.js:**

```javascript
const axios = require('axios');

const API_BASE_URL = 'https://api.firs-einvoice.com';
const ACCESS_TOKEN = 'your-access-token';

// Sign invoice from ERP order
async function signInvoiceFromOrder(orderNumber) {
  try {
    const response = await axios.post(
      `${API_BASE_URL}/api/invoices/sign-from-order`,
      { order_number: orderNumber },
      {
        headers: {
          'Authorization': `Bearer ${ACCESS_TOKEN}`,
          'Content-Type': 'application/json'
        }
      }
    );
    return response.data;
  } catch (error) {
    console.error('Error signing invoice:', error.response?.data || error.message);
    throw error;
  }
}

// Generate QR code
async function generateQRCode(irn) {
  try {
    const response = await axios.post(
      `${API_BASE_URL}/api/qr/generate?format=json`,
      { IRN: irn },
      {
        headers: {
          'Authorization': `Bearer ${ACCESS_TOKEN}`,
          'Content-Type': 'application/json'
        }
      }
    );
    return response.data;
  } catch (error) {
    console.error('Error generating QR code:', error.response?.data || error.message);
    throw error;
  }
}
```

**Python:**

```python
import requests

API_BASE_URL = 'https://api.firs-einvoice.com'
ACCESS_TOKEN = 'your-access-token'

headers = {
    'Authorization': f'Bearer {ACCESS_TOKEN}',
    'Content-Type': 'application/json'
}

# Sign invoice from ERP order
def sign_invoice_from_order(order_number):
    url = f'{API_BASE_URL}/api/invoices/sign-from-order'
    data = {'order_number': order_number}
    
    response = requests.post(url, json=data, headers=headers)
    response.raise_for_status()
    return response.json()

# Generate QR code
def generate_qr_code(irn):
    url = f'{API_BASE_URL}/api/qr/generate?format=json'
    data = {'IRN': irn}
    
    response = requests.post(url, json=data, headers=headers)
    response.raise_for_status()
    return response.json()
```

---

## Support

Need help? We're here for you!

- **📧 Email:** support@firs-einvoice.com
- **📚 Documentation:** https://docs.firs-einvoice.com
- **💬 Community:** https://community.firs-einvoice.com
- **🐛 Report Issues:** https://github.com/firs-einvoice/api/issues

---

## Changelog

### Version 1.0.0 (2024-01-15)

- ✨ Initial release
- 🔐 JWT and API key authentication
- 📊 ERP integration support (Dynamics)
- 🔍 QR code generation
- 📝 Invoice signing and management
- 🎯 Multi-tenant support
- ⚡ Rate limiting

---

**Last Updated:** January 15, 2024  
**API Version:** 1.0.0

