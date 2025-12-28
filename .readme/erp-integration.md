# ERP Integration

Connect your ERP system to automatically fetch invoice data. Supported systems include Microsoft Dynamics, Oracle, SAP, Sage, and more.

## Configure ERP System

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

## Supported ERP Systems

| ERP System | Status | Authentication Methods |
|------------|--------|------------------------|
| **Microsoft Dynamics 365 Business Central** | ✅ Supported | OAuth 2.0, Basic Auth |
| **Microsoft Dynamics NAV** | ✅ Supported | Basic Auth |
| **Oracle ERP** | 🚧 Coming Soon | TBD |
| **SAP** | 🚧 Coming Soon | TBD |
| **Sage** | 🚧 Coming Soon | TBD |
| **Vanilla (Custom)** | ✅ Supported | Custom configuration |

## How ERP Integration Works

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

