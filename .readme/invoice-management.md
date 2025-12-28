# Invoice Management

Manage your invoices through the complete lifecycle: signing, tracking, and updating status.

## Sign Invoice

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

## Sign Invoice from ERP Order

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

## Get Invoice Details

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

## Update Invoice Status

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

