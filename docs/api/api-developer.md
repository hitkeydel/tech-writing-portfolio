# Billing API v1 Developer & Integration Guide

## 1. Overview
The Billing API enables automated transaction execution, balance posting, and credit card processing integration with core system ledgers.

---

## 2. Authentication

All requests to the API must include authentication headers. The API supports **Bearer Tokens (JWT)** for user-level sessions and **API Keys** for backend service-to-service calls.

### Required Headers:
* `Authorization: Bearer <YOUR_JWT_TOKEN>`
* `X-API-Key: <YOUR_API_KEY>`
* `Content-Type: application/json`

---

## 3. Quickstart & Examples

### 3.1 Execute Card Payment (`cURL`)

```bash
curl -X POST "https://sandbox.billing-system.com/v1/payments/charge" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "X-API-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "study_id": 437,
    "payer_type": "Patient",
    "amount": 200.00,
    "currency": "USD",
    "card_number": "4242424242424242",
    "exp_month": 11,
    "exp_year": 2028,
    "cvc": "123",
    "cardholder_name": "ONE TEST"
  }'
```