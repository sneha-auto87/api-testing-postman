# 🔌 API Testing with Postman & Newman

> A collection of real-world API test suites built using Postman and Newman CLI — covering REST API validation, authentication flows, Open Banking journeys and regression testing patterns used in Banking and Telecommunications environments.

![Postman](https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)
![Newman](https://img.shields.io/badge/Newman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)

---

## 📁 Repository Structure

```
api-testing-postman/
│
├── collections/
│   ├── 01_REST_API_Basics.postman_collection.json
│   ├── 02_Auth_and_OAuth2.postman_collection.json
│   ├── 03_Open_Banking_Journey.postman_collection.json
│   ├── 04_Payment_Gateway_Validation.postman_collection.json
│   └── 05_Regression_Suite.postman_collection.json
│
├── environments/
│   ├── dev.postman_environment.json
│   ├── staging.postman_environment.json
│   └── production.postman_environment.json
│
├── test-data/
│   ├── valid_users.json
│   ├── invalid_payloads.json
│   └── edge_cases.json
│
├── reports/
│   └── sample-newman-report.html
│
├── docs/
│   ├── API_Testing_Strategy.md
│   ├── Test_Case_Design_Guide.md
│   └── Newman_Setup_Guide.md
│
└── README.md
```

---

## 🧪 What's Inside

### Collection 1 — REST API Basics
Covers fundamental API testing patterns every QA engineer should know:
- GET, POST, PUT, PATCH, DELETE request validation
- Status code assertions (200, 201, 400, 401, 403, 404, 500)
- Response body schema validation
- Response time assertions
- Header validation

### Collection 2 — Authentication & OAuth2
- Bearer token validation
- OAuth 2.0 authorisation code flow testing
- Token expiry and refresh scenarios
- Invalid credential handling
- Session management edge cases

### Collection 3 — Open Banking Journey ⭐
End-to-end Open Banking authentication and consent flow testing:
- Consent initiation and validation
- Redirect URI handling
- Account information retrieval
- Payment initiation flows
- Error handling for declined consent

### Collection 4 — Payment Gateway Validation
- Successful payment transaction flows
- Declined payment scenarios
- Refund and reversal testing
- Timeout and network error handling
- Idempotency key validation

### Collection 5 — Regression Suite
- Full end-to-end regression collection
- Environment-driven test data
- Pre-request scripts for dynamic data generation
- Automated assertions across all critical endpoints

---

## 🚀 Getting Started

### Prerequisites
- [Postman](https://www.postman.com/downloads/) installed
- [Node.js](https://nodejs.org/) (v14 or above)
- Newman CLI installed globally

```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```

### Running Collections in Postman
1. Clone this repository
```bash
git clone https://github.com/sneha-auto87/api-testing-postman.git
```
2. Open Postman → Import → select any `.json` file from `/collections`
3. Import the matching environment from `/environments`
4. Click **Run Collection**

### Running with Newman CLI
```bash
# Run a single collection
newman run collections/01_REST_API_Basics.postman_collection.json \
  -e environments/dev.postman_environment.json

# Run with HTML report
newman run collections/03_Open_Banking_Journey.postman_collection.json \
  -e environments/staging.postman_environment.json \
  -r htmlextra \
  --reporter-htmlextra-export reports/open-banking-report.html

# Run full regression suite
newman run collections/05_Regression_Suite.postman_collection.json \
  -e environments/staging.postman_environment.json \
  --iteration-count 3 \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/regression-report.html
```

---

## ✅ Test Design Approach

This repository follows industry best practices for API test design:

| Technique | Description |
|---|---|
| **Positive Testing** | Valid inputs returning expected responses |
| **Negative Testing** | Invalid inputs, missing fields, wrong data types |
| **Boundary Testing** | Min/max field lengths, limits and thresholds |
| **Schema Validation** | JSON schema checks on every response |
| **Chained Requests** | Output of one request as input to the next |
| **Environment Variables** | Dynamic, environment-agnostic test data |
| **Pre-request Scripts** | Dynamic token generation, timestamps, UUIDs |

---

## 📋 Sample Test Scripts

### Status Code Assertion
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

### Response Time Assertion
```javascript
pm.test("Response time is under 2000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});
```

### JSON Schema Validation
```javascript
const schema = {
    type: "object",
    required: ["id", "status", "accountNumber"],
    properties: {
        id: { type: "string" },
        status: { type: "string", enum: ["ACTIVE", "INACTIVE"] },
        accountNumber: { type: "string", minLength: 8 }
    }
};

pm.test("Response schema is valid", function () {
    pm.response.to.have.jsonSchema(schema);
});
```

### Chained Request — Extract Token
```javascript
// Save token from login response for use in next request
const jsonData = pm.response.json();
pm.environment.set("auth_token", jsonData.access_token);
pm.environment.set("user_id", jsonData.user.id);

pm.test("Auth token is present", function () {
    pm.expect(jsonData.access_token).to.be.a('string').and.not.empty;
});
```

### Negative Test — Invalid Payload
```javascript
pm.test("Returns 400 for missing required field", function () {
    pm.response.to.have.status(400);
});

pm.test("Error message is descriptive", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.error).to.include("required");
});
```

---

## 📊 Sample Newman Report Output

```
newman

REST API Basics Collection
→ GET /users
  ✓ Status code is 200
  ✓ Response time under 2000ms
  ✓ Response schema is valid
  ✓ User ID is present in response

→ POST /users
  ✓ Status code is 201
  ✓ New user ID returned
  ✓ Response time under 2000ms

→ POST /users (invalid payload)
  ✓ Status code is 400
  ✓ Error message is descriptive

┌─────────────────────────┬──────────┬──────────┐
│                         │ executed │   failed │
├─────────────────────────┼──────────┼──────────┤
│              iterations │        1 │        0 │
│                requests │       12 │        0 │
│            test-scripts │       24 │        0 │
│      prerequest-scripts │        6 │        0 │
│              assertions │       38 │        0 │
├─────────────────────────┴──────────┴──────────┤
│ total run duration: 3.4s                       │
│ total data received: 14.56KB                   │
│ average response time: 284ms                   │
└────────────────────────────────────────────────┘
```

---

## 🔄 CI/CD Integration

This collection is designed to run in CI/CD pipelines. Example GitHub Actions workflow:

```yaml
name: API Regression Tests

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  api-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Install Newman
        run: npm install -g newman newman-reporter-htmlextra

      - name: Run API Tests
        run: |
          newman run collections/05_Regression_Suite.postman_collection.json \
            -e environments/staging.postman_environment.json \
            -r cli,htmlextra \
            --reporter-htmlextra-export reports/regression-report.html

      - name: Upload Test Report
        uses: actions/upload-artifact@v3
        with:
          name: newman-report
          path: reports/regression-report.html
```

---

## 📖 Documentation

| Doc | Description |
|---|---|
| [API Testing Strategy](./docs/API_Testing_Strategy.md) | Test approach, coverage areas and risk assessment |
| [Test Case Design Guide](./docs/Test_Case_Design_Guide.md) | How to write effective API test cases |
| [Newman Setup Guide](./docs/Newman_Setup_Guide.md) | Step-by-step Newman installation and usage |

---

## 👩‍💻 About the Author

**Sneha Ujjainkar** — Senior QA Engineer with 9+ years experience in Banking and Telecommunications.
Specialising in Mobile, Web & API Testing | Open Banking | Agile Environments.

🌏 Relocating to London, UK in 2025 | Open to Skilled Worker Visa Sponsorship

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/sneha-ujjainkar-732552122/)

---

## 📄 Licence

This project is open source and available under the [MIT Licence](LICENSE).
