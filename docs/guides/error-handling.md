---
title: Error Handling
excerpt: The API uses standard HTTP status codes and provides detailed error messages.
---

# Error Handling

The API uses standard HTTP status codes and provides detailed error messages.

## HTTP Status Codes

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

## Error Response Format

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

## Common Error Codes

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

## Example Error Responses

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

