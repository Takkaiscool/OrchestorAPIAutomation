# Skill: swagger-to-jmeter-testplan-generator

## Description

AI skill that converts a Swagger/OpenAPI specification into a **complete JMeter performance test plan (.jmx)**.

The skill automatically discovers all API endpoints and generates performance-ready HTTP samplers with assertions, authentication support, and structured JMeter configuration.

---

# Purpose

Generate a **fully functional Apache JMeter `.jmx` test plan** from a user-provided **Swagger/OpenAPI URL**.

The skill must automatically:

* Discover all endpoints
* Create HTTP samplers for each endpoint
* Configure performance test structure
* Add assertions and authentication placeholders
* Output ready-to-run performance testing artifacts

---

# Required Input

* **swagger_url**
  URL pointing to a Swagger/OpenAPI specification.

Example:

```
https://api.example.com/swagger/v1/swagger.json
```

---

# Optional Inputs

| Parameter         | Description                      | Default      |
| ----------------- | -------------------------------- | ------------ |
| base_url          | Override base API host           | from Swagger |
| threads           | Number of virtual users          | 10           |
| ramp_up           | Ramp-up time (seconds)           | 30           |
| loops             | Loop count per user              | 1            |
| throughput_target | Target requests/sec              | none         |
| timeout           | HTTP timeout                     | 30000        |
| auth_type         | bearer | apiKey | basic | oauth2 | none         |
| auth_token        | token placeholder or variable    | none         |

---

# Execution Workflow

## Step 1 — Parse Swagger Specification

Download and parse the Swagger/OpenAPI document.

Extract:

* API title and version
* Base server URL
* All paths
* HTTP methods
* Query parameters
* Path parameters
* Header parameters
* Request body schemas
* Security schemes
* Endpoint tags

If the Swagger cannot be parsed:

Return:

```
ERROR: Invalid or unreachable Swagger specification.
```

Stop execution.

---

# Step 2 — Build JMeter Test Plan Structure

Create the following hierarchy:

```
Test Plan
 ├── Thread Group
 │    ├── HTTP Request Defaults
 │    ├── HTTP Header Manager
 │    ├── Cookie Manager
 │    ├── CSV Data Set Config (optional)
 │    ├── Endpoint Samplers
 │    └── Assertions
 └── Result Collectors
```

Configuration:

Thread Group

* Threads = ${threads}
* Ramp-up = ${ramp_up}
* Loop count = ${loops}

HTTP Defaults

* Protocol = http/https
* Server Name = base_url
* Timeout = ${timeout}

Add:

* **View Results Tree**
* **Summary Report**
* **Aggregate Report**

---

# Step 3 — Generate Endpoint Samplers

For **each path + method combination** in Swagger:

Create one HTTP sampler.

Sampler configuration:

Name

```
<METHOD> <PATH>
```

Example:

```
GET /orders/{id}
```

Sampler configuration rules:

### HTTP Method

Use method defined in Swagger.

### Path

Include path parameters as variables.

Example:

```
/orders/${orderId}
```

### Query Parameters

Add all query parameters as JMeter parameters.

### Headers

Include required headers.

Common headers:

```
Content-Type: application/json
Accept: application/json
```

### Request Body

If request body exists:

* Generate JSON payload from example
* If example missing → generate schema-based sample

Example:

```json
{
  "name": "sampleName",
  "status": "ACTIVE"
}
```

---

# Step 4 — Assertions

Add assertions to every sampler.

### Status Code Assertion

Expected:

```
2xx
```

Example assertion:

```
Response Code = 200 OR 201
```

---

### Response Time Assertion

If threshold configured:

```
Response Time < threshold
```

---

### Response Content Assertion

Validate at least one expected field when response schema exists.

Example:

```
$.id exists
```

---

# Step 5 — Authentication Support

Support the following security schemes.

## Bearer Token

Add header:

```
Authorization: Bearer ${AUTH_TOKEN}
```

---

## API Key

Header example:

```
x-api-key: ${API_KEY}
```

---

## Basic Authentication

Use JMeter Authorization Manager.

```
username=${API_USER}
password=${API_PASSWORD}
```

---

## OAuth2 Token Passthrough

Token passed via variable:

```
Authorization: Bearer ${OAUTH_TOKEN}
```

---

# Step 6 — Correlation Handling

When response contains reusable fields:

Examples:

* id
* token
* referenceId
* orderId

Add **JSON Extractor**.

Example:

```
Variable name: orderId
JSON Path: $.id
```

Reuse extracted variable in downstream requests.

Example:

```
/orders/${orderId}
```

---

# Step 7 — Optional CSV Data Support

If request fields appear dynamic:

Create CSV Data Set Config.

Example CSV:

```
users.csv
```

```
username,password
user1,pass1
user2,pass2
```

Use variables:

```
${username}
${password}
```

---

# Step 8 — Output Artifacts

Generate the following files.

## 1. JMeter Test Plan

```
performance/<service-name>-all-endpoints.jmx
```

Contains all generated samplers.

---

## 2. Performance README

```
performance/README.md
```

Example content:

Run command:

```
jmeter -n -t performance/service-all-endpoints.jmx \
-JAUTH_TOKEN=your_token \
-l results.jtl
```

Variables to configure:

```
AUTH_TOKEN
API_KEY
API_USER
API_PASSWORD
```

---

## 3. Endpoint Coverage Table

| Method | Endpoint     | Sampler Created |
| ------ | ------------ | --------------- |
| GET    | /orders      | Yes             |
| POST   | /orders      | Yes             |
| GET    | /orders/{id} | Yes             |

---

# Defaults

If parameters are not provided:

```
threads = 10
ramp_up = 30
loops = 1
timeout = 30000
```

---

# Validation Checklist

Before returning artifacts verify:

* Every Swagger endpoint has a sampler
* All path parameters are included
* JSON bodies are valid
* Authentication uses variables
* No credentials hardcoded
* JMX file is valid XML
* Thread group configured correctly

---

# Error Handling

If validation fails return:

```
GENERATION FAILED
Endpoint: <method path>
Reason: <error description>
```

Example:

```
GENERATION FAILED
Endpoint: POST /orders
Reason: Missing required body schema
```

---

# Success Output

Return:

```
SUCCESS
Generated artifacts:

performance/service-all-endpoints.jmx
performance/README.md
performance/coverage-table.md
```

---

# Usage Example

User prompt:

```
Generate JMeter test plan from:

https://api.example.com/swagger.json

threads=50
ramp_up=60
loops=10
auth_type=bearer
```

Expected output:

* JMX performance test plan
* README with execution instructions
* Endpoint coverage report

---
