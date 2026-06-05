---
layout: single
title: Installing Java 10 on Linux
date: 2018-04-15 17:40:30.000000000 +09:00
type: post
header:
    teaser: "https://i.imgur.com/gxL06lI.jpg"
    image: "https://i.imgur.com/gxL06lI.jpg"
categories:
- IT
tags: [Java, linux]
---

I've put together how to install Java 10 on Linux. It installs just fine on both Ubuntu and Linux Mint. I referred to the [linuxuprising](https://www.linuxuprising.com/2018/04/install-oracle-java-10-in-ubuntu-or.html) documentation.

## 1. Preparing the Installation

I'm not sure why, but as of April 15, 2018, Java 10 isn't available through apt-get. By the time you're reading this, it might be available, so let's check.

```bash
sudo apt-cache search java10
```

If it's not in apt-get yet, let's install it from the PPA repository. I also tried the approach of downloading the JDK tar file and manually configuring PATH and JAVA_HOME, but for some reason it didn't work on Linux Mint.

```bash
sudo add-apt-repository ppa:linuxuprising/java
sudo apt update
```

## 2. Installation

Now let's install Oracle Java 10.

```bash
sudo apt install oracle-java10-installer
```

Don't be alarmed by the blue screen—you need to agree to the license for the installation to proceed. The installation takes quite a while.

On Ubuntu, Oracle Java 10 is automatically set as the default, but on other distributions such as Linux Mint, you have to set it yourself.

```bash
sudo apt install oracle-java10-set-default
```

And with that, the installation is complete.

## 3. Verification

Let's verify that the installation went well.

```bash
java -version
```

If Oracle Java 10 was correctly installed as the default, you'll see a message like the following.

```bash
java version "10" 2018-03-20
Java(TM) SE Runtime Environment 18.3 (build 10+46)
Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10+46, mixed mode)
```

Let's check javac as well.


```bash
javac -version
```

If the installation went well, you'll see this message.

```bash
javac 10
```

Now let's start using Java 10!

