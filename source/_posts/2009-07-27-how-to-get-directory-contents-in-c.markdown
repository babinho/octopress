---
author: babinho
comments: true
date: 2009-07-27 16:18:04
layout: post
slug: how-to-get-directory-contents-in-c
title: How to get directory contents in C#
wordpress_id: 14
categories:
- C#
---

I am creating a backup application and have to get the list of files in some directory here is the simple way to do it. If you only need contents of one directory you can use this code, i output it to System.Console just as an example, you can catch everything in string[] or whatever collection you prefer for your application. You only need using System.IO for this example:

```java
static void GetDirContents(string sDir)
{
  try
  {
    foreach (string d in Directory.GetDirectories(sDir))
    {
      DirectoryInfo dirInfo = new DirectoryInfo(d);
      Console.WriteLine("[DIR] " + dirInfo.Name);
      foreach (string f in Directory.GetFiles(d))
      {
        FileInfo fInfo = new FileInfo(f);
        Console.WriteLine("\t" + fInfo.Name);
      }
    }
  }
  catch (Exception sex)
  {
    Console.WriteLine(sex.Message);
  }
}
```

If you are in need to do this recursively for all sub directories you just call the void after file writing for loop

```java
static void GetDirContents(string sDir)
{
  try
  {
    foreach (string d in Directory.GetDirectories(sDir))
    {
      DirectoryInfo dirInfo = new DirectoryInfo(d);
      Console.WriteLine("[DIR] " + dirInfo.Name);
      foreach (string f in Directory.GetFiles(d))
      {
        FileInfo fInfo = new FileInfo(f);
        Console.WriteLine("\t" + fInfo.Name);
      }
      GetDirContents(d);
    }
  }
  catch (Exception sex)
  {
    Console.WriteLine(sex.Message);
  }
}
```

See you soon with next example.
