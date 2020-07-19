---
layout: post
title: "Writing Appium tests in Kotlin"
date: 2020-06-21
categories: appium
author: nathan
tags: appium
image: https://media-exp1.licdn.com/dms/image/C5612AQE4_jpP5OHJyw/article-cover_image-shrink_720_1280/0?e=1598486400&v=beta&t=PNDt6hFcu-vArgaU2AmWObXHdCvQzHfQt3afSSDVK-I
---

> Writing Appium tests in Kotlin

Kotlin is a modern programming language that focuses on conciseness, clarity, and code safety. Google officially adopted Kotlin by adding support into Android Studio in 2017 and since then has announced it as the preferred language for Android developers at Google I/O 2019. Perhaps your Android app team has incrementally added Kotlin code to your project or have elected to start new Android projects in Kotlin moving forward. Having the app code and UI automation code in the same language helps engineering and QA teams level set on the critical user journeys being tested. I’d like to share the basics of getting started with writing an Appium test in Kotlin. I’ll be using the Google Maps Android app as the target for this example and test on a Google Pixel 3a emulator.

Kotlin is fully compatible with Java so we can fortunately leverage the Appium Java client and JUnit libraries in addition to writing our code in the IntelliJ IDE. IntelliJ has a free community version called IntelliJ IDEA CE, which can be downloaded from their website or via your package manager.

### Getting Started

In IntelliJ IDE, navigate to _File > New Project_. Select _Gradle_ and under _Additional Libraries and Frameworks_ select _Kotlin/JVM_.

![](https://media-exp1.licdn.com/dms/image/C5612AQEopUacN8XmCg/article-inline_image-shrink_1500_2232/0?e=1598486400&v=beta&t=Ok-_tf8hiiBLjtnfzin1zNT17lLdU1pPoST56pRtNSw)

In the Project file view on the left, open the _build.gradle_ file and add the following dependencies:

{% highlight gradle %}
testCompile group: 'junit', name: 'junit', version: '4.13'
compile group: 'io.appium', name: 'java-client', version: '7.3.0'
{% endhighlight %}

We will add the Appium tests in the _src/test/kotlin_ project directory.

![](https://media-exp1.licdn.com/dms/image/C5612AQE1ec9m8qK1OQ/article-inline_image-shrink_1000_1488/0?e=1598486400&v=beta&t=kL5YnLMf_HvtOggKSxuHC4bMYkY_9xfayHHsbVLqj5E)

Right click the _kotlin_ subdirectory and select _New > Kotlin File/Class_. Create three new classes:

- _ProjectCapabilities_
- _TestBase_
- _GoogleMapsAndroidTest_

![](https://raw.githubusercontent.com/appexplained/blog/gh-pages/assets/post-images/appium-kotlin/new-kotlin-file-appium-test.png)

We will structure the project in these three classes to make the organization clear. The _ProjectCapabilities_ class will return the Desired Capabilities, _TestBase_ will be a base class for our test for scaffolding the test setup and teardown, and lastly the _GoogleMapsAndroidTest_ class will inherit from our _TestBase_, use the Desired Capabilities, and contain our test logic.

### 1. ProjectCapabilities Class

Let’s get started with the ProjectCapabilities class. This class will have a method to return the Desired Capabilities.

{% highlight kotlin %}
import org.openqa.selenium.remote.DesiredCapabilities

class ProjectCapabilities {
companion object {
fun AndroidBaseCapabilities(): DesiredCapabilities {
val caps = DesiredCapabilities()
caps.setCapability("autoAcceptAlerts", true)
caps.setCapability("platformName", "Android")
caps.setCapability("automationName", "UiAutomator2")
caps.setCapability("deviceName", "Android Emulator")
caps.setCapability("platformVersion", "10")
caps.setCapability("appPackage", "com.google.android.apps.maps")
caps.setCapability("appActivity", "com.google.android.maps.MapsActivity")
return caps
}
}
}
{% endhighlight %}

Functions and Methods in Kotlin are declared with the “fun” keyword. You can also indicate the function return type with a colon followed by the type. We can make this a static class method by declaring it in a “companion object.” You can create read-only variables with “val” and variables that can be reassigned with “var”.

In this case I am targeting a local Pixel 3a emulator. For the appPackage and appActivity I am specifying the associated values for the Google Maps Android app.

### 2. TestBase Class

Next, let’s work on our TestBase class. By default classes in Kotlin are implicitly final and can’t be inherited. To make a class inheritable, we’ll mark it with the “open” keyword. In this class we will create property variables for the: driver, capabilities, and WebDriver URL. We’ll provide visibility modifiers to our properties, in this case:

- Protected: Only visible in the class or subclasses
- Private: Only visible in the class

We’ll also mark our Desired Capabilities variable as “open” so it can be overridden from our GoogleMapsAndroidTest class which inherits from the TestBase class.

In addition you can indicate whether a variable can be null. The ability to declare this upfront is a great feature of the Kotlin language because it protects against unexpected NullPointerExceptions from our code. In this case we can indicate that the driver and caps can be null with “?” after the type and and set them to null. Kotlin also supports string interpolation by using the “$” character to interpolate a variable or “${}” to interpolate an expression.

Our TestBase will also have two JUnit annotations: @Before for our setup function and @After for our teardown to perform a driver quit. Our setup function will be straightforward and it will instantiate the driver object. In our teardown function we’ll check to see if the driver object is not null and if so quit our session. Otherwise we will throw an Exception. Kotlin provides a convenience operator for checking for a null condition with “?:”. This is called the Elvis Operator because if you turn your head sideways the question mark character looks like a swoosh of hair and the colon looks like eyes.

![](https://media-exp1.licdn.com/dms/image/C5612AQEnpjFwUJNVDA/article-inline_image-shrink_1000_1488/0?e=1598486400&v=beta&t=BIoGM3lbmtcmRPmzYv3tpbyLDU8zCR05YSKxV51Xmb4)

Do you see the resemblance now? Haha either way, the source code for the class can be found below and let’s carry on.

{% highlight kotlin %}
import org.openqa.selenium.remote.DesiredCapabilities

class ProjectCapabilities {
companion object {
fun AndroidBaseCapabilities(): DesiredCapabilities {
val caps = DesiredCapabilities()
caps.setCapability("autoAcceptAlerts", true)
caps.setCapability("platformName", "Android")
caps.setCapability("automationName", "UiAutomator2")
caps.setCapability("deviceName", "Android Emulator")
caps.setCapability("platformVersion", "10")
caps.setCapability("appPackage", "com.google.android.apps.maps")
caps.setCapability("appActivity", "com.google.android.maps.MapsActivity")
return caps
}
}
}
{% endhighlight %}

### 3. GoogleMapsAndroidTest Class

Our last class, GoogleMapsAndroidTest will have our test logic. Here we have one test function annotated with the JUnit Test annotation. Our test will be simple and straightforward, we will tap on the Headlines tab button and then scroll down from the list of news articles. Since we will be performing driver actions we’ll need to first ensure the driver is not null. Kotlin provides a way to perform an operation only if the value is not null using the “?” (the safe call operator) together with “let.”

{% highlight kotlin %}
import io.appium.java_client.MobileBy
import org.junit.Test
import org.openqa.selenium.interactions.Interaction
import org.openqa.selenium.interactions.PointerInput
import org.openqa.selenium.interactions.Sequence
import org.openqa.selenium.remote.DesiredCapabilities
import org.openqa.selenium.support.ui.ExpectedConditions
import org.openqa.selenium.support.ui.WebDriverWait
import java.time.Duration

class GoogleMapsAndroidTest: TestBase() {
override var caps: DesiredCapabilities? = ProjectCapabilities.AndroidBaseCapabilities()

    // Point of Interest for Search
    private val pointOfInterest: String = "Lincoln Memorial Reflecting Pool"
    private val pointOfInterestTextAttribute: String = "Lincoln Memorial Circle Northwest, Washington, DC"

    // Elements
    private val firstLaunchSkipButton: String = """//*[@class="android.widget.Button" and @text="SKIP"]"""
    private val searchBox: String = "com.google.android.apps.maps:id/search_omnibox_text_box"
    private val searchBoxInput: String = "com.google.android.apps.maps:id/search_omnibox_edit_text"
    private val searchOptionListElement: String = """//*[@class="android.widget.TextView" and @text="$pointOfInterestTextAttribute"]"""
    private val resultCardTitle: String = "com.google.android.apps.maps:id/title"
    private val photosSection: String = "Photos"
    private val aboutSection: String = "About"


    @Test
    fun googleMapsTest() {

        // Set an explicit wait of 10 seconds
        val wait = WebDriverWait(driver?.let { it }, 10)

        // On first launch, press the SKIP button
        wait.until(ExpectedConditions.presenceOfAllElementsLocatedBy(MobileBy.xpath(firstLaunchSkipButton)))[0].click()

        // Click on the Search Box
        wait.until(ExpectedConditions.presenceOfElementLocated(MobileBy.id(searchBox))).click()

        // Enter point of interest
        val searchBoxInput = wait.until(ExpectedConditions.presenceOfElementLocated(MobileBy.id(searchBoxInput)))
        searchBoxInput.sendKeys(pointOfInterest)

        // Tap the option
        wait.until(ExpectedConditions.presenceOfElementLocated(MobileBy.xpath(searchOptionListElement))).click()

        // Tap on the Card title
        wait.until(ExpectedConditions.presenceOfElementLocated(MobileBy.id(resultCardTitle))).click()

        // Tap on the Photos section
        wait.until(ExpectedConditions.presenceOfElementLocated(MobileBy.AccessibilityId(photosSection)))

        // Swipe Down
        val finger: PointerInput = PointerInput(PointerInput.Kind.TOUCH, "finger")
        val moveToStart: Interaction = finger.createPointerMove(Duration.ZERO, PointerInput.Origin.viewport(), 533, 1449)
        val pressDown: Interaction = finger.createPointerDown(PointerInput.MouseButton.LEFT.asArg())
        val moveToEnd: Interaction = finger.createPointerMove(Duration.ofMillis(100), PointerInput.Origin.viewport(), 553, 538)
        val pressUp: Interaction = finger.createPointerUp(PointerInput.MouseButton.LEFT.asArg())

        val swipe = Sequence(finger, 0)
        swipe.addAction(moveToStart)
        swipe.addAction(pressDown)
        swipe.addAction(moveToEnd)
        swipe.addAction(pressUp)

        driver?.let { it.perform(arrayListOf(swipe)) }

        // Tap on the About section
        driver?.let { it.findElementByAccessibilityId(aboutSection).click() }
    }

}
{% endhighlight %}

### Conclusion

Our work here is done! We can now execute our test and watch it in action.

Congratulations you now have a taste of what it’s like to write an Appium test in Kotlin! You can download the full project source code [here](https://github.com/appexplained/appium-kotlin).
