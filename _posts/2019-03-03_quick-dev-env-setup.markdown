---
layout: post
title:  Quick Development Environment Setup
date:   2019-03-03 17:45:00
tags: cheatsheet git java gradle maven eclipse 
short_description: Basic "checklist" for setting up a fresh development environment with Git, Java, Maven, Gradle, and Eclipse.
mathjax: false
---

This is just a small checklist with commands to setup a small development environment. I usually like to setup these "user local" - i.e. the development toolre mostly installed _per user_, so that tools only needed for development are only available for the development user. Other users of the system do not need to be concerned. with that stuff.

We are going to set up the following tools:
* Git
* Java
* Maven
* Gradle
* Eclipse

The assumption is that the operation system is an Ubuntu derivative. For other systems you would need to use a different package manager and may need to edit `~/.bashrc` instead of `~/.bash_aliases`.

## Basic Install Organization

everything but Git is only user locally installed. I like to choose a central user "binaries" directory for that. This directory is `~/bin/installs`. Per development tool I have a sub-directory in this binaries directory. Each subdirectory contains the various versions of the tool and a symbolic link to the currently used "active"/default version.

## Git

Installs basic Git stuff as well as a simple GUI (_gitk_).

```shell
$ sudo apt-get install git-all
$ git config --global user.name "gitusername"
$ git config --global user.email "user@email"
```

## Java

This is an OpenJDK installation. Because of the major differences between Java 8 and later versions I always have at least two LTS versions installed. In this case Java 8 and Java 11. Note, that with Java 11, JavaFX is not bundled with the JDK anymore. So it needs to be installed separately. You can get the current JDK versions at [https://adoptopenjdk.net](https://adoptopenjdk.net).

THe Java versions installed with the following commands are JDK8-u202-b8 and JDK11-0-2_b9. The default system JDK is JDK11.

```shell
$ mkdir -p ~/bin/installs/java
$ cd ~/bin/installs/java

$ wget https://github.com/AdoptOpenJDK/openjdk8-binaries/releases/download/jdk8u202-b08/OpenJDK8U-jdk_x64_linux_hotspot_8u202b08.tar.gz
$ tar -xzf OpenJDK8U-jdk_x64_linux_hotspot_8u202b08.tar.gz

$ wget https://github.com/AdoptOpenJDK/openjdk11-binaries/releases/download/jdk-11.0.2%2B9/OpenJDK11U-jdk_x64_linux_hotspot_11.0.2_9.tar.gz
$ tar -xzf OpenJDK11U-jdk_x64_linux_hotspot_11.0.2_9.tar.gz

$ rm *tar.gz
$ ln -s jdk-11.0.2+9 jdk

$ echo "export JAVA_HOME=~/bin/installs/java/jdk" >> ~/.bash_aliases
$ echo "export PATH=\"\$JAVA_HOME/bin:\$PATH\"" >> ~/.bash_aliases

$ java -version

$ source ~/.bashrc
```

## Maven

The only extra special thing I like to do is to change the Maven lcoal repaostiory location. Dpeending on where the environment needs to be set up it may be necessary to add some custom remote repositories (for example when you are using Archiva) and proxy settings. Maven binaries can be downloaded from [https://maven.apache.org](https://maven.apache.org).

```shell
$ mkdir -p ~/bin/installs/maven
$ cd ~/bin/installs/maven
$ wget http://mirror.funkfreundelandshut.de/apache/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz
$ tar -xzf apache-maven-3.6.0-bin.tar.gz
$ rm *tar.gz
$ ln -s ~/bin/installs/maven/apache-maven-3.6.0 maven
$ mkdir ~/.m2
$ cp ~/bin/installs/maven/maven/conf/settings.xml ~/.m2/

$ echo "export M2_HOME=~/bin/installs/maven/maven" >> ~/.bash_aliases
$ echo "export PATH=\"\$M2_HOME/bin:\$PATH\"" >> ~/.bash_aliases
$ source ~/.bashrc

$ mvn -version

$ vi ~/.m2/settings.xml
```

Local repo, proxy, etc. in `~/.m2/settings.xml`:
```xml
<localRepository>/home/youruser/mavenrepo</localRepository>
```

## Gradle

Like for Maven I am changing the Gradle user home to a custom location. Basic Gradle installation instruction can be found at [https://gradle.org/install](https://gradle.org/install).

```shell
$ mkdir -p ~/bin/installs/gradle
$ cd ~/bin/installs/gradle
$ wget https://services.gradle.org/distributions/gradle-5.2.1-bin.zip
$ jar -xf gradle-5.2.1-bin.zip
$ rm *zip
$ ln -s ~/bin/installs/gradle/gradle-5.2.1 gradle
$ chmod a+x gradle/bin/gradle

$ echo "export GRADLE_USER_HOME=\"~/gradle\"" >> ~/.bash_aliases
$ echo "export GRADLE_HOME=~/bin/installs/gradle/gradle/bin" >> ~/.bash_aliases
$ echo "export PATH=\"\$GRADLE_HOME:\$PATH\"" >> ~/.bash_aliases
$ source ~/.bashrc
```

For bash auto completion of Gradle commands this can be added. Note, that "bash-completion" is required for it to work. On Ubuntu "bash-completion" should be already installed.

```shell
$ cd ~/bin/installs/gradle
$ wget https://raw.githubusercontent.com/gradle/gradle-completion/master/gradle-completion.bash
$ echo "source ~/bin/installs/gradle/gradle-completion.bash" >> ~/.bash_aliases
$ source ~/bin/installs/gradle/gradle-completion.bash
```

## Eclipse

The thing is that you can run Eclipse with JDK11. But there are a few issues. For example a number of plugins require JAXB. But JAXB is not part of JDK11 anymore. That is why a explicitly specify in the `eclipse.ini` that Eclispe is to be run with Java 8.

```shell
$ mkdir -p ~/bin/installs/eclipse
$ cd ~/bin/installs/eclipse
$ wget http://ftp.osuosl.org/pub/eclipse/technology/epp/downloads/release/2018-12/R/eclipse-jee-2018-12-R-linux-gtk-x86_64.tar.gz
$ tar -xzf eclipse-java-2018-12-R-linux-gtk-x86_64.tar.gz
$ rm *tar.gz
$ mv eclipse eclipse-2018-12
$ ln -s ~/bin/installs/eclipse/eclipse-2018-12 eclipse
$ echo "-vm" > eclipse.ini.tmp && echo "/home/$USER/bin/installs/java/jdk8u202-b08/bin" >> eclipse.ini.tmp && cat eclipse/eclipse.ini >> eclipse.ini.tmp && mv eclipse.ini.tmp eclipse/eclipse.ini
$ echo "export ECLIPSE_HOME=~/bin/installs/eclipse/eclipse" >> ~/.bash_aliases
$ echo "export PATH=\"\$ECLIPSE_HOME/eclipse:\$PATH\"" >> ~/.bash_aliases
$ source ~/.bashrc
```

My default additional plugins:
* Recommenders
* Darkest Dark Theme
* EGradle Editor

Configuration which I always change:
* Preferences >> Gradle (use the custom Gradle installation)
* Preferences >> Team >> Git (Git repo base)
* Preferences >> Java >> Installed JREs (add both JDKs)
* Preferences >> Java >> Editor >> Content Assist >> Favorites (add Assertj and JUnit)
