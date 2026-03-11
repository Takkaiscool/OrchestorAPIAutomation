# Skill: swagger-to-jmeter-performance-engine

## Description

AI skill that converts a Swagger/OpenAPI specification into a **fully structured Apache JMeter performance test suite**.

The engine automatically:

* Parses Swagger/OpenAPI
* Generates JMeter `.jmx` test plans
* Builds HTTP samplers for every endpoint
* Performs **automatic correlation detection**
* Generates **synthetic test datasets**
* Adds assertions and authentication
* Produces ready-to-run performance artifacts

The output is a **complete performance testing package**.

---

# Purpose

Generate a **production-ready JMeter performance testing suite** from a Swagger/OpenAPI specification.

The skill must:

* Discover all API endpoints
* Build structured JMeter test plans
* Automatically detect reusable response values
* Generate dynamic test data
* Produce CSV datasets for load testing
* Output validated JMeter artifacts

---

# Required Input

Parameter:

```text
swagger_url
```

Example:

```text
https://api.example.com/swagger/v1/swagger.json
```

---

# Optional Inputs

| Parameter         | Description                      | Default      |
| ----------------- | -------------------------------- | ------------ |
| base_url          | Override API host                | From Swagger |
| threads           | Virtual users                    | 10           |
| ramp_up           | Ramp-up seconds                  | 30           |
| loops             | Loop count                       | 1            |
| throughput_target | Requests/sec                     | none         |
| timeout           | HTTP timeout                     | 30000        |
| dataset_size      | Synthetic test rows              | 100          |
| auth_type         | bearer / apiKey / basic / oauth2 | none         |

---

# Execution Workflow

---

# Step 1 — Swagger Parsing Engine

Download and parse the Swagger/OpenAPI document.

Extract:

* service name
* base server URL
* all endpoints
* HTTP methods
* path parameters
* query parameters
* headers
* request body schemas
* response schemas
* security definitions
* endpoint tags

Create internal endpoint map:

```text
Endpoint {
 method
 path
 parameters
 requestBody
 responseSchema
 auth
 tag
}
```

If parsing fails:

```text
ERROR: Unable to parse Swagger specification
```

Stop execution.

---

# Step 2 — Build JMeter Test Plan Skeleton

Create hierarchy:

```text
Test Plan
 ├── User Defined Variables
 ├── Thread Group
 │    ├── HTTP Request Defaults
 │    ├── HTTP Header Manager
 │    ├── Cookie Manager
 │    ├── CSV Data Set Config
 │    ├── Endpoint Samplers
 │    ├── Assertions
 │    └── Extractors
 └── Listeners
```

Configuration:

Thread Group

```text
Threads = ${threads}
Ramp-up = ${ramp_up}
Loops = ${loops}
```

HTTP Defaults

```text
Protocol = https
Server = ${BASE_URL}
Timeout = ${timeout}
```

Listeners

* Summary Report
* Aggregate Report
* Backend Listener (optional)

---

# Step 3 — Synthetic Test Data Generation

When request schemas contain fields likely needing dynamic values, generate datasets.

Supported data types:

| Schema Type | Generated Data        |
| ----------- | --------------------- |
| string      | random strings        |
| email       | synthetic emails      |
| uuid        | generated UUID        |
| number      | random numeric        |
| enum        | random enum selection |
| date        | generated timestamp   |

Example generated dataset:

```text
users.csv
```

```text
username,email,password
user1,user1@test.com,Pass123
user2,user2@test.com,Pass123
```

CSV Data Config:

```text
Filename: users.csv
Variables: username,email,password
Recycle: true
```

Variables used inside samplers:

```text
${username}
${email}
```

Dataset size controlled by:

```text
dataset_size
```

---

# Step 4 — Endpoint Sampler Generation

For every endpoint:

Create one HTTP sampler.

Sampler name:

```text
<METHOD> <PATH>
```

Example:

```text
POST /orders
```

---

## Path Parameters

Convert to variables.

Example:

```text
/orders/${orderId}
```

---

## Query Parameters

Add to parameter section.

Example:

```text
status=${status}
page=${page}
```

---

## Request Body Generation

If Swagger example exists → use example.

Otherwise generate payload from schema.

Example generated payload:

```json
{
  "name": "${username}",
  "email": "${email}",
  "status": "ACTIVE"
}
```

---

# Step 5 — Automatic Correlation Engine

Detect dynamic response fields that must be reused.

Correlation candidates include:

| Field Pattern | Reason                  |
| ------------- | ----------------------- |
| id            | resource identifiers    |
| token         | authentication tokens   |
| sessionId     | session reuse           |
| orderId       | workflow chaining       |
| referenceId   | transaction correlation |

When detected:

Add **JSON Extractor**.

Example:

```text
Variable Name: orderId
JSON Path: $.id
```

Store as:

```text
${orderId}
```

Reuse variable in downstream requests.

Example:

```text
GET /orders/${orderId}
```

---

## Correlation Detection Rules

Field is correlated if:

* Appears in response
* Appears in later request path/body
* Matches id/token/reference pattern

If correlation detected:

Automatically link samplers.

---

# Step 6 — Authentication Handling

Support:

## Bearer Token

Header:

```text
Authorization: Bearer ${AUTH_TOKEN}
```

---

## API Key

```text
x-api-key: ${API_KEY}
```

---

## Basic Auth

JMeter Authorization Manager.

```text
username=${API_USER}
password=${API_PASSWORD}
```

---

## OAuth2 Token Passthrough

```text
Authorization: Bearer ${OAUTH_TOKEN}
```

All credentials remain variable-driven.

Never hardcode secrets.

---

# Step 7 — Assertions

Each sampler receives:

### Status Assertion

```text
Expected response: 200–299
```

---

### Response Time Assertion

```text
Response time < ${MAX_RESPONSE_TIME}
```

---

### JSON Validation Assertion

Verify expected fields exist.

Example:

```text
$.id exists
$.status exists
```

---

# Step 8 — Result Collectors

Add listeners:

* Summary Report
* Aggregate Report
* Response Time Graph
* Backend Listener (optional)

Backend Listener supports:

* InfluxDB
* Prometheus exporters

---

# Step 9 — Output Artifacts

Generate folder:

```text
performance/
```

---

## Artifact 1 — JMeter Test Plan

```text
performance/<service>-all-endpoints.jmx
```

Contains:

* Thread group
* Samplers
* Correlation extractors
* Assertions
* CSV configs

---

## Artifact 2 — Test Data

```text
performance/datasets/*.csv
```

Example:

```text
users.csv
orders.csv
accounts.csv
```

---

## Artifact 3 — Performance README

```text
performance/README.md
```

Example run command:

```bash
jmeter -n \
-t performance/service-all-endpoints.jmx \
-JBASE_URL=https://api.example.com \
-JAUTH_TOKEN=token \
-l results.jtl
```

---

## Artifact 4 — Endpoint Coverage Report

```text
performance/coverage.md
```

Example:

| Method | Endpoint     | Sampler |
| ------ | ------------ | ------- |
| GET    | /orders      | Yes     |
| POST   | /orders      | Yes     |
| GET    | /orders/{id} | Yes     |

---

# Defaults

If user does not specify configuration:

```text
threads = 10
ramp_up = 30
loops = 1
timeout = 30000
dataset_size = 100
```

---

# Validation Checklist

Before returning artifacts verify:

✔ every Swagger endpoint has sampler
✔ path parameters replaced by variables
✔ JSON payloads valid
✔ correlation variables generated
✔ datasets generated when required
✔ authentication uses variables
✔ JMX is valid XML

---

# Error Handling

If validation fails:

```text
GENERATION FAILED
Endpoint: <METHOD PATH>
Reason: <description>
```

Example:

```text
GENERATION FAILED
Endpoint: POST /users
Reason: Request schema missing
```

---

# Success Output

Return:

```text
SUCCESS
Artifacts generated:

performance/service-all-endpoints.jmx
performance/datasets/
performance/coverage.md
performance/README.md
```

---

# Usage Example

User request:

```text
Generate JMeter performance suite

swagger_url=https://api.example.com/swagger.json
threads=50
ramp_up=60
loops=10
dataset_size=500
auth_type=bearer
```

Generated output:

* JMeter performance test plan
* Correlation-enabled workflow
* Synthetic datasets
* Endpoint coverage
* Execution instructions

---
