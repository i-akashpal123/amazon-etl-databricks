# Selenium + Java Framework for Google Search Automation

## 1) Tech stack
- Java 17+
- Maven
- Selenium WebDriver
- TestNG (or JUnit 5)
- WebDriverManager
- ExtentReports (optional)
- Owner/Typesafe config (optional)

## 2) Suggested project structure
```text
google-search-framework/
  pom.xml
  src/
    main/java/
      core/
        DriverFactory.java
        ConfigManager.java
        WaitUtils.java
      pages/
        GoogleHomePage.java
        GoogleResultsPage.java
      utils/
        ScreenshotUtils.java
        TestData.java
    test/java/
      tests/
        GoogleSearchTests.java
      listeners/
        TestListener.java
  testng.xml
```

## 3) Maven dependencies
Use these core dependencies in `pom.xml`:
- `org.seleniumhq.selenium:selenium-java`
- `io.github.bonigarcia:webdrivermanager`
- `org.testng:testng`
- `com.aventstack:extentreports` (optional)

## 4) Core design (framework layers)
- **Base layer**: Driver setup/teardown, browser options, timeouts.
- **Page Object layer**: One class per page; keep locators + page actions only.
- **Test layer**: Assertions and workflow validation.
- **Utilities layer**: waits, screenshots, logging, reusable helpers.
- **Reporting layer**: test reports and artifacts.

## 5) Implementation blueprint

### 5.1 DriverFactory (ThreadLocal for parallel runs)
```java
public final class DriverFactory {
    private static final ThreadLocal<WebDriver> DRIVER = new ThreadLocal<>();

    public static void initDriver(String browser) {
        if (browser.equalsIgnoreCase("chrome")) {
            WebDriverManager.chromedriver().setup();
            ChromeOptions options = new ChromeOptions();
            options.addArguments("--start-maximized");
            DRIVER.set(new ChromeDriver(options));
        }
    }

    public static WebDriver getDriver() {
        return DRIVER.get();
    }

    public static void quitDriver() {
        if (DRIVER.get() != null) {
            DRIVER.get().quit();
            DRIVER.remove();
        }
    }
}
```

### 5.2 Base page utilities
- Wrap explicit waits (`WebDriverWait`) in helper methods.
- Avoid hard sleeps (`Thread.sleep`).
- Add helper methods like `click`, `type`, `visible`, `present`, `jsClick`.

### 5.3 Page Objects
`GoogleHomePage`:
- Locators: search input, search button.
- Methods: `open()`, `search(String keyword)`.

`GoogleResultsPage`:
- Locators: result stats block, top result title/link.
- Methods: `isLoaded()`, `containsResult(String text)`.

### 5.4 Test class
- `@BeforeMethod`: initialize driver.
- `@Test`: open Google, search keyword, verify result page loaded and expected text exists.
- `@AfterMethod`: quit driver.

## 6) Sample test flow
```java
@Test
public void searchShouldReturnRelevantResults() {
    GoogleHomePage home = new GoogleHomePage(DriverFactory.getDriver());
    GoogleResultsPage results = home.open().search("Selenium WebDriver");

    Assert.assertTrue(results.isLoaded(), "Results page should load");
    Assert.assertTrue(results.containsResult("selenium.dev"), "Expected domain should appear");
}
```

## 7) Stability best practices for Google UI
- Handle cookie/privacy consent dialog conditionally.
- Prefer robust locators (`name='q'`, semantic attributes) over brittle XPath.
- Use explicit waits for dynamic elements.
- Keep assertions business-focused (page loaded, expected domain present).
- Run headless in CI and capture screenshot on failure.

## 8) CI/CD setup
- Run tests with Maven Surefire.
- Export HTML reports + screenshots as artifacts.
- Run a smoke suite on every pull request, full regression nightly.

## 9) Common pitfalls
- CAPTCHA or bot detection can appear with aggressive runs.
- Locale differences can alter element text and consent flow.
- Overly strict locators break frequently on Google UI changes.

## 10) Next enhancements
- Add cross-browser support (Firefox/Edge).
- Add data-driven tests via TestNG DataProvider.
- Integrate remote execution (Selenium Grid/Selenoid).
- Add retry analyzer for flaky tests.
- Add tags/groups for smoke/regression suites.
