# Specific Rules for Selenium WebDriver

> Applies when automating browsers with Java and Selenium WebDriver.

## 1. Locator Priority Order

Strictly follow this order to ensure speed and stability:

1. `id` — Fastest, most unique
2. `data-testid` / `data-test` / `data-qa` — Dedicated test attributes
3. `name` — Standard HTML attribute
4. `cssSelector` — Flexible, fast
5. `xpath` — Last resort

Correct example:
```java
driver.findElement(By.id("login-btn"));
driver.findElement(By.cssSelector("button[data-testid='submit-btn']"));
driver.findElement(By.name("username"));
```

Incorrect example — position-dependent structural XPath:
```java
// FORBIDDEN: Absolute XPath based on DOM structure
driver.findElement(By.xpath("//div[3]/div[2]/form/div[1]/button"));
```

## 2. Wait Strategy

**FORBIDDEN:**
- `Thread.sleep()` — In any case.
- Any method that fixes wait time.

**USE:**
- Java Explicit Waits with `WebDriverWait` + `ExpectedConditions`:

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// Wait for element to be visible
WebElement element = wait.until(
    ExpectedConditions.visibilityOfElementLocated(By.id("profile"))
);

// Wait for element to be clickable
wait.until(ExpectedConditions.elementToBeClickable(By.id("submit-btn")));

// Wait for text to appear
wait.until(ExpectedConditions.textToBePresentInElementLocated(
    By.id("message"), "Success"
));

// Wait for URL redirection
wait.until(ExpectedConditions.urlContains("/dashboard"));
```

- You can create a custom `FluentWait` if flexible polling is needed:
```java
Wait<WebDriver> fluentWait = new FluentWait<>(driver)
    .withTimeout(Duration.ofSeconds(15))
    .pollingEvery(Duration.ofMillis(500))
    .ignoring(NoSuchElementException.class);
```

## 3. Browser Setup

- **Viewport:** Set desktop viewport (`1920x1080`) when debugging:
  ```java
  driver.manage().window().setSize(new Dimension(1920, 1080));
  ```
- **Headed Mode:** Mandatory when debugging (do not set `--headless`).
- **Headless Mode:** Only use when tests have passed in headed mode or in CI/CD.

## 4. Test Structure (TestNG)

```java
public class LoginTest extends BaseTest {

    @BeforeMethod
    public void setUp() {
        // Navigate, setup data...
    }

    @Test(groups = {"smoke", "regression"})
    public void testLoginWithValidCredentials() {
        // Arrange
        LoginPage loginPage = new LoginPage(driver);
        String email = DataGenerator.generateEmail("login");

        // Act
        loginPage.login(email, "ValidPass@123");

        // Assert
        DashboardPage dashboard = new DashboardPage(driver);
        Assert.assertTrue(dashboard.isDisplayed(),
            "Dashboard should be displayed after successful login");
    }

    @AfterMethod
    public void tearDown() {
        // Cleanup...
    }
}
```

## 5. Assertions

- Use TestNG Assertions (`Assert.assertEquals`, `Assert.assertTrue`...).
- Always add a **descriptive message** to the assertion:
  ```java
  Assert.assertEquals(actualTitle, "Dashboard", "Page title should be Dashboard");
  Assert.assertTrue(element.isDisplayed(), "Element should be displayed on the page");
  ```
- Each test method must have at least **1 assertion** at the end.
