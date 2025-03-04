# AIRBNB
Testing Web of AIRBNB
package AirbnbBaseClassTest;

import Pages.*;
import com.aventstack.extentreports.ExtentReports;
import com.aventstack.extentreports.ExtentTest;
import io.github.bonigarcia.wdm.WebDriverManager;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.ITestResult;
import org.testng.annotations.*;
import Pages.Json_Schema;
import com.google.gson.*;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import java.io.File;

public class BaseClassTest {
    public static WebDriver driver;
    protected SearchPage searchPage;
    protected static CheckInPage checkInPage;
    protected static CheckOutPage checkOutPage;
    protected static AddGuestsPage addGuestsPage;
    protected static ApartmentPage select;
    protected static BookingPageNextTab newtab;
    protected CreateFile createFile;
    protected JSon_Format json;
    protected static ExtentReports extent;
    protected static ExtentTest test;
    protected static ExtentTest childTest;
    protected static ExtentTest grandChildTest;


    @BeforeSuite
    public void setUp() {
        /*ExtentSparkReporter htmlReporter = new ExtentSparkReporter("ExtentReportAirBnB.html");
        htmlReporter.config().setTheme(Theme.DARK);
        htmlReporter.config().setDocumentTitle("Airbnb Automation Report");
        htmlReporter.config().setReportName("Test Execution Report");

         */

//        extent.attachReporter(htmlReporter);


        WebDriverManager.chromedriver().setup();
        System.setProperty("chrome.driver", "resources\\chromedriver.exe");
        driver = new ChromeDriver();
        driver.get("https://www.airbnb.com/");
        driver.manage().window().maximize();
        System.out.println("WEBSITE NAME: " + driver.getTitle());
        // Initialize Extent Reports
        extent = new ExtentReports();
        ExtentManager.initReports(driver,"test-output/ExtentReport.html");

        // Initialize CreateFile with both WebDriver and file path
        String filePath = "output.txt";
        //createFile = new CreateFile(driver);  // For driver-related functionality
        createFile = new CreateFile(filePath); // For file-related functionality
        createFile.creatingFile();// Create the file
        String filePathh = "output.json";
        json = new JSon_Format(filePathh);
        //json.creatingFile();

    }

    @BeforeMethod
    public void startDriver() {
        searchPage = new SearchPage(driver);
        checkInPage = new CheckInPage(driver);
        checkOutPage = new CheckOutPage(driver);
        addGuestsPage = new AddGuestsPage(driver);
        select = new ApartmentPage(driver);
        newtab = new BookingPageNextTab(driver);
        createFile = new CreateFile(driver);
        json = new JSon_Format(driver);

    }
   /* @AfterMethod
    public void logResult(ITestResult result) {
        // Log test result in Extent Reports
        if (result.getStatus() == ITestResult.SUCCESS) {
            test.pass("Test Passed: " + result.getName()).pass(result.getThrowable());
        } else if (result.getStatus() == ITestResult.FAILURE) {
            test.fail("Test Failed: " + result.getName())
                    .fail(result.getThrowable());
        } else if (result.getStatus() == ITestResult.SKIP) {
            test.skip("Test Skipped: " + result.getName());
        }
    }*/


    @AfterSuite
    public void tearDown() {
        if (driver != null) {
            createFile.writeData();
            System.out.println("Data writing completed after all tests.");
            // Gather data from the web page

            JSon_Format.ApartmentDetails apartmentDetails = json.gatherDataFromPage();
            // Write the data to the JSON file
            json.writeData(apartmentDetails);


            // Optionally read and print the data from the file
            JSon_Format.ApartmentDetails readDetails = json.readData();
            System.out.println("Data read from JSON: " + readDetails.getPlaceName() + ", " + readDetails.getRent() + "," + readDetails.getDescription() + "," + readDetails.getHeading() + "," + readDetails.getHighlights() + "," + readDetails.getDefaultDescription() + "," + readDetails.getAmenities());
            //System.out.println("Data read from JSON: \n" + new GsonBuilder().setPrettyPrinting().create().toJson(readDetails))
            try {
                Json_Schema.Schema();
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
            driver.quit();
        }

        //extent.flush();
        ExtentManager.flushReport();
        ExtentManager.sendreportemail();


    /*public ExtentTest startTest(String testName) {
        test = extent.createTest(testName); // Initialize the test in ExtentReports
        return test;
    }*/
    }


}
