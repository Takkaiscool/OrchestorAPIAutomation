# Project patterns for Playwright API migration

## Example fixture

```ts
import { APIRequestContext, request as playwrightRequest, test as base } from '@playwright/test';

export const test = base.extend<{ api: APIRequestContext }>({
  api: async ({}, use) => {
    const api = await playwrightRequest.newContext({
      baseURL: process.env.API_BASE_URL,
      extraHTTPHeaders: {
        Authorization: `Bearer ${process.env.API_TOKEN}`,
        Accept: 'application/json',
      },
    });

    await use(api);
    await api.dispose();
  },
});
```

## Example assertion flow

```ts
const response = await api.post('/orders', { data: payload });
expect(response.status()).toBe(201);
const body = await response.json();
expect(body.id).toBeTruthy();
expect(body.status).toBe('CREATED');
```
