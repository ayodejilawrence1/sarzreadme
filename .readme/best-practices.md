# Best Practices

## Security

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

## Performance

1. **⚡ Use ERP Integration**
   - Prefer `/api/invoices/sign-from-order` over manual signing
   - Reduces data entry errors and saves time

2. **📊 Batch Operations**
   - Process invoices in batches when possible
   - Respect rate limits

3. **💾 Cache Tokens**
   - Cache access tokens until expiration
   - Use refresh tokens to get new access tokens

## Data Quality

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

## Tenant Management

1. **🎯 Auto-Detection**
   - Let the system auto-detect your tenant from authentication
   - Only specify `tenant_key` when you have multiple tenants

2. **⚙️ ERP Configuration**
   - Configure ERP once per tenant
   - Test connection before going live
   - Keep field mappings up to date

## Code Examples

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

