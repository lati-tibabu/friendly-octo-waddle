# Telebirr Payment Integration for Odoo

This repository contains an Odoo module for integrating Telebirr payment gateway into your Odoo e-commerce system.

## Overview

Telebirr is Ethiopia's leading mobile payment service, allowing users to make secure online and mobile payments. This module enables Odoo-based e-commerce websites to accept payments through Telebirr's payment gateway.

## Module Structure

```
payment_telebirr/
├── __init__.py                          # Module initialization
├── __manifest__.py                      # Module manifest with metadata
├── README.md                            # Module documentation
├── controllers/
│   ├── __init__.py
│   └── main.py                          # Payment controllers (webhooks, returns)
├── models/
│   ├── __init__.py
│   ├── payment_provider.py              # Payment provider model
│   └── payment_transaction.py           # Payment transaction model
├── views/
│   ├── payment_provider_views.xml       # Provider configuration views
│   └── payment_telebirr_templates.xml   # Payment form templates
├── data/
│   └── payment_provider_data.xml        # Initial provider data
├── security/
│   └── ir.model.access.csv              # Access control rules
└── static/
    ├── description/
    │   └── index.html                   # App store description
    └── src/
        ├── img/
        │   ├── telebirr_icon.png        # Provider icon
        │   └── telebirr_icon.svg        # Provider icon (SVG)
        └── js/
            └── payment_form.js          # Frontend JavaScript
```

## Features

- ✅ Secure payment processing through Telebirr gateway
- ✅ Support for Ethiopian Birr (ETB) currency
- ✅ Webhook integration for real-time payment status updates
- ✅ Test (sandbox) and production mode support
- ✅ Transaction lifecycle management
- ✅ Payment status tracking
- ✅ Configurable through Odoo UI
- ✅ Mobile-optimized payment flow

## Installation

### Prerequisites

1. Odoo 14.0 or higher
2. Telebirr merchant account with API credentials:
   - App ID
   - App Key
   - Merchant ID
   - RSA Public Key

### Installation Steps

1. **Clone the repository or download the module:**
   ```bash
   cd /path/to/odoo/addons
   git clone <repository-url>
   ```

2. **Restart Odoo server:**
   ```bash
   sudo service odoo restart
   ```

3. **Update Apps List:**
   - Go to Apps menu in Odoo
   - Click "Update Apps List"
   - Search for "Telebirr"

4. **Install the module:**
   - Click "Install" on the "Payment Provider: Telebirr" module

## Configuration

### Step 1: Activate the Payment Provider

1. Navigate to: **Accounting → Configuration → Payment Providers**
2. Find and click on **Telebirr**
3. Configure the provider:

### Step 2: Basic Configuration

- **State**: 
  - Select "Test" for sandbox testing
  - Select "Enabled" for production use
- **Display As**: "Telebirr" (or customize as needed)
- **Allow Tokenization**: Configure based on your needs
- **Allow Express Checkout**: Configure based on your needs

### Step 3: API Credentials

Enter your Telebirr merchant credentials:

- **App ID**: Your application ID from Telebirr
- **App Key**: Your application key (kept secret)
- **Merchant ID**: Your merchant identifier
- **Public Key**: RSA public key provided by Telebirr

### Step 4: Save and Publish

1. Click **Save**
2. Click **Published** to make the payment provider visible to customers

## Usage

### For Customers

1. During checkout, customers will see Telebirr as a payment option
2. When selected, they'll be redirected to Telebirr's payment page
3. They complete payment using their Telebirr mobile wallet
4. Upon completion, they're redirected back to your store
5. Payment confirmation is displayed

### For Administrators

#### Monitoring Transactions

1. Go to: **Accounting → Payment Transactions**
2. Filter by provider "Telebirr"
3. View transaction status and details

#### Handling Failed Payments

- Failed transactions are automatically marked
- Review logs for detailed error information
- Contact Telebirr support for API-related issues

## Payment Flow

```
┌─────────────┐
│  Customer   │
│   Checkout  │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Select    │
│  Telebirr   │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Redirect   │
│ to Telebirr │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Payment   │
│  Completed  │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Webhook    │
│ Notification│
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Update    │
│ Transaction │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Return to  │
│    Store    │
└─────────────┘
```

## API Integration

### Endpoints

The module implements two main endpoints:

1. **Return URL**: `/payment/telebirr/return`
   - Handles customer return from Telebirr
   - Displays payment status

2. **Webhook URL**: `/payment/telebirr/notify`
   - Receives payment notifications from Telebirr
   - Updates transaction status automatically

### API URLs

- **Production**: `https://api.telebirr.com/gateway`
- **Sandbox**: `https://test-api.telebirr.com/gateway`

## Testing

### Test Mode Setup

1. Set provider state to "Test"
2. Use Telebirr's sandbox credentials
3. Use test phone numbers provided by Telebirr

### Test Scenarios

- ✅ Successful payment
- ✅ Failed payment
- ✅ Cancelled payment
- ✅ Webhook notifications
- ✅ Return redirects

## Security

### Security Features

- **Signature Verification**: All webhook notifications are verified using HMAC SHA-256
- **Credential Protection**: API credentials are encrypted and accessible only to system administrators
- **HTTPS Required**: All API communications use HTTPS
- **Access Control**: Proper Odoo security groups and access rules

### Security Best Practices

1. Never share your App Key
2. Use test credentials in sandbox mode only
3. Regularly rotate API credentials
4. Monitor transaction logs for suspicious activity
5. Keep the module updated

## Troubleshooting

### Common Issues

**Issue**: Payment provider not showing at checkout
- **Solution**: Ensure the provider is published and state is not "Disabled"
- **Solution**: Verify currency is set to ETB

**Issue**: Webhook notifications not received
- **Solution**: Check that your Odoo instance is accessible from the internet
- **Solution**: Verify webhook URL in Telebirr merchant dashboard
- **Solution**: Check server logs for errors

**Issue**: Payment fails with signature error
- **Solution**: Verify App Key is correct
- **Solution**: Ensure system time is synchronized

### Logs

Check Odoo logs for detailed error messages:
```bash
tail -f /var/log/odoo/odoo-server.log | grep -i telebirr
```

## Development

### Module Structure

The module follows Odoo's standard addon structure:

- **Models**: Extend `payment.provider` and `payment.transaction`
- **Controllers**: Handle HTTP endpoints for webhooks and returns
- **Views**: XML templates for configuration and payment forms
- **Static Assets**: Icons, JavaScript for frontend

### Extending the Module

To extend functionality:

1. Inherit the payment models
2. Override methods as needed
3. Add custom views or templates
4. Update manifest dependencies

## Support

### Resources

- [Odoo Documentation](https://www.odoo.com/documentation)
- [Telebirr Developer Portal](https://www.telebirr.com/developers)
- Module Repository: Submit issues and feature requests

### Getting Help

1. **Module Issues**: Open an issue in the repository
2. **Telebirr API Issues**: Contact Telebirr support
3. **Odoo Questions**: Consult Odoo community forums

## License

This module is licensed under LGPL-3. See LICENSE file for details.

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## Changelog

### Version 1.0 (Initial Release)

- ✅ Basic payment processing
- ✅ Webhook integration
- ✅ Test and production modes
- ✅ ETB currency support
- ✅ Transaction management
- ✅ Configuration UI

## Authors

Developed for Odoo integration with Telebirr payment gateway.

## Acknowledgments

- Odoo SA for the payment framework
- Telebirr for the payment gateway API
- Contributors to this module
