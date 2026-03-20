# Page object patterns for Playwright TypeScript

## Recommended shape

```ts
import { expect, Locator, Page } from '@playwright/test';

export class LoginPage {
  readonly page: Page;
  readonly username: Locator;
  readonly password: Locator;
  readonly submit: Locator;

  constructor(page: Page) {
    this.page = page;
    this.username = page.getByLabel('Username');
    this.password = page.getByLabel('Password');
    this.submit = page.getByRole('button', { name: 'Sign in' });
  }

  async open() {
    await this.page.goto('/login');
  }

  async login(user: string, pass: string) {
    await this.username.fill(user);
    await this.password.fill(pass);
    await this.submit.click();
  }

  async assertLoggedIn() {
    await expect(this.page.getByRole('banner')).toContainText('Welcome');
  }
}
```

## Conversion guidance

- Keep locators as readonly properties when they are reused.
- Expose user actions and assertions, not low-level getter methods.
- Prefer fixtures for shared authenticated state over inheritance-based base test classes.
