---
title: "Hello World!"
date: 2024-09-20 04:51:00 +0800
categories: [Android Security, Mobile Security]
tags: [android, adb, insecure-logging, hardcoded-credentials, debugging, mobile-security, reverse-engineering]
author: "0xReDrag0n"                     # for single entry
---

## Insecure Logging

```powershell
adb install allsafe.apk
```

```
adb logcat
```

```
adb shell ps
```

```
adb logcat
```

```
adb logcat | findstr "3371"
```

## Hardcoded Credentials
- go to file infosecadventures.allsafe.challenges.HardcodedCredentials

search about dev_env on res/values/strings.xml