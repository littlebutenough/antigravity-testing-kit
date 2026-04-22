# Specific Rules for Appium (Mobile Automation)

> Applies when automating mobile applications with Java and Appium.

## 1. Locator Priority Order

Use native locator strategies for each platform (iOS / Android) instead of web equivalents:

1. `accessibility id` — Cross-platform, most stable
2. `resource-id` (Android) — Native Android attribute
3. `id` — Common ID
4. `iOS predicate string` (iOS) — Fast, iOS-specific
5. `iOS class chain` (iOS) — iOS structure query
6. `xpath` — Last resort (slowest)

Correct example:
```java
// Accessibility id — Cross-platform, always prioritized
driver.findElement(AppiumBy.accessibilityId("login_button"));

// Android — resource-id
driver.findElement(AppiumBy.id("com.application.xyz:id/login_button"));

// iOS — Predicate String (fast)
driver.findElement(AppiumBy.iOSNsPredicateString("label == 'Login'"));

// iOS — Class Chain
driver.findElement(AppiumBy.iOSClassChain(
    "**/XCUIElementTypeButton[`label == 'Login'`]"
));
```

## 2. FORBIDDEN

- Absolute XPath based on position — any small layout change will cause failure:
  ```java
  // FORBIDDEN:
  driver.findElement(By.xpath(
      "//android.widget.FrameLayout[1]/android.widget.LinearLayout[2]/android.widget.Button[1]"
  ));
  ```
- Querying off-screen elements without scrolling first.
- Interacting with disabled elements without checking their status.
- Hardcoding animation wait times.

## 3. Wait Strategy

**FORBIDDEN:**
- `Thread.sleep()` — In any case.

**USE:**
- Explicit Waits with `WebDriverWait`:
  ```java
  WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(15));

  // Wait for element to be visible
  wait.until(ExpectedConditions.visibilityOfElementLocated(
      AppiumBy.accessibilityId("welcome_text")
  ));

  // Wait for element to be clickable
  wait.until(ExpectedConditions.elementToBeClickable(
      AppiumBy.accessibilityId("submit_button")
  ));
  ```

- Handling scroll to element:
  ```java
  // Android — UiScrollable
  driver.findElement(AppiumBy.androidUIAutomator(
      "new UiScrollable(new UiSelector().scrollable(true))" +
      ".scrollIntoView(new UiSelector().text(\"Submit\"))"
  ));
  ```

## 4. Test Structure (TestNG)

```java
public class LoginMobileTest extends BaseTest {

    @BeforeMethod
    public void setUp() {
        // Initialize driver, capabilities...
    }

    @Test(groups = {"mobile", "regression"})
    public void testLoginSuccess() {
        // Arrange
        LoginScreen loginScreen = new LoginScreen(driver);
        String email = DataGenerator.generateEmail("loginMobile");

        // Act
        loginScreen.login(email, "ValidPass@123");

        // Assert
        HomeScreen homeScreen = new HomeScreen(driver);
        Assert.assertTrue(homeScreen.isWelcomeDisplayed(),
            "Home screen should be displayed after login");
    }
}
```

Note:
- Mobile uses **Screen Objects** (equivalent to Page Objects) — `Screen` suffix.
- Examples: `LoginScreen.java`, `HomeScreen.java`, `SettingsScreen.java`.

## 5. Mobile Testing Specifics

- **Screen Rotation:** Test both portrait and landscape if the app supports it:
  ```java
  driver.rotate(ScreenOrientation.LANDSCAPE);
  ```
- **Background/Foreground:** Test the app when moving to the background and returning:
  ```java
  driver.runAppInBackground(Duration.ofSeconds(5));
  ```
- **Push Notifications:** Verify notifications using Appium notification listener.
- **Permission Dialogs:** Handle permission request dialogs (camera, location...):
  ```java
  // Android — Auto-grant permissions in capabilities
  capabilities.setCapability("autoGrantPermissions", true);
  ```
