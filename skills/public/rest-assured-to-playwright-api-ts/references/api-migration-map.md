# Rest Assured to Playwright API migration map

## Core mapping

- `given().baseUri(...).header(...).body(...)` -> `await request.post(url, { headers, data })`
- `queryParam` / `pathParam` -> build the final URL explicitly or use helper functions
- `auth().oauth2(token)` -> set `Authorization` header in a shared request context
- `multiPart(...)` -> use Playwright multipart request options
- `then().statusCode(200)` -> `expect(response.status()).toBe(200)`
- `then().body('path', equalTo(...))` -> parse JSON and assert with `expect(json.path).toBe(...)`

## Fixture pattern

Create one request fixture per auth model or environment. Example responsibilities:

- inject base URL from config
- add default headers
- fetch or mint tokens
- expose domain-specific client helpers when repeated calls are common

## Common pitfalls

1. Do not mimic every `given/when/then` chain with nested helpers.
2. Do not hide the final URL or payload so deeply that tests lose readability.
3. Do not parse JSON multiple times; read once and assert from the parsed object.
4. Do not keep static shared mutable test data between specs.
