package org.example;

import io.github.bonigarcia.wdm.WebDriverManager;
import io.qase.api.annotation.QaseTitle;
import org.aspectj.lang.annotation.Before;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;

import java.util.Random;

import static org.junit.jupiter.api.Assertions.assertEquals;


public class EcomTest {

    private WebDriver driver;
    private Object confirm;

    static EcomTest createPlaygroundWebTest() {
        return new EcomTest();
    }


    @BeforeAll
    public static void init() {
        WebDriverManager.chromedriver().setup();
    }

    @BeforeEach
    public void setup() {
        driver = new ChromeDriver();
        driver.manage().window().setPosition(new Point(2000, 0));
        driver.manage().window().maximize();
    }

    @AfterEach
    public void closeBrowser() {
        driver.close();
        driver.quit();
    }


    @Test
    @QaseTitle("create_an_account")
    public void test_create_an_account() {
        Random random = new Random();
        String firstName = "felix";
        String lastName = "hangreen";
        String eMail ="flex" + random.nextInt() + "@hangreen.se";
        String telephone = "+46 070 111 32 111";
        String password = "tagf658";
        String passwordconfirm = "tagf658";

        driver.get("https://ecommerce-playground.lambdatest.io/index.php?route=account/register");

        driver.findElement(By.id("input-firstname")).sendKeys(firstName);
        driver.findElement(By.id("input-lastname")).sendKeys(lastName);
        driver.findElement(By.id("input-email")).sendKeys(eMail);
        driver.findElement(By.id("input-telephone")).sendKeys(telephone);
        driver.findElement(By.id("input-password")).sendKeys(password);
        driver.findElement(By.id("input-confirm")).sendKeys(passwordconfirm);


        driver.findElement(By.cssSelector("label[for='input-agree']")).click();
        driver.findElement(By.xpath("//input[@value=\"Continue\"]")).click();

        String message = driver.findElement(By.xpath("//h1[@class='page-title my-3']")).getText();
        assertEquals(message, "Your Account Has Been Created!");
    }


    @Test
    @QaseTitle("should_be_able_to_login")
    public void should_be_able_to_login() {

        String message = login();

        assertEquals(message, "My Account");
    }


    @Test
    @QaseTitle("should_be_able_to_logout")
    public void should_be_able_to_logout() {

        login();

        driver.findElement(By.cssSelector(".fas.fa-sign-out-alt.fa-fw.mr-1")).click();
        String message =  driver.findElement(By.cssSelector(".page-title.my-3")).getText();

        assertEquals(message, "Account Logout");
    }


    @Test
    @QaseTitle("should_be_able_buy")
    public void should_be_able_buy() {

        driver.get("https://ecommerce-playground.lambdatest.io/index.php?route=account/buy");
        login();
        driver.findElement(By.xpath("//input[@name=\"search\"]")).sendKeys("Sony VAIO");
        driver.findElement(By.xpath("//button[@type=\"submit\"]")).click();
        WebElement element = driver.findElement(By.xpath("//button[@class='btn btn-cart cart-46']"));
        JavascriptExecutor executor = (JavascriptExecutor)driver;
        executor.executeScript("arguments[0].click();", element);

        WebElement element2 = driver.findElement(By.xpath("//a[@href='https://ecommerce-playground.lambdatest.io/index.php?route=checkout/checkout']"));
        executor.executeScript("arguments[0].click();", element2);

        driver.findElement(By.cssSelector("label[for='input-agree']")).click();
        driver.findElement(By.id("button-save")).click();

      //  WebElement element3 = driver.findElement(By.xpath("//button[@class='btn btn-lg btn-primary flex-grow-1']"));
     //   driver.findElement(By.cssSelector(".btn.btn-lg.btn-primary.flex-grow-1")).click();

    }


    private String login() {
        driver.get("https://ecommerce-playground.lambdatest.io/index.php?route=account/login");
        driver.findElement(By.id("input-email")).sendKeys("Hampus@stan.nu");
        driver.findElement(By.id("input-password")).sendKeys("Vatsug00");
        driver.findElement(By.xpath("//input[@value=\"Login\"]")).click();
        return driver.findElement(By.linkText("My Account")).getText();
    }

    @Test
    public String should_be_able_to_generate_with_qase(){

        driver.get("https://app.qase.io/projects");
        driver.findElement(By.id("input-email")).sendKeys("Gustav@Blommorna.nu");
        driver.findElement(By.id("input-password")).sendKeys("Gustav2000");
        driver.findElement(By.xpath("//input[@value=\"Login\"]")).click();
        String message = driver.findElement(By.linkText("My Account")).getText();
        return message;
    }
}
