---
title : Setting Up a Quick Embedded Web Server with Jetty
categories: Java Jetty Webserver
tags: java jetty webserver
description: This guide shows how to set up a basic web server using Jetty. It's a simple, step-by-step approach for getting a web server up and running quickly

last_modified_at: 2025-02-13 18:41:00 -0530
---

### Set Up a Java Project Using Gradle Command Line (Draft)

1. **Download Gradle:** First, download Gradle from the official website.
2. **Add Gradle to PATH:** Next, add Gradle binaries to your PATH.
3. **Create Java Project:** Once Gradle is set in the path, run the below command to create a Java project:
  
```bash

gradle init \
  --type java-application \
  --dsl groovy \
  --test-framework junit-jupiter \
  --package com.example \
  --project-name embedded-jetty  \
  --no-split-project  \
  --java-version 17

```

  &nbsp;&nbsp;&nbsp;&nbsp;4\. **Clone a Project:** Alternatively, simply clone an existing Gradle project from a repository.


### Add Jetty Dependencies to Your Gradle Project
- Add Jetty Dependencies: Add the following dependencies to the dependencies section in build.gradle file

```groovy
dependencies {
    implementation 'org.eclipse.jetty:jetty-server:12.0.16'
}
``` 
### Program a Simple HTTP Server








##### * References*
[https://jetty.org/docs/jetty/12/programming-guide/index.html](https://jetty.org/docs/jetty/12/programming-guide/index.html)
