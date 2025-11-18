# Telebirr Payment Integration for Odoo

This repository contains a complete Odoo module for integrating Telebirr mobile payment gateway into Odoo e-commerce systems.

## What is This?

Telebirr is Ethiopia's leading mobile payment service. This module allows Odoo-based businesses to accept payments from Telebirr users, enabling seamless mobile payment integration for Ethiopian e-commerce.

## Quick Start

1. **Install the module** in your Odoo instance
2. **Configure** your Telebirr API credentials
3. **Activate** the payment provider
4. **Start accepting** Telebirr payments!

## Documentation

- 📖 [Complete Integration Guide](INTEGRATION_GUIDE.md)
- 📘 [Module Documentation](payment_telebirr/README.md)

## Features

- ✅ Secure payment processing via Telebirr gateway
- ✅ Ethiopian Birr (ETB) currency support
- ✅ Real-time webhook notifications
- ✅ Test and production environments
- ✅ Full transaction lifecycle management
- ✅ Mobile-optimized payment experience

## Module Structure

```
payment_telebirr/          # Main Odoo module
├── models/                # Payment provider and transaction models
├── controllers/           # Webhook and redirect handlers
├── views/                 # Configuration and payment templates
├── data/                  # Initial data
├── security/              # Access control
└── static/                # Assets (icons, JavaScript)
```

## Requirements

- Odoo 14.0+
- Telebirr merchant account with API credentials

## Installation

See the [Integration Guide](INTEGRATION_GUIDE.md) for detailed installation and configuration instructions.

## Support

For issues and questions:
- **Module issues**: Open an issue in this repository
- **Telebirr API**: Contact Telebirr support
- **Odoo integration**: Consult Odoo documentation

## License

LGPL-3
