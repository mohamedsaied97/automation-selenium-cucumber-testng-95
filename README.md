# AutomationExercise.com – Web Automation Testing Project

Automated test suite for [automationexercise.com](https://automationexercise.com) built with
**Java + Selenium WebDriver + TestNG**, following the **Page Object Model (POM)**.

## Tech Stack
| Tool | Version | Purpose |
|---|---|---|
| Java | 21 | Language |
| Selenium WebDriver | 4.18.1 | Browser automation |
| TestNG | 7.9.0 | Test runner / assertions |
| Maven | - | Build & dependency management |
| Microsoft Edge | - | Browser under test |

## Project Structure
```
src/test/java/
  base/
    DriverManager.java        -> the only place that opens/closes the browser
  pages/
    HomePage.java
    SignupLoginPage.java
    AccountInformationPage.java
    AccountCreatedPage.java
    ProductsPage.java
    ProductDetailsPage.java
    CartPage.java
    CheckoutPage.java
    PaymentPage.java
    ContactUsPage.java
    TestCasesPage.java
  tests/
    RegistrationTest.java
    LoginTest.java
    CartCheckoutTest.java
    ContactUsTest.java
    TestCasesTest.java
    ProductsTest.java
    LoginFailureTest.java
    NegativeRegistrationTest.java
    NegativeLoginTest.java
    NegativeCheckoutTest.java
    NegativeContactUsTest.java
    NegativeSearchTest.java
    NegativePaymentTest.java
    NegativeCheckoutWithoutLoginTest.java
```

## Design Notes (Page Object Model)
- **DriverManager** is the only class that ever calls `new EdgeDriver()`. Every
  test/page goes through `DriverManager.driver`, which is what makes it easy
  to switch browsers (this project was originally built on Chrome and moved
  to Edge by changing a single file) or add parallel execution later.
- **Page classes** (`pages/`) hold locators and simple actions only (click,
  type, read text) — never assertions. Assertions live in the `@Test`
  methods, keeping "how to interact with a page" separate from "what we're
  verifying."
- **Implicit wait** (10 seconds) is set once in `DriverManager`, so page
  transitions on this ad-heavy live site have time to settle without
  scattering `Thread.sleep()` everywhere.
- A small helper, `clickSafely()` (in `ProductsPage` and `TestCasesPage`),
  falls back to a JavaScript click when a normal click is blocked by an ad
  iframe overlapping a button — a real issue encountered while testing
  against the live site.

## Scenarios Covered

### Core (8/8)
| # | Scenario | Test class |
|---|---|---|
| 1 | Register successfully | `RegistrationTest` |
| 2 | Login successfully | `LoginTest` |
| 3 | Add to cart + complete checkout | `CartCheckoutTest` |
| 4 | Submit Contact Us form | `ContactUsTest` |
| 5 | Test Cases page loads | `TestCasesTest` |
| 6 | Browse products + view details | `ProductsTest` |
| 7 | Logout | `LoginTest` |
| 8 | Login fails with incorrect data | `LoginFailureTest` |

### Negative Scenarios (7/7)
| Scenario | Test class |
|---|---|
| Signup with an email that already exists | `NegativeRegistrationTest` |
| Login with correct email but wrong password | `NegativeLoginTest` |
| Checkout attempted with an empty cart | `NegativeCheckoutTest` |
| Contact Us submitted with empty required fields | `NegativeContactUsTest` |
| Search with a keyword that matches nothing | `NegativeSearchTest` |
| Payment submitted with empty card fields | `NegativePaymentTest` |
| Checkout attempted while not logged in | `NegativeCheckoutWithoutLoginTest` |

## How to Run
Each test class can be run individually from IntelliJ (right-click the
class → **Run**), or all together via Maven:
```bash
mvn clean test
```

## Notes on Testing Against a Live Site
- Every test that needs an account registers a **fresh, timestamped email**
  on each run, since the site is live and re-using an email would collide
  with "already registered" errors.
- The site has no real payment gateway, so the "invalid payment info"
  negative test relies on the browser's own required-field validation
  (empty fields) rather than a badly-formatted card number, which the site
  accepts anyway.
- An implicit wait of 10 seconds is used throughout to absorb page-load and
  ad-related delays on the live site.
