---
author: babinho
comments: true
date: 2012-11-13 21:20:37
layout: post
slug: pos-printing-on-android
title: POS printing on Android
wordpress_id: 170
categories:
- Android
tags:
- android
- java
---

So, you want to develop your big Point Of Sale(POS) solution on android but don't know how to print the receipts. You have a few options:

1. Make a centralized mobile web application, reachable on wifi and use your tablets to take orders and print the receipts at the bar.
2. Use some kind of a Windows Mobile/CE handheld device with a printer
3. Make an Android app and print via Bluetooth

Due to the requirements of the project i have been working on, i have made a solution somewhere between 1 and 3, using Ruby on Rails for back end administration and api, and using Android tablets for billing. As for the printing of the receipts, the only POS printer we could find on our market with Bluetooth capabilities was [Bixolon SPP-R200](http://www.bixolon.com/_eng/products/product_form.asp?code=0105&uid=34) so i decided to use it. And the Bixolon support guy Jörg from Germany was really helpful, and sent me the android api so all i had to do was include one jar file and write a class for handling the printer. You also get the whole example application, with every api call available.

For my needs, and future compatibility, i first integrated everything into the activity, yes silly of me, but then i extracted it into a class and also created a printing interface which my app  now uses, and it can be used across multiple apps very easily. First a basic printing interface:

```java
public interface Printer {
  public boolean getStatus();
  public boolean connectPrinter();
  public boolean disconnectPrinter();
  public boolean print(String textToPrint);
}
```

And i implemented the class using the Bixolon Api here:

```java
import com.bixolon.android.library.BxlService;
public class BixolonPrinter implements Printer {
  BxlService mBxlService;
  boolean conn;

  @Override
    public boolean getStatus() {
      return conn;
    }

  @Override
    public boolean connectPrinter() {
      if (conn != true) {
        CheckGC("Connect_Start");
        mBxlService = new BxlService();
        if (mBxlService.Connect() == 0) {
          conn = true;
        } else {
          conn = false;
        }
        CheckGC("Connect_End");
      }
      return conn;
    }

  @Override
    public boolean disconnectPrinter() {
      if (mBxlService.Disconnect() == 0) {
        conn = false;
      }
      else
      {
        conn = true;
      }
      return !conn;
    }

  @Override
    public boolean print(String textToPrint) {
      boolean result = false;
      CheckGC("PrintText_Start");
      int returnValue;
      returnValue = mBxlService.PrintText(textToPrint,
          BxlService.BXL_ALIGNMENT_CENTER, BxlService.BXL_FT_DEFAULT,
          BxlService.BXL_TS_0WIDTH | BxlService.BXL_TS_0HEIGHT);
      if (returnValue == BxlService.BXL_SUCCESS) {
        returnValue = mBxlService.LineFeed(2);
        result = true;
      }
      else {
        result = false;
      }
      CheckGC("PrintText_End");
      return result;
    }

  void CheckGC(String FunctionName) {
    Runtime.getRuntime().maxMemory();
    System.runFinalization();
    System.gc();
    Runtime.getRuntime().maxMemory();
  }
}
```

All that is remaining in your activity is to declare a variable of the Printer type and instantiate a new BixolonPrinter class.

```java
public class TestActivity extends Activity {
  Printer printer;

  @Override
    public void onCreate(Bundle savedInstanceState) {
      // TODO: implement this into settings and choose based on that
      printer = new BixolonPrinter();
      printer.connectPrinter();
    }
}
```

I chose this path because one company that is in mobile solutions in Croatia heard a presentation about the application, and decided to lend us a Zebra mobile POS printer(don't really know which model), to implement it into the app. I will publish the implementation here as soon as i can.
You can get the Bixolon api and documents by registering to the bixolon
developer's site [Bixolon developer site](http://www.bixolon.com)
