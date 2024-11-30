---
title: 如何安装 JDK
date: 2024-09-11
updated: 2024-11-30
tags:
categories:
  - Java
---

你是否思考过 JVM、JRE 和 JDK 之间的区别？你是否知道如何选择正确的 JDK 版本和供应商？如果你是刚入门 Java 开发的小白，那么这篇文章就是为你准备的。

<!-- more -->

## 到底需要哪一个？

Java 的一大特性（口号）就是 [Write Once, Run Anywhere](https://en.wikipedia.org/wiki/Write_once,_run_anywhere)，这意味着你只需要编写一份代码，通过一次编译，然后就可以在任何支持 Java 的平台上运行，于是它需要拥有特定的工具来实现这一特性。与 Python 等 script language 不同，Java 是一种编译型语言，这意味着它需要将代码编译成 platform-independent 的字节码，然后在不同的平台上通过虚拟机/容器来运行。

Java Runtime Environment 是 Java 应用程序的运行时环境，它包含了运行字节码的虚拟机 Java Virtual Machine，一些 runtime libraries 和其他支持文件。JRE 是运行 Java 程序的必要组件。

如果你想编写自己的 Java 应用程序，JRE 就不够用了。 正如一辆在路上行驶的汽车，汽车相当于 Java 字节码，JRE 相当于一条条道路，而 Java Development Kit 就相当于汽车的设计工厂。JDK 包含编译器 javac、调试器 jdb、文档生成器 javadoc 等开发工具。同样的，在汽车生产出来后需要进行上路测试，JVM 在编译字节码后也需要将其运行，所以 JDK 包含了 JRE。

{% note success %}

### Takeaway

- 要运行 Java 程序，您需要 JRE。
- 要开发 Java 程序，您需要 JDK。
{% endnote %}

## 该如何选择 JDK 版本？

版本选择的最优解决方案是使用最多人使用的版本，这样可以获得更好的社区支持。

根据 [State of Java 2023](https://www.azul.com/wp-content/uploads/final-2023-state-of-java-report.pdf) 报告，Java 11 LTS 是 2023 年最受欢迎的选择，但考虑到现在接近 2025 年，Java 17 LTS 或者 Java 21 LTS 可能更适合新项目。

{% note info %}

### LTS 是啥？

LTS 是 Long Term Support 的缩写，意味着我们可以在其正常生命周期结束后获得延长的支持。
{% endnote %}

## 那么，我该选择哪一个供应商的 JDK？

有许多供应商提供自己构建的 OpenJDK。包括但不限于：

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

在一开始时，Oracle 的闭源 JDK 是大多数 Java 开发人员的唯一选择。它是开发和生产环境的事实标准。

然而在2006 年，Sun Microsystems 将 JDK 开源（称为 OpenJDK）并在 [GPLv2](https://openjdk.java.net/legal/gplv2+ce.html) 许可下发布。这意味着任何人都可以访问、构建和分发 OpenJDK，但有一些限制。

### 那么，为什么不直接使用 Oracle JDK 或 OpenJDK？

虽然 Oracle JDK 对个人使用是免费的，但商业使用确实需要 [subscription](https://www.oracle.com/java/java-se-subscription/)。当你需要长时间使用 JDK 时，这是一笔超级大的开支。个人开发者倾向于使用开源 SDK 进行编程（或许类似一些宗教信仰），JDK 也是如此。但是如果你需要 Oracle 的某些特定功能或支持，他们的专有 JDK 是唯一的选择。

### 那么，OpenJDK 和 Oracle JDK 有什么区别吗？

还好，[Technology Compatibility Kit](https://en.wikipedia.org/wiki/Technology_Compatibility_Kit) （一种测试）确保了在大多数常见的 Java 使用场景下，Oracle JDK 和 OpenJDK 之间几乎没有区别。

一些第三方供应商甚至会在 OpenJDK 的基础上增加一些额外的功能。可以通过对应产品的介绍页面，找到最适合你特定需求的 JDK。~~虽然小白根本没啥需求就是了~~，那么选择最易于安装和使用的 JDK 就是最好的选择。

市面上的大多数 OpenJDK 都是其他 JDK 的 drop-in replacement。这意味着有无数的“后悔药”可以选择，如果你发现当前的不合适，可以随时无损更换。

## 下载 & 安装 JDK

### Windows

#### Winget & Chocolatey

不是哥们你都在 Windows 上安装包管理器了，就不需要我了吧？

#### Installer

1. 下载 `.exe` or `.msi` 结尾的安装包。
2. 记得在安装时勾选上 `Add to system PATH` 。如果没有勾选，可以手动添加 `bin` 文件夹到系统环境变量中。

#### Archive

1. 下载 `.zip` 结尾的压缩包。
2. 解压缩，并且移动到一个你喜欢的目录。
3. 添加 `bin` 文件夹到 `PATH` 里.

### MacOS

#### Homebrew

```sh
brew install openjdk@21
```

#### Installer

1. 下载 `.dmg` or `.pkg` 结尾的安装包。
2. 在安装时勾选 `Add to system PATH` 。

#### Archive

同 Windows。

### Linux

#### Installer

根据不同的发行版，使用不同的包管理器安装 JDK。

#### Archive

同 Windows。

## Verify installation

打开终端，输入以下命令：

```sh
java --version
```

它应该输出类似以下的信息：

```text
openjdk 21.0.4 2024-07-16
OpenJDK Runtime Environment Homebrew (build 21.0.4)
OpenJDK 64-Bit Server VM Homebrew (build 21.0.4, mixed mode, sharing)
```

{% note success %}
    登登，你已经成功安装了 JDK！Happy coding!
{% endnote %}
