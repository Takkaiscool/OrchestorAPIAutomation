# Java Selenium to Playwright TypeScript migration map

## Core API mappings

- `WebDriver driver` -> `Page page`
- `driver.get(url)` -> `await page.goto(url)`
- `driver.findElement(By.cssSelector(...))` -> `page.locator('...')`
- `driver.findElements(...)` -> `page.locator(...).all()` or count/assertion patterns
- `WebElement.click()` -> `await locator.click()`
- `WebElement.sendKeys()` -> `await locator.fill()` or `pressSequentially()` when typing behavior matters
- `getText()` -> `await locator.textContent()` or `await expect(locator).toHaveText(...)`
- `isDisplayed()` -> `await expect(locator).toBeVisible()`
- `Actions` chains -> `locator.hover()`, `dragTo()`, keyboard helpers, or `page.mouse`
- `Select` -> `await locator.selectOption()`
- `JavascriptExecutor` -> `await page.evaluate()` only when Playwright has no native alternative

## Wait strategy

Prefer Playwright assertions and actionability checks.

- `WebDriverWait(...).until(visibilityOf(...))` -> `await expect(locator).toBeVisible()`
- `elementToBeClickable` -> usually `await locator.click()` is enough; add `await expect(locator).toBeEnabled()` if intent matters
- `presenceOfElementLocated` -> `await expect(locator).toHaveCount(1)` or `toBeAttached()`
- `Thread.sleep(...)` -> replace with network, navigation, response, or UI-state waits

## Assertion strategy

- `assertEquals(expected, actual)` -> `expect(actual).toBe(expected)` or locator-specific matchers
- `assertTrue(element.isDisplayed())` -> `await expect(locator).toBeVisible()`
- `assertFalse(...)` -> `await expect(locator).not.to...`

## Common pitfalls

1. Do not wrap every action with manual waits.
2. Do not keep the Selenium base-page pattern if it only proxies driver calls.
3. Do not default to XPath when Playwright role or text locators are available.
4. Do not carry over stale-element recovery code; Playwright locators re-query automatically.
