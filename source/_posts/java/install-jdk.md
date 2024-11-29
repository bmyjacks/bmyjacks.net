---
title: Install JDK
date: 2024-09-11
updated: 2024-10-13
tags:
categories:
  - Java
---

When you first meet Java, you may be confused by acronyms like JVM, JRE, and JDK.

- What is the difference between them?
- Which one should you install for developing a Java program?
- Should you choose Java 8 or Java 21?

<!-- more -->

Let's further identify them.

## Choose the right software

Java runs on supported platforms using virtual machines called Java Virtual Machine (JVM)

JRE, shorts of Java Runtime Environment, includes all things you need to run a Java program. So JVM is included definitely.

{% note info %}

### Other compoments in JRE

If you want to know other stuffs included in JRE, they are runtime libraries(e.g. `rt.jar`), fonts, patches, etc.
{% endnote %}

Beyond running Java applications, if you want to create your own, the JRE falls short. That's where the Java Development Kit (JDK) comes in, the essential tool for developing Java applications.

Also, after developing a program, you need to run it. That's why JRE is automatically included in JDK.

{% note success %}

### Takeaway

- To run a Java program, you need JRE.
- To develop a Java program, you need JDK.
{% endnote %}

## Choose version

The ultimate solution to version choosing is use the most people used.

While Java 11 LTS was the most popular choice in 2023 according to the [State of Java 2023](https://www.azul.com/wp-content/uploads/final-2023-state-of-java-report.pdf) report, considering it's now 2024, Java 17 LTS or even Java 21 LTS might be more suitable for new projects.

{% note info %}

### What is LTS

LTS stands for Long Term Support, which means we can get extended support after its normal life cycle.
{% endnote %}

## Choose vendor

There are many vendors provide their own build of OpenJDK. Including but not limited to:

- [Oracle JDK](https://www.oracle.com/java/technologies/downloads/)
- [Oracle OpenJDK](https://openjdk.org/)
- [Azul Zulu](https://www.azul.com/downloads/)
- [Amazon Corretto](https://aws.amazon.com/corretto/)
- [Adoptium Eclipse Temurin](https://adoptium.net/)
- [IBM](https://www.ibm.com/support/pages/java-sdk-downloads)
- [SAP](https://sap.github.io/SapMachine/)
- [BellSoft Liberica](https://bell-sw.com/pages/downloads/)
- [Microsoft](https://www.microsoft.com/openjdk/)
- [Red Hat](https://developers.redhat.com/products/openjdk/download/)

For many years Oracle's proprietary JDK was the only choice for most Java developers. It was the de facto standard for both development and production environments.

However, a significant shift occurred in 2006 when Sun Microsystems made the JDK opensource (called OpenJDK) under [GPLv2](https://openjdk.org/legal/gplv2+ce.html) license. Which means anyone can access, build and distribute OpenJDK under some restrictions.

### Java is owned by Oracle, why don't you just use their JDK or OpenJDK

While Oracle JDK is free for personal use, commercial use does require a [subscription](https://www.oracle.com/java/java-se-subscription/). It's a huge amount of money when you need to use JDK for a long time.

Personal developers tends to use opensource SDK for their programming, so do JDK. If you looking for some specific features or supports from Oracle, their proprietary JDK is the only choice.

### Is there a difference between Oracle JDK and OpenJDK

Fortunately, the [Technology Compatibility Kit (TCK)](https://en.wikipedia.org/wiki/Technology_Compatibility_Kit) ensures that for most common Java use cases, there's little to no difference between Oracle JDK and OpenJDK.

Some third-party vendors even enhance their OpenJDK with additional features. Be sure to explore their offerings to find the best fit for your specific needs.

The majority of OpenJDK builds are drop-in replacements of other JDK. This vendor neutrality means you're not locked into a specific ecosystem and can freely switch between different OpenJDK providers.

{% note success %}

### Takeaway

All you need to care about is the license.
{% endnote %}

## Download & Install JDK

### Windows

#### Winget

Go to [winget.run](https://winget.run/search?query=jdk) and follow the instructions on the packages page.

#### Chocolatey

Go to [chocolatey.org](https://community.chocolatey.org/packages?q=jdk) and follow the instructions on the packages page.

#### Installer

1. Download `.exe` or `.msi` installers from vendors.
2. Check `Add to system PATH` options while installing those packages.

#### Archive

1. Download `.zip` archive from vendors.
2. Uncompress the file, save the path.
3. Add the `bin` folder to your `PATH`.

### MacOS

#### Homebrew

```sh
brew install openjdk@21
```

#### Installer

1. Download `.dmg` or `.pkg` installers from vendors.
2. Check `Add to system PATH` options while installing those packages.

#### Archive

1. Download `.zip` or `.tar.gz` archive from vendors.
2. Uncompress the file, save the path.
3. Add the `bin` folder to your `PATH`.

### Linux

#### Installer

1. Download your OS's installers from vendors.
2. Check `Add to system PATH` options while installing those packages.

#### Archive

1. Download `.tar.gz` archive from vendors.
2. Uncompress the file, save the path.
3. Add the `bin` folder to your `PATH`.

## Verify installation

Type in a terminal:

```sh
java --version
```

```py
def main():
    print("Hello, World!")
```

The output should like:

```text
openjdk 21.0.4 2024-07-16
OpenJDK Runtime Environment Homebrew (build 21.0.4)
OpenJDK 64-Bit Server VM Homebrew (build 21.0.4, mixed mode, sharing)
```

{% note success %}
    You've completed all steps of installing JDK. Happy coding!
{% endnote %}
