---
title: Overview
excerpt: >-
  The FIRS E-Invoice API provides a complete solution for generating, signing,
  and managing electronic invoices compliant with Nigeria's FIRS requirements.
---

# Overview

The FIRS E-Invoice API provides a complete solution for generating, signing, and managing electronic invoices compliant with Nigeria's FIRS requirements. Our API supports both manual invoice creation and seamless integration with popular ERP systems.

## Key Features

✨ **Multi-Tenant Support** - Isolated configurations per tenant  
🔐 **Dual Authentication** - JWT tokens and API keys  
📊 **ERP Integration** - Connect with Dynamics, Oracle, SAP, Sage, and more  
🔍 **QR Code Generation** - FIRS-compliant QR codes with RSA encryption  
📝 **Invoice Lifecycle** - Sign, track, and update invoice status  
🎯 **Smart Tenant Detection** - Automatic tenant resolution from authentication  
⚡ **Rate Limiting** - Built-in protection and fair usage policies

## How It Works

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

