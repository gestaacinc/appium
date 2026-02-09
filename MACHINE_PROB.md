# Machine Problem: Android Messaging Automation

This document outlines the requirements and provided codebase for the Messaging App automation task.

## Problem Description
The goal is to automate a simple workflow in the **Google Messages** app. Because the Calculator app was unavailable on the target emulator, we are using the Messaging app to practice locating elements and handling app onboarding flows.

### Tasks to Complete:
1.  **Launch the App**: The boilerplate code handles opening `com.google.android.apps.messaging`.
2.  **Handle Onboarding**: Bypassing the sign-in screen by selecting "Use Messages without an account".
3.  **Locate 'Start chat'**: Use **Appium Inspector** to find the unique identifier (ID or Accessibility ID) for the "Start chat" floating action button.
4.  **Perform Action**: Implement the code to click the button.
5.  **Verification**: Assert that the app has successfully transitioned to the "New conversation" screen.

---

## Required Setup
*   **Appium Server**: Running on port 4723.
*   **Android Emulator**: `emulator-5554` running Android 14.
*   **App Package**: `com.google.android.apps.messaging`
*   **App Activity**: `.ui.ConversationListActivity`

---

## Provided Codebase (`MessagingChallenge.java`)

```java
package com.appium.demo;

import io.appium.java_client.AppiumBy;
import io.appium.java_client.android.AndroidDriver;
import io.appium.java_client.android.options.UiAutomator2Options;
import org.openqa.selenium.WebElement;
import org.testng.Assert;
import org.testng.annotations.AfterTest;
import org.testng.annotations.BeforeTest;
import org.testng.annotations.Test;

import java.net.MalformedURLException;
import java.net.URL;
import java.time.Duration;

public class MessagingChallenge {

    AndroidDriver driver;

    @BeforeTest
    public void setup() throws MalformedURLException {
        UiAutomator2Options options = new UiAutomator2Options();
        options.setPlatformName("Android");
        options.setAutomationName("UiAutomator2");
        options.setDeviceName("emulator-5554");

        // Messaging Package & Activity
        options.setAppPackage("com.google.android.apps.messaging");
        options.setAppActivity(".ui.ConversationListActivity");

        driver = new AndroidDriver(new URL("http://127.0.0.1:4723/"), options);
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
    }

    @Test
    public void testNewMessage() {
        System.out.println("Starting Messaging Test...");

        // Step 1: Handle Onboarding / Sign-in screen if it exists
        try {
            // "Use Messages without an account" button
            WebElement skipSignIn = driver.findElement(AppiumBy.id("com.google.android.apps.messaging:id/secondary_action_button"));
            skipSignIn.click();
            System.out.println("Skipped Sign-in screen.");
        } catch (Exception e) {
            System.out.println("Sign-in screen not visible or already bypassed.");
        }

        // TODO: Find the "Start chat" button and click it
        // Hint: It is a floating action button (ImageButton)
        // Use Appium Inspector to find its Resource ID or Accessibility ID!
        /*
        WebElement startChatBtn = driver.findElement(AppiumBy.accessibilityId("Start chat"));
        startChatBtn.click();
        */

        // TODO: Verify we are on the "New conversation" screen
        // Hint: Look for a text field or header with "To" or "New conversation"
        /*
        WebElement recipientField = driver.findElement(AppiumBy.id("com.google.android.apps.messaging:id/recipient_text_view"));
        Assert.assertTrue(recipientField.isDisplayed());
        */
        
        System.out.println("Machine Problem logic needs to be implemented above!");
    }

    @AfterTest
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

## How to Run
Once you have implemented the TODOs, run the test from your terminal using:
```bash
mvn test -Dtest=MessagingChallenge
```

## Expected Output
*   **Console**: Should print "Starting Messaging Test..." and "Skipped Sign-in screen."
*   **App**: Should open the "New conversation" screen with a blinking cursor in the recipient field.
*   **Result**: TestNG report should show `BUILD SUCCESS`.
