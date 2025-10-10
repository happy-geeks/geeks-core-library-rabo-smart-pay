# GeeksCoreLibrary - Rabo Smart Pay Integration

## Repository Metadata
- **Type**: Integration Module
- **Language(s)**: C#
- **Framework(s)**: .NET 9.0
- **Database**: MySQL (via Wiser EAV)

## Purpose & Context

### What This Repository Does
This repository provides a payment service provider integration module for Rabobank's SmartPay (OmniKassa) payment system within the GeeksCoreLibrary/Wiser ecosystem. It enables e-commerce functionality by handling payment transactions, webhooks, and order status updates through Rabobank's payment gateway.

The module implements the `IPaymentServiceProviderService` interface from GeeksCoreLibrary and provides full integration with Rabobank's OmniKassa API, supporting multiple payment methods including iDEAL, credit cards, PayPal, Afterpay, and various European payment brands.

### Business Value
This integration is critical for any Wiser-based e-commerce platform that processes payments through Rabobank SmartPay. It enables businesses to:
- Accept payments from Dutch and European customers using familiar payment methods
- Process secure transactions through a trusted banking partner
- Handle payment callbacks and status updates reliably
- Support multiple payment brands from a single integration

### Wiser Ecosystem Role
This module extends GeeksCoreLibrary's payment processing capabilities by:
- **Extends**: `PaymentServiceProviderBaseService`
- **Implements**: `IPaymentServiceProviderService` interface
- **Integrates with**: Shopping basket service, order process, and database components
- **Depends on**: GeeksCoreLibrary core package (version 5.3.2508.1)
- **Used by**: Wiser-based e-commerce websites during checkout process

## Core Functionality

### Main Components

**RaboSmartPayService** - Main service implementing:

1. **`HandlePaymentRequestAsync()`**
   - Initiates a payment request
   - Converts shopping baskets to OmniKassa order format
   - Returns redirect URL to OmniKassa payment page

2. **`ProcessStatusUpdateAsync()`**
   - Processes status callbacks
   - Updates order status based on payment result

3. **`HandleNotification()`**
   - Processes webhook notifications from OmniKassa
   - Validates signatures for security
   - Retrieves full status details via API

4. **`GetProviderSettingsAsync()`**
   - Loads configuration from database
   - Determines environment (test vs production)
   - Decrypts API credentials

### Supported Payment Methods

| External Name | Rabobank Brand |
|---------------|----------------|
| IDEAL | IDEAL |
| AFTERPAY | AFTERPAY |
| PAYPAL | PAYPAL |
| MASTERCARD | MASTERCARD |
| VISA | VISA |
| BANCONTACT | BANCONTACT |
| MAESTRO | MAESTRO |
| V_PAY / VPAY | V_PAY |

### Key Business Rules

**Price Handling:**
- Prices converted to EUR currency
- Uses `PspPriceInVat` calculation method
- Validates total amount is positive

**Address Requirements:**
- Billing address is mandatory
- Shipping address falls back to billing if not provided
- Supports street, number, suffix, postal code, city, country

**Security:**
- All responses validated via HMAC signatures
- Credentials stored encrypted in database
- Supports separate test/live environments

## Installation & Setup

### Prerequisites
- **.NET 9.0 SDK** or later
- **GeeksCoreLibrary** version 5.3.2508.1+
- **MySQL database** with Wiser schema
- **Rabobank OmniKassa account** with API credentials

### Configuration

Configuration stored in Wiser database (`wiser_itemdetail`):

| Property Key | Purpose | Environment |
|--------------|---------|-------------|
| `raboomnikassarefreshtokenlive` | Production refresh token | Live |
| `raboomnikassarefreshtokentest` | Test refresh token | Test |
| `raboomnikassasigningkeylive` | Production signing key | Live |
| `raboomnikassasigningkeytest` | Test signing key | Test |

All values are stored encrypted.

## Troubleshooting

### Common Issues

**"IllegalSignatureException":**
- Verify signing key matches OmniKassa dashboard
- Check correct environment suffix (Test vs Live)
- Regenerate signing key if necessary

**"InvalidAccessTokenException":**
- Refresh token expired or revoked
- Generate new token in OmniKassa dashboard
- Update encrypted value in database

**"Unsupported payment method":**
- Check external name matches supported brands
- Update `ConvertPaymentMethodToPaymentBrand()` if needed

**Webhook not triggering:**
- Verify webhook URL is publicly accessible
- Check URL matches OmniKassa dashboard configuration
- Test endpoint manually with sample payload

## External Resources

- [Rabobank OmniKassa Developer Portal](https://developer.rabobank.nl/omnikassa)
- [OmniKassa .NET SDK](https://www.nuget.org/packages/OmniKassa_Rabobank/)
- [.NET 9.0 Documentation](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-9)

---

*This documentation is part of the Wiser Platform knowledge base maintained by Happy Horizon B.V.*
