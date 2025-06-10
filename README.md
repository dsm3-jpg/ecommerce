// LoginPage.java
package pages;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;

public class LoginPage {
    WebDriver driver;

    @FindBy(id = "login2")
    WebElement loginLink;

    @FindBy(id = "loginusername")
    WebElement usernameField;

    @FindBy(id = "loginpassword")
    WebElement passwordField;

    @FindBy(xpath = "//button[text()='Log in']")
    WebElement loginButton;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public void login(String username, String password) {
        loginLink.click();
        usernameField.sendKeys(username);
        passwordField.sendKeys(password);
        loginButton.click();
    }
}

// HomePage.java
package pages;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.PageFactory;

public class HomePage {
    WebDriver driver;

    public HomePage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public String getPageTitle() {
        return driver.getTitle();
    }
}

// ProductPage.java
package pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class ProductPage {
    WebDriver driver;

    public ProductPage(WebDriver driver) {
        this.driver = driver;
    }

    public void addProductToCart(String productName) {
        driver.findElement(By.linkText(productName)).click();
        driver.findElement(By.linkText("Add to cart"))
              .click();
        driver.switchTo().alert().accept();
    }
}

// CartPage.java
package pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class CartPage {
    WebDriver driver;

    public CartPage(WebDriver driver) {
        this.driver = driver;
    }

    public boolean isProductInCart(String productName) {
        return driver.getPageSource().contains(productName);
    }
}

// CheckoutPage.java
package pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class CheckoutPage {
    WebDriver driver;

    public CheckoutPage(WebDriver driver) {
        this.driver = driver;
    }

    public void placeOrder(String name, String country, String city, String card, String month, String year) {
        driver.findElement(By.xpath("//button[text()='Place Order']")).click();
        driver.findElement(By.id("name")).sendKeys(name);
        driver.findElement(By.id("country")).sendKeys(country);
        driver.findElement(By.id("city")).sendKeys(city);
        driver.findElement(By.id("card")).sendKeys(card);
        driver.findElement(By.id("month")).sendKeys(month);
        driver.findElement(By.id("year")).sendKeys(year);
        driver.findElement(By.xpath("//button[text()='Purchase']")).click();
    }
}
________________________________________
🧪 Extent Report & Screenshot Integration

// TestListener.java
package listeners;

import org.testng.ITestContext;
import org.testng.ITestListener;
import org.testng.ITestResult;
import utils.ScreenshotUtil;

public class TestListener implements ITestListener {

    @Override
    public void onTestFailure(ITestResult result) {
        ScreenshotUtil.captureScreenshot(result.getName());
    }

    @Override
    public void onStart(ITestContext context) {}

    @Override
    public void onFinish(ITestContext context) {}

    @Override
    public void onTestStart(ITestResult result) {}

    @Override
    public void onTestSuccess(ITestResult result) {}

    @Override
    public void onTestSkipped(ITestResult result) {}

    @Override
    public void onTestFailedButWithinSuccessPercentage(ITestResult result) {}
}

// ScreenshotUtil.java
package utils;

import org.apache.commons.io.FileUtils;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;

import java.io.File;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class ScreenshotUtil {
    static WebDriver driver;

    public ScreenshotUtil(WebDriver driver) {
        ScreenshotUtil.driver = driver;
    }

    public static void captureScreenshot(String testName) {
        File src = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
        String timestamp = new SimpleDateFormat("yyyyMMddHHmmss").format(new Date());
        try {
            FileUtils.copyFile(src, new File("Screenshots/" + testName + "_" + timestamp + ".png"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

// LoginTest.java
package tests;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import pages.HomePage;
import pages.LoginPage;

public class LoginTest {
    WebDriver driver;
    LoginPage loginPage;
    HomePage homePage;

    @BeforeClass
    public void setup() {
        driver = new ChromeDriver();
        driver.get("https://www.demoblaze.com/");
        loginPage = new LoginPage(driver);
        homePage = new HomePage(driver);
    }

    @Test
    public void testLogin() throws InterruptedException {
        loginPage.login("testuser", "testpass");
        Thread.sleep(2000); // wait for login to complete
        Assert.assertTrue(homePage.getPageTitle().contains("STORE"));
    }

    @AfterClass
    public void tearDown() {
        driver.quit();
    }
}

// CartTest.java
package tests;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import pages.CartPage;
import pages.ProductPage;

public class CartTest {
    WebDriver driver;
    ProductPage productPage;
    CartPage cartPage;

    @BeforeClass
    public void setup() {
        driver = new ChromeDriver();
        driver.get("https://www.demoblaze.com/");
        productPage = new ProductPage(driver);
        cartPage = new CartPage(driver);
    }

    @Test
    public void testAddToCart() throws InterruptedException {
        productPage.addProductToCart("Samsung galaxy s6");
        driver.findElement(By.id("cartur")).click();
        Thread.sleep(2000);
        Assert.assertTrue(cartPage.isProductInCart("Samsung galaxy s6"));
    }

    @AfterClass
    public void tearDown() {
        driver.quit();
    }
}

// CheckoutTest.java
package tests;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.*;
import pages.CartPage;
import pages.CheckoutPage;
import pages.ProductPage;

public class CheckoutTest {
    WebDriver driver;
    ProductPage productPage;
    CartPage cartPage;
    CheckoutPage checkoutPage;

    @BeforeClass
    public void setup() {
        driver = new ChromeDriver();
        driver.get("https://www.demoblaze.com/");
        productPage = new ProductPage(driver);
        cartPage = new CartPage(driver);
        checkoutPage = new CheckoutPage(driver);
    }

    @Test
    public void testCheckout() throws InterruptedException {
        productPage.addProductToCart("Nokia lumia 1520");
        driver.findElement(By.id("cartur")).click();
        Thread.sleep(2000);
        checkoutPage.placeOrder("Manisha", "India", "Hyderabad", "1234567890123456", "06", "2025");
    }

    @AfterClass
    public void tearDown() {
        driver.quit();
    }
}

// testng.xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="ECommerceTestSuite">
    <listeners>
        <listener class-name="listeners.TestListener" />
    </listeners>
    <test name="ECommerceTests">
        <classes>
            <class name="tests.LoginTest" />
            <class name="tests.CartTest" />
            <class name="tests.CheckoutTest" />
        </classes>
    </test>
</suite>
________________________________________
// .github/workflows/maven.yml
name: Java CI with Maven

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Set up JDK 17
      uses: actions/setup-java@v3
      with:
        java-version: '17'
        distribution: 'temurin'

    - name: Cache Maven packages
      uses: actions/cache@v3
      with:
        path: ~/.m2
        key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
        restore-keys: |
          ${{ runner.os }}-maven-

    - name: Build with Maven
      run: mvn -B clean test --file pom.xml

    - name: Archive Test Reports
      if: always()
      uses: actions/upload-artifact@v3
      with:
        name: test-reports
        path: target/surefire-reports/
