# 🛒 FakeStore API — Complete Postman Test Suite

> A comprehensive API test collection built against [FakeStoreAPI](https://fakestoreapi.com) — a free, realistic e-commerce REST API.
> This collection demonstrates real-world API testing techniques including authentication, schema validation, chained requests, negative testing and dynamic data generation.

**Built by:** Sneha Ujjainkar — Senior QA Engineer | 9+ Years in Banking & Telecommunications
**API Under Test:** `https://fakestoreapi.com`
**Total Test Cases:** 20 requests · 60+ assertions

---

## 📁 File

| File | Description |
|---|---|
| `FakeStoreAPI_Complete_Test_Suite.postman_collection.json` | Full Postman collection — import directly into Postman |

---

## 🧪 Test Coverage Summary

| Module | Requests | Key Assertions |
|---|---|---|
| 🔐 Auth | 3 | JWT token format, error handling, missing fields |
| 🛍️ Products | 9 | Schema, sorting, filtering, CRUD, chained requests |
| 👤 Users | 3 | Nested schema, email regex, dynamic data |
| 🛒 Carts | 5 | Date range filter, user filter, product quantities |
| **Total** | **20** | **60+ assertions** |

---

## 🚀 How to Import & Run

### Option 1 — Run in Postman (Recommended for beginners)

1. Download the collection file from this folder
2. Open **Postman** → click **Import** (top left)
3. Drag and drop the `.json` file into the import window
4. Click the collection name → click **Run collection**
5. Hit **Run FakeStore API** — watch all tests execute live ✅

### Option 2 — Run via Newman CLI (Command line)

**Install Newman:**
```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```

**Run the collection:**
```bash
newman run FakeStoreAPI_Complete_Test_Suite.postman_collection.json
```

**Run with a beautiful HTML report:**
```bash
newman run FakeStoreAPI_Complete_Test_Suite.postman_collection.json \
  -r htmlextra \
  --reporter-htmlextra-export reports/fakestore-report.html
```

---

## 📋 Detailed Test Cases

---

### 🔐 Auth

The Auth module tests the login endpoint which returns a JWT token used for authenticated requests.

#### 1. POST Login — Valid Credentials ✅
**Endpoint:** `POST /auth/login`
**What it tests:**
- Status code is `200`
- Response time is under `2000ms`
- JWT token is present in the response
- Token is extracted and saved automatically as a collection variable for reuse
- Token format is validated as a proper 3-part JWT (`header.payload.signature`)

```json
Request Body:
{
  "username": "johnd",
  "password": "m38rmF$"
}
```

---

#### 2. POST Login — Invalid Password ❌ (Negative Test)
**Endpoint:** `POST /auth/login`
**What it tests:**
- Status code is `400` or `401` — NOT `200`
- Response time is under `2000ms`
- An error message body is returned

**Why this matters:** Verifying that incorrect credentials are rejected is critical in Banking and FinTech systems. A passing login on invalid credentials would be a critical security defect.

---

#### 3. POST Login — Missing Username ❌ (Negative Test)
**Endpoint:** `POST /auth/login`
**What it tests:**
- Status code is NOT `200`
- Response time is under `2000ms`

**Why this matters:** Missing required fields must always be handled gracefully — never causing a server crash or unexpected `500` error.

---

### 🛍️ Products

The Products module covers the full product lifecycle — GET, POST, PUT, PATCH, DELETE — as well as filtering and sorting.

#### 4. GET All Products ✅
**Endpoint:** `GET /products`
**What it tests:**
- Status code is `200`
- Response time is under `2000ms`
- Response is an array
- Returns exactly `20` products
- Every product has: `id`, `title`, `price`, `category`, `image`
- All prices are positive numbers (data integrity check)

---

#### 5. GET All Products — Limited Results ✅
**Endpoint:** `GET /products?limit=5`
**What it tests:**
- Status code is `200`
- Returns exactly `5` products as specified by the `limit` query parameter
- Response time is under `2000ms`

**Technique used:** Query parameter validation — ensures the API correctly filters results based on user input.

---

#### 6. GET All Products — Sorted Descending ✅
**Endpoint:** `GET /products?sort=desc`
**What it tests:**
- Status code is `200`
- First product ID is greater than the last product ID (descending order confirmed)

**Technique used:** Sorting validation — confirms the `sort` parameter is applied correctly server-side.

---

#### 7. GET Single Product by ID ✅
**Endpoint:** `GET /products/1`
**What it tests:**
- Status code is `200`
- Response time is under `2000ms`
- Product ID matches the requested ID
- Full JSON schema validation including nested `rating` object
- Rating value is between `0` and `5`

**Schema validated:**
```json
{
  "id": number,
  "title": string,
  "price": number,
  "description": string,
  "category": string,
  "image": string,
  "rating": {
    "rate": number (0-5),
    "count": number
  }
}
```

---

#### 8. GET All Categories ✅
**Endpoint:** `GET /products/categories`
**What it tests:**
- Status code is `200`
- Response is an array of strings
- Expected categories `electronics` and `jewelery` are present
- First category is saved as a collection variable for the next chained request

**Technique used:** Chained request setup — the output of this test feeds directly into the next test.

---

#### 9. GET Products by Category ✅ *(Chained from Test 8)*
**Endpoint:** `GET /products/category/{{category}}`
**What it tests:**
- Status code is `200`
- Response is an array
- **Every single product returned belongs to the requested category** — no data leakage from other categories

**Technique used:** Chained request — uses the category variable saved in Test 8. This simulates real end-to-end journey validation.

---

#### 10. POST Add New Product ✅
**Endpoint:** `POST /products`
**What it tests:**
- Status code is `200` or `201`
- Response time is under `3000ms`
- A new product `id` is returned
- Product `title` matches what was sent in the request body

**Technique used:** Pre-request script generates a unique timestamp-based product title on every run — preventing test data conflicts.

---

#### 11. PUT Update Product ✅
**Endpoint:** `PUT /products/1`
**What it tests:**
- Status code is `200`
- Updated `title` is reflected in the response
- Updated `price` is reflected in the response

**Technique used:** Full resource replacement — all fields sent, all fields validated in response.

---

#### 12. PATCH Partial Update Product ✅
**Endpoint:** `PATCH /products/1`
**What it tests:**
- Status code is `200`
- Only the `price` field is updated
- Other fields remain unchanged

**Technique used:** Partial update — demonstrates understanding of the difference between `PUT` (full replace) and `PATCH` (partial update). A common interview question for QA roles.

---

#### 13. DELETE Product ✅
**Endpoint:** `DELETE /products/1`
**What it tests:**
- Status code is `200`
- Response time is under `2000ms`
- Deleted product data is returned in the response confirming what was removed

---

### 👤 Users

#### 14. GET All Users ✅
**Endpoint:** `GET /users`
**What it tests:**
- Status code is `200`
- Response is an array
- Every user has: `id`, `email`, `username`, `address`
- **Email format validated using regex** for all users — not just checking the field exists, but that the value is a valid email address

**Regex used:** `/^[^\s@]+@[^\s@]+\.[^\s@]+$/`

---

#### 15. GET Single User by ID ✅
**Endpoint:** `GET /users/1`
**What it tests:**
- Status code is `200`
- Full nested schema validation including `name` object (`firstname`, `lastname`) and `address` object (`city`, `street`, `zipcode`)
- User `id` matches the requested ID

**Schema validated:**
```json
{
  "id": number,
  "email": string,
  "username": string,
  "name": {
    "firstname": string,
    "lastname": string
  },
  "address": {
    "city": string,
    "street": string,
    "zipcode": string
  },
  "phone": string
}
```

---

#### 16. POST Create New User ✅
**Endpoint:** `POST /users`
**What it tests:**
- Status code is `200` or `201`
- New user `id` is returned
- `username` matches what was sent

**Note:** Request body uses a London, UK address — a nice touch for UK market relevance! 🇬🇧

---

### 🛒 Carts

#### 17. GET All Carts ✅
**Endpoint:** `GET /carts`
**What it tests:**
- Status code is `200`
- Response is an array
- Every cart has: `id`, `userId`, `products` (as an array)

---

#### 18. GET Cart by ID ✅
**Endpoint:** `GET /carts/1`
**What it tests:**
- Status code is `200`
- Full cart schema validation including nested `products` array
- Cart contains at least one product
- **All product quantities are positive numbers** — prevents zero or negative quantity defects reaching production

---

#### 19. GET Carts in Date Range ✅
**Endpoint:** `GET /carts?startdate=2020-01-01&enddate=2020-12-31`
**What it tests:**
- Status code is `200`
- Response is an array

**Why this matters:** Date range filtering is critical in Banking and e-commerce systems — transaction history, statement periods, and reporting all depend on accurate date filtering.

---

#### 20. GET Carts by User ID ✅
**Endpoint:** `GET /carts/user/1`
**What it tests:**
- Status code is `200`
- **Every cart in the response belongs to the requested user ID** — no data from other users returned

**Why this matters:** In Banking, data isolation between users is a compliance and security requirement. This test pattern directly mirrors Open Banking consent and account segregation testing.

---

#### 21. POST Add New Cart ✅
**Endpoint:** `POST /carts`
**What it tests:**
- Status code is `200` or `201`
- New cart `id` is returned
- Cart contains exactly `2` products as specified in the request

---

## 🧠 Testing Techniques Demonstrated

| Technique | Where Used |
|---|---|
| ✅ Positive testing | All GET, POST, PUT, PATCH requests |
| ❌ Negative testing | Auth invalid credentials, missing fields |
| 🔗 Chained requests | Categories → Products by Category |
| 📐 JSON Schema validation | Products, Users, Carts |
| 🔢 Data integrity checks | Prices > 0, Ratings 0–5, Quantities > 0 |
| 📧 Format validation | Email regex across all users |
| ⏱️ Response time assertions | Every single request |
| 🎲 Dynamic test data | Pre-request scripts with timestamps |
| 🔍 Query parameter testing | limit, sort, startdate, enddate |
| 🔐 Token extraction & reuse | JWT saved from login, used downstream |

---

## 👩‍💻 About the Author

**Sneha Ujjainkar**
Senior QA Engineer · Test Lead · Quality Culture Advocate
9+ years across Banking (ASB Bank) and Telecommunications (Spark NZ)

Specialising in: Mobile · Web · API Testing · Open Banking · Agile Environments

🌏 Relocating to London, UK in 2026 | Open to Skilled Worker Visa Sponsorship

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/sneha-ujjainkar-732552122/)

---

*This collection is for portfolio and demonstration purposes using a publicly available free API.*
