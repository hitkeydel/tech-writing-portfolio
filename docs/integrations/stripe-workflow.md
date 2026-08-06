# Stripe Integration & Card Payment Processing

## 1. System Configuration & Setup

### 1.1 Prerequisites & Credentials
1. The client must maintain an active Stripe account (`stripe.com`) and extract their **Public Key** and **Secret Key**.
2. Navigate to **`Setup -> Billing -> Payment card providers`** in XXXXX and enter the retrieved API keys.
3. Add the `ExternalPaymentsProcessor` daemon to the system variable **`daemon.jobs_list`**.
4. Configure supported credit card providers via **`Setup -> Billing -> Card types`**.

---

## 2. Payment Processing Workflow

### 2.1 Scope & Capabilities
Stripe integration allows charging credit/debit cards for:
* Patients (Self-pay / Copay / Deductible)
* Referring Facilities (B2B commercial credit cards)
* Insurance Payers

### 2.2 Execution Steps
1. Open the target study record and navigate to **`Billing Info`**.
2. Select **`Operations -> Post payment`**.
3. Set **Method** to `Credit/debit card`.
4. Populate card details (**Number**, **Cardholder Name**, **Expiration Date**, **CVV**, and **Billing Address**).
5. Click **OK**.
   * The system immediately executes an automated API payment call to Stripe.
   * Upon successful authorization, the payment transaction is posted to the billing record.

### 2.3 Transaction Audit & Tracking
All posted card payments and saved card profiles are logged and accessible in **`Billing -> Cards`**.

---

## 3. Testing Environment & Credentials

### 3.1 Test Account Credentials
* **Portal**: Stripe Web Console
* **Username**: `user@name.com`
* **Password**: `xxx`

### 3.2 Test API Keys
* **Public Key**: `pk_test_xxx`
* **Secret Key**: `sk_test_xxx`

### 3.3 Reference Resources
* [Stripe Testing Documentation](https://stripe.com/docs/testing)
* [Stripe Test Card Numbers](https://stripe.com/docs/testing#cards)