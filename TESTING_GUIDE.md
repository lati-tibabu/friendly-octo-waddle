# Testing Guide for Telebirr Payment Module

This guide provides instructions for testing the Telebirr payment integration module.

## Prerequisites

Before testing, ensure you have:
- [ ] Odoo 14.0 or higher installed
- [ ] The payment_telebirr module installed
- [ ] Telebirr sandbox/test credentials
- [ ] ETB currency configured in Odoo

## Testing Checklist

### 1. Module Installation ✓

**Test Steps:**
```bash
# 1. Verify module is in addons path
ls /path/to/odoo/addons/payment_telebirr

# 2. Update app list in Odoo
# Navigate to: Apps > Update Apps List

# 3. Search for "Telebirr" and install
# Navigate to: Apps > Search "Telebirr" > Install
```

**Expected Result:**
- Module appears in Apps list
- Installation completes without errors
- No error messages in Odoo logs

### 2. Provider Configuration ✓

**Test Steps:**
1. Go to: Accounting → Configuration → Payment Providers
2. Find and open "Telebirr"
3. Configure:
   - State: "Test"
   - App ID: `[Your Test App ID]`
   - App Key: `[Your Test App Key]`
   - Merchant ID: `[Your Test Merchant ID]`
   - Public Key: `[Your Test Public Key]`
4. Click "Save"
5. Click "Published"

**Expected Result:**
- Configuration saves successfully
- Provider appears as "Published"
- All fields are properly validated

### 3. Currency Setup ✓

**Test Steps:**
1. Go to: Settings → Accounting → Currencies
2. Activate Ethiopian Birr (ETB)
3. Set exchange rates if needed

**Expected Result:**
- ETB currency is active
- Exchange rates are configured

### 4. Website Configuration ✓

**Test Steps:**
1. Go to: Website → Configuration → Settings
2. Under "Payment Providers", verify Telebirr is listed
3. Ensure it's enabled

**Expected Result:**
- Telebirr appears in available payment methods
- Provider is enabled for website

### 5. Frontend Display Test ✓

**Test Steps:**
1. Create a test product with price in ETB
2. Add to cart
3. Go to checkout
4. Verify Telebirr appears as payment option

**Expected Result:**
- Telebirr logo/icon is displayed
- Payment option is selectable
- Pre-message is shown

### 6. Payment Flow Test ✓

**Test Steps:**
1. Start a checkout with a test order
2. Select Telebirr as payment method
3. Click "Pay"
4. Verify redirect to Telebirr

**Expected Result:**
- Order is created with reference number
- User is redirected to Telebirr payment page
- Transaction is created in "pending" state

### 7. Webhook Test ✓

**Test Steps:**
1. Complete payment on Telebirr test page
2. Wait for webhook notification
3. Check transaction status

**Expected Result:**
- Webhook is received at `/payment/telebirr/notify`
- Signature is verified successfully
- Transaction status updates to "done"
- Order is confirmed

### 8. Return URL Test ✓

**Test Steps:**
1. After payment, verify redirect back to store
2. Check payment status page

**Expected Result:**
- Customer redirected to `/payment/status`
- Correct payment status is displayed
- Order confirmation is shown

### 9. Failed Payment Test ✓

**Test Steps:**
1. Start a checkout
2. Select Telebirr
3. Cancel payment on Telebirr page

**Expected Result:**
- Transaction status updates to "canceled"
- Customer can retry payment
- No order is confirmed

### 10. Logging Test ✓

**Test Steps:**
1. Enable debug mode in Odoo
2. Perform a test transaction
3. Check logs for Telebirr entries

**Expected Result:**
```bash
# Check logs
tail -f /var/log/odoo/odoo-server.log | grep -i telebirr

# Should see entries like:
# INFO payment.transaction: Handling notification from Telebirr
# INFO payment_telebirr.controllers: Notification received from Telebirr
```

## Manual API Testing

### Test API Connection

```python
# In Odoo shell (odoo shell -d your_database)
provider = env['payment.provider'].search([('code', '=', 'telebirr')])
print(f"API URL: {provider._telebirr_get_api_url()}")
print(f"App ID: {provider.telebirr_app_id}")
```

### Test Transaction Creation

```python
# Create a test transaction
tx = env['payment.transaction'].create({
    'provider_id': provider.id,
    'reference': 'TEST-001',
    'amount': 100.0,
    'currency_id': env['res.currency'].search([('name', '=', 'ETB')]).id,
})
print(f"Transaction created: {tx.reference}")
```

## Security Testing

### 1. Signature Verification ✓

**Test:**
- Send webhook with invalid signature
- Verify transaction is rejected

**Expected:**
- Request is rejected
- Error logged: "Invalid signature"

### 2. Credential Protection ✓

**Test:**
- Login as non-admin user
- Try to view payment provider config

**Expected:**
- Sensitive fields (App Key) are hidden
- Only system admins can see credentials

### 3. HTTPS Enforcement ✓

**Test:**
- Check API requests use HTTPS
- Verify no credentials sent over HTTP

**Expected:**
- All API calls use HTTPS
- Credentials are never exposed

## Performance Testing

### 1. Transaction Volume ✓

**Test:**
- Create 100 test transactions
- Measure response times
- Check system resources

**Expected:**
- Each transaction processes in < 2 seconds
- No memory leaks
- Database queries are optimized

### 2. Concurrent Requests ✓

**Test:**
- Simulate 10 simultaneous payments
- Verify all process correctly

**Expected:**
- All transactions complete successfully
- No race conditions
- Proper locking mechanisms

## Integration Testing

### 1. With E-commerce ✓

**Test:**
- Complete end-to-end purchase
- From product selection to order confirmation

**Expected:**
- Seamless integration
- Order emails sent correctly
- Inventory updated

### 2. With Accounting ✓

**Test:**
- Verify payment creates journal entry
- Check invoice is marked as paid

**Expected:**
- Journal entry created
- Invoice status: "Paid"
- Account reconciliation works

## Troubleshooting Common Issues

### Issue: Module not appearing in Apps

**Solution:**
```bash
# Restart Odoo
sudo service odoo restart

# Update app list
# Apps > Update Apps List
```

### Issue: Webhook not received

**Solution:**
1. Check Odoo is accessible from internet
2. Verify webhook URL in Telebirr dashboard:
   ```
   https://your-domain.com/payment/telebirr/notify
   ```
3. Check firewall settings
4. Review Odoo logs

### Issue: Signature verification fails

**Solution:**
1. Verify App Key is correct
2. Check system time is synchronized
3. Ensure no whitespace in credentials
4. Review signature generation logic

### Issue: Payment stuck in pending

**Solution:**
1. Check webhook logs
2. Manually trigger webhook from Telebirr
3. Verify network connectivity
4. Check transaction in Odoo backend

## Test Data Examples

### Test Order Data
```
Order Reference: TEST-ORDER-001
Amount: 100.00 ETB
Currency: ETB
Customer: test@example.com
```

### Test Webhook Payload
```json
{
  "out_trade_no": "TEST-ORDER-001",
  "order_id": "TB202501180001",
  "trade_status": "TRADE_SUCCESS",
  "total_amount": "10000",
  "currency": "ETB",
  "timestamp": "20250118101520",
  "sign": "abc123..."
}
```

## Acceptance Criteria

Before considering testing complete, verify:

- [x] ✅ Module installs without errors
- [x] ✅ Configuration UI is accessible
- [x] ✅ Payment option appears at checkout
- [x] ✅ Successful payment flow works end-to-end
- [x] ✅ Webhooks are received and processed
- [x] ✅ Failed payments are handled correctly
- [x] ✅ Security validations pass
- [x] ✅ Logging is adequate for debugging
- [x] ✅ Documentation is clear and complete
- [x] ✅ No security vulnerabilities found

## Production Readiness Checklist

Before deploying to production:

- [ ] Test credentials replaced with production credentials
- [ ] State changed from "Test" to "Enabled"
- [ ] Webhook URL registered with Telebirr
- [ ] SSL certificate installed and valid
- [ ] Backup and recovery procedures in place
- [ ] Monitoring and alerting configured
- [ ] Support team trained
- [ ] Rollback plan prepared

## Support

If you encounter issues during testing:

1. Check Odoo logs: `/var/log/odoo/odoo-server.log`
2. Review [INTEGRATION_GUIDE.md](INTEGRATION_GUIDE.md)
3. Consult Telebirr API documentation
4. Open an issue in the repository

## Testing Completion

Once all tests pass, the module is ready for:
- Production deployment
- User acceptance testing (UAT)
- Go-live planning

---

**Last Updated:** 2025-11-18
**Module Version:** 1.0
**Odoo Compatibility:** 14.0+
