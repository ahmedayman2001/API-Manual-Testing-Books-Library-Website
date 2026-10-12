#  API Manual Testing — Books Library Website

![Postman](https://img.shields.io/badge/Postman-Collection%20v2.1-FF6C37?logo=postman&logoColor=white)
![Test Cases](https://img.shields.io/badge/Test%20Cases-32-blue)
![Assertions](https://img.shields.io/badge/JS%20Assertions-119-success)
![Type](https://img.shields.io/badge/Testing-Functional%20%7C%20Negative%20%7C%20Boundary-informational)

A structured **API manual testing project** built with **Postman** for a **Books Library Website** backend, the [Simple Books API](https://simple-books-api.click), a REST API that lets clients browse books and reserve them through authenticated orders.

The project covers **32 test cases** across all API endpoints, with **JavaScript assertions** written in Postman's test scripts, **dynamic data generation**, **request chaining** through collection variables, and a mix of **positive, negative, boundary and end-to-end scenarios**.

---

## 🎬 Demo

▶️ **Video demo:** [Watch the project walkthrough](https://drive.google.com/file/d/1b3WK1dRpp8Pdm8Pq0ctgv5Aa96fWtgiJ/view?usp=drive_link)

---

##  API Under Test

| | |
|---|---|
| **Base URL** | `https://simple-books-api.click` |
| **Official documentation** | [Simple Books API docs](https://github.com/vdespa/introduction-to-postman-course/blob/main/simple-books-api.md) |
| **Authentication** | Bearer token, obtained by registering an API client (valid for 7 days) |

### Endpoints

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|:----:|
| `GET` | `/status` | API health status | ❌ |
| `GET` | `/books` | List books (optional `type` = `fiction` / `non-fiction`, `limit` = 1–20) | ❌ |
| `GET` | `/books/:bookId` | Get details of a single book | ❌ |
| `POST` | `/api-clients` | Register an API client and receive an access token | ❌ |
| `POST` | `/orders` | Submit a new order (`bookId`, `customerName`) | ✅ |
| `GET` | `/orders` | List all orders | ✅ |
| `GET` | `/orders/:orderId` | Get a single order | ✅ |
| `PATCH` | `/orders/:orderId` | Update an order (`customerName`) | ✅ |
| `DELETE` | `/orders/:orderId` | Delete an order | ✅ |

---

##  Project Objectives

- Verify that every endpoint behaves according to the API documentation.
- Validate **status codes**, **response headers**, **response body structure** and **data types**.
- Cover **negative scenarios** (missing fields, wrong types, missing or invalid token, non-existent resources).
- Cover **boundary value analysis** for the `limit` query parameter (0, 1, 20, 21).
- Automate the full **order lifecycle** (create → read → update → verify → delete) using chained requests.
- Make the collection **re-runnable** with no manual edits by generating fresh test data on every run.

---

##  Key Features

-  **32 test cases** organized into 3 folders by feature area
-  **119 JavaScript assertions** using Postman's `pm.test` and Chai (`pm.expect`)
-  **Dynamic test data**: random client name and email generated in a pre-request script (avoids `409 Conflict` on re-runs)
-  **Request chaining**: `accessToken`, `bookId` and `orderId` are extracted from responses and reused automatically
-  **Bearer token authentication** applied through collection variables
-  **Negative and boundary testing** with meaningful error-message checks
-  **Persistence verification**: a follow-up `GET` confirms that a `PATCH` was actually saved
-  **Collection Runner ready** for one-click execution

---

##  Collection Structure

```
API_ManualTesting_BooksLibraryWebsite_APIs_Collection_Postman
│
├── 📁 Status & Authentication      (TC01 – TC05)
├── 📁 Books                        (TC06 – TC17)
└── 📁 Orders                       (TC18 – TC32)
```

### 🔹 Status & Authentication (TC01 – TC05)

| ID | Scenario | Expected |
|----|----------|----------|
| TC01 | `GET /status`: API is up | `200`, `status = OK` |
| TC02 | `POST /api-clients`: register new client | `201`, `accessToken` returned and saved |
| TC03 | `POST /api-clients`: duplicate registration | `409`, "already registered" message |
| TC04 | `POST /api-clients`: missing `clientEmail` | `400` |
| TC05 | `POST /api-clients`: missing `clientName` | `400` |

### 🔹 Books (TC06 – TC17)

| ID | Scenario | Expected |
|----|----------|----------|
| TC06 | `GET /books`: list of books | `200`, non-empty array, valid structure, unique ids; first available `bookId` saved |
| TC07 | `GET /books?type=fiction` | `200`, only fiction books |
| TC08 | `GET /books?type=non-fiction` | `200`, only non-fiction books |
| TC09 | `GET /books?limit=3` | `200`, exactly 3 books |
| TC10 | `GET /books?limit=1`: lower boundary | `200`, exactly 1 book |
| TC11 | `GET /books?limit=20`: upper boundary | `200` |
| TC12 | `GET /books?limit=0`: below lower boundary | `400` |
| TC13 | `GET /books?limit=21`: above upper boundary | `400` |
| TC14 | `GET /books?type=comics`: invalid type | `400` |
| TC15 | `GET /books?type=fiction&limit=2`: combined filters | `200` |
| TC16 | `GET /books/:bookId`: valid book | `200`, `id`, `name`, `author`, `isbn` present |
| TC17 | `GET /books/:bookId`: non-existent book | `404`, no book data returned |

### 🔹 Orders (TC18 – TC32)

| ID | Scenario | Expected |
|----|----------|----------|
| TC18 | `POST /orders`: no token | `401`, no order created |
| TC19 | `POST /orders`: invalid token | `401`, no order created |
| TC20 | `POST /orders`: valid data | `201`, `orderId` returned and saved |
| TC21 | `POST /orders`: missing `bookId` | `400` |
| TC22 | `POST /orders`: `bookId` as a string | `400` |
| TC23 | `POST /orders`: non-existent `bookId` | `404` / `400` |
| TC24 | `GET /orders`: list all orders | `200` |
| TC25 | `GET /orders`: no token | `401` |
| TC26 | `GET /orders/:orderId`: valid order | `200`, correct fields and data types, id matches |
| TC27 | `GET /orders/:orderId`: non-existent order | `404` |
| TC28 | `PATCH /orders/:orderId`: update `customerName` | `204`, empty body |
| TC29 | `GET /orders/:orderId`: verify PATCH persisted | `200`, updated `customerName` |
| TC30 | `PATCH /orders/:orderId`: no token | `401` |
| TC31 | `DELETE /orders/:orderId`: no token | `401` |
| TC32 | `DELETE /orders/:orderId`: delete order | `204`, empty body |

---

##  JavaScript Assertions

Each request includes a test script that goes beyond checking the status code. The checks include:

- **Status code** validation
- **Headers**, for example `Content-Type` includes `application/json`
- **Schema and data types**, for example every book has exactly `id`, `name`, `type`, `available` with the correct types
- **Business rules**, for example book `type` is `fiction` or `non-fiction`, returned count equals `limit`, ids are unique
- **Negative checks**: an error message is returned and is not empty, and no `accessToken`, order or book data is leaked
- **Empty body** validation for `204 No Content` responses
- **Data integrity**: the order `id` equals the stored `orderId`, and the updated name is persisted

**Example: validating the books list (TC06)**

```javascript
const books = pm.response.json();

pm.test("Every book has id, name, type and available", () => {
    books.forEach(book => {
        pm.expect(book).to.have.all.keys("id", "name", "type", "available");
        pm.expect(book.id).to.be.a("number");
        pm.expect(book.name).to.be.a("string").and.not.empty;
        pm.expect(book.type).to.be.oneOf(["fiction", "non-fiction"]);
        pm.expect(book.available).to.be.a("boolean");
    });
});
```

**Example: saving a value for the next request (TC20)**

```javascript
const jsonData = pm.response.json();
if (jsonData.orderId) {
    pm.collectionVariables.set("oederId", jsonData.orderId);
}
```

---

## 🔗 Collection Variables

| Variable | Purpose | Set by |
|----------|---------|--------|
| `domain` | Base URL of the API | **Manually**: `https://simple-books-api.click` |
| `clientName` | Random client name | Pre-request script (TC02) |
| `clientEMAIL` | Random client email | Pre-request script (TC02) |
| `accessToken` | Bearer token used by all `/orders` requests | Test script (TC02) |
| `bookId` | First available book | Test script (TC06) |
| `oederId` | Id of the created order | Test script (TC20) |

---

##  How to Run

### Prerequisites
- [Postman](https://www.postman.com/downloads/) (desktop app or web)

### Steps

1. **Clone** this repository or download the files.
2. Open Postman and click **Import**, then select  
   `API_ManualTesting_BooksLibraryWebsite_APIs_Collection_Postman.json`.
3. Open the collection, go to the **Variables** tab and set:
   ```
   domain = https://simple-books-api.click
   ```
4. Run the collection in the **correct order** (see below) using the **Collection Runner**, or send requests one by one.

###  Recommended Execution Order

The requests depend on each other, so run the folders in this order:

1. **Status & Authentication**: registers a client and stores `accessToken`
2. **Books**: stores an available `bookId`
3. **Orders**: uses the token and `bookId` to create, update and delete an order

> In the Collection Runner, drag the folders into the order above (or run each folder separately) before starting the run.

---

##  Tools & Technologies

| Tool | Usage |
|------|-------|
| **Postman** | API client, test scripting and Collection Runner |
| **JavaScript (Chai assertions)** | Test scripts and pre-request scripts |
| **REST / JSON** | API architecture and data format |
| **Bearer Token Auth** | Authentication of protected endpoints |

---

##  Testing Techniques Applied

- Functional testing
- Positive and negative testing
- Boundary Value Analysis (`limit`: 0, 1, 20, 21)
- Equivalence partitioning (valid and invalid `type` values)
- Authentication and authorization testing
- Input validation (missing fields, wrong data types)
- CRUD lifecycle and data persistence testing
- Data-driven setup through dynamic variables

---

##  Repository Contents

```
├── API_ManualTesting_BooksLibraryWebsite_APIs_Collection_Postman.json   # Postman collection
└── README.md
```

---

##  Future Improvements

- Add a Postman **Environment** file (instead of collection variables) for easier switching between setups
- Run the collection from the command line with **Newman** and generate an HTML report
- Add a CI pipeline (GitHub Actions) to run the tests automatically
- Add JSON Schema validation for all responses

---

##  Author

**Ahmed Ayman**  
Software QA Engineer | Manual, API & Automation Testing | ECPC Finalist | Competitive Programmer

- GitHub: [ahmedayman2001](https://github.com/ahmedayman2001)

---

