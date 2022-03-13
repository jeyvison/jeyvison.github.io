---
title: "Gradle Basic Tutorial"
date: 2017-01-15T15:40:38Z
draft: false
---

# Gradle Basics Tutorial
## 1. Overview

In this tutorial, we will cover some basics of Gradle, a polyglot build system.

## 2. Installation
### 2.1. Prerequisite

In order to install Gradle, you must have a Java JDK or JRE installed on your machine. Gradle will use whatever java installation it finds in your path.

### 2.2. Gradle

You can follow [this link](https://gradle.org/install) to download one of the Gradle distributions.


### 2.3. GRADLE_HOME Environment Variable

For running Gradle from every path in the system, we must first configure the GRADLE_HOME environment variable.

Add the variable GRADLE_HOME to your system and point it to the unpacked files downloaded from Gradle website.

Then, add GRADLE_HOME/bin to your PATH environment variable.

### 2.4. Testing the Installation

If GRADLE_HOME was successfully set to the path, you can run

```
gradle -v
```

in your command line tool to test the installation.

The output shows the Gradle version and some environment info like JVM version, OS version, etc.

## 3. Project and Tasks

You may have one or more projects built with Gradle. A project is composed of tasks((i.e Compile the source code, run the unit tests) that occur only once in the build execution.

Each project using Gradle defines a **build.gradle** file. The gradle command looks for a build.gradle in your current path. In this file, you can customize the tasks you want to run, define your dependencies and much more.

### 3.1. Build Script File

Let’s create a build.gradle file and define a task in it:

**build.gradle**

```
task hello { 
    println “hello, Gradle!” 
}
```

Let’s decompose this task:

- **task Hello** :You use the reserved word task to define a new unit of work or to modify a task that already exists(like compile, test,etc.). Every task must be named so it can be called by Gradle
- **println “Hello, Baeldung”**: The code block

To see it running, run

```
gradle hello
```

in your **build.gradle** directory and check the output.

### 3.2. Build Script Code

You can use Groovy(and Kotlin) to customize the build script. For example:

```
task hello { 
    def awesomeness = ‘awesome’
    println “this tutorial is ${awesomeness}” 
}
```

If you want to check the tasks that Gradle offers you, run

```
gradle tasks
```

## 4. Gradle Build and Dependency Management

In order to build a project, a set of configurations and tasks are needed so Gradle knows where to find the source code to compile, where are the tests, how to add the dependencies in the project and so on.

Fortunately, there’s a way to build our project without so much effort. We can use Gradle plugins.

In this tutorial, we will use the Java Plugin.

### 4.1. Java Plugin

The Java plugin has predefined configurations and tasks that help us to build a java project very easily. It uses Maven-like source sets to find the source code.

A Source-Set is a configuration that tells the plugin where to find the source code for compiling or testing.

For default, the Java source code is found under src/main/java.

So, to start let’s apply the java plugin to our build.gradle file:

```
apply plugin: ‘java’ task hello { 
    def awesomeness = ‘awesome’ 
    println “this Baeldung’s tutorial is ${awesomeness}” 
}
```

This unique line added did a lot to our project. Now, we can use all the tasks that the plugin offers us to build our java project.

To check the new tasks we can use, run

```
gradle tasks
```

in your build.gradle file directory.

### 4.2. Build the Project

Let’s create a Main.java file under src/main /java and compile it with our new tasks:

**src/main/java/Main.java**

```
public class Main{ 
    public static void main(String[] args){     
        System.out.println(“Hello, Medium”); 
    } 
}
```

In our build.gradle directory we can now run

```
gradle build
```

and check the tasks being executed.You can find the result of the build in the ./build folder.

### 4.3. Dependencies Management

Gradle offers you some ways to define your dependencies. Before to define any of them, we first need to tell Gradle from where it will get it.

For this, we can use the repositories configuration. There are some repositories we can use, but for this tutorial, we will use the maven central repository:

```
apply plugin: ‘java’ repositories{ 
    mavenCentral() 
} task hello { 
    def awesomeness = ‘awesome’ 
    println “this Baeldung’s tutorial is ${awesomeness}” 
}
```

Now we told Gradle that any external dependency we need, it must look at this repository for us. Let’s say now our project needs an external dependency, the Spring framework. We can declare it in a dependencies block like this:

```
apply plugin: ‘java’ repositories{ 
    mavenCentral() 
} dependencies{ 
    compile ‘org.springframework:spring-context:4.3.5.RELEASE’ 
} task hello { 
    def awesomeness = ‘awesome’ 
    println “this Baeldung’s tutorial is ${awesomeness}” 
}
```

So, what does it do?

- The block dependencies is where we define our dependencies.
- The compile (configuration added by the java plugin) word tells Gradle that this dependency is needed to compile our project’s source and therefore to run it too.
- Our dependency in the (**GAV**) format **group:artifactId:version**. This is a format standardized by Maven.You can check it here.

There is another format to use when declaring dependencies.If you prefer, you can declare it in the following way:

```
dependencies{ 
    compile group:’org.springframework’ , name:’spring-context’, version:’4.3.5.RELEASE’ 
}
```

## 5. Publishing Artifacts
### 5.1. The Artifact Name

We can use the dependency configurations to publish files to repositories. These files are called artifacts.

Before publishing our artifact to some repository we need first define some informations based on the GAV format we saw above.

In **build.gradle** we can define these informations in the following way:

```
group=’com.baeldung.tutorial’
version=’1.0.0' apply plugin: ‘java’ repositories{ 
    mavenCentral()
} dependencies{ 
    compile ‘org.springframework:spring-context:4.3.5.RELEASE’ 
} task hello { 
    def awesomeness = ‘awesome’ 
    println “this Baeldung’s tutorial is ${awesomeness}” 
}
```

The artifactId information is the name of our project(i.e the folder containing the build.gradle file). Run

```
gradle build
```

and check the version of our artifact in the ./build dir.

### 5.6. Publishing

There are two things we need to publish our artifact. First, we will use the Maven plugin. Second, we need to configure where is our repository. Our build.gradle file would look like this:

```
group=’com.baeldung.tutorial’ 
version=’1.0.0' apply plugin: ‘java’ 
apply plugin: ‘maven’ repositories{ 
    mavenCentral() 
} dependencies{ 
    compile ‘org.springframework:spring-context:4.3.5.RELEASE’ 
} task hello { 
    def awesomeness = ‘awesome’ 
    println “this Baeldung’s tutorial is ${awesomeness}” 
} 
uploadArchives { 
    repositories { 
        mavenDeployer { 
            repository(url: ‘http://yourmavenrepo/repository') {   
                authentication(userName: ‘user’, password: ‘password’); } 
        } 
    } 
}
```

The maven plugin adds the configuration we need to upload our artifact to a maven repository. The UploadArchives configuration is where we define the destination of our bundle with some other informations, like authentication.

If you have a maven repository configured you can run

```
gradle upload
```

and your artifact will be uploaded.
## 6. More on Gradle
### 6.1. The Gradle Wrapper

Gradle has a wrapper, so you don’t have to install it on every machine you are working. It contains the Gradle version you define, so you won’t have version issues when building your project.

To create a wrapper for our project we can run

```
gradle wrapper --gradle-version 3.2.1
```

where “3.2.1” is the version we wanna use.

This command creates the following files and directories:

- **gradlew**: A shell script file that executes the Gradle tasks
- **gradlew.bat**: A Windows batch script that executes the Gradle tasks
- **./gradle**: A directory containing a Gradle wrapper jar and a properties file indicating the version we are using and other configurations

To execute the wrapper you just need to run the gradlew.bat file if you are using windows or the gradlew file if you are using Linux/OS X.

### 6.2. Gradle Properties

We may define some configurations external to the build.gradle file. We need to create a gradle.properties in our project at the same level as our build script. Let’s create this file and define some properties in it:

**gradle.properties**

```
awesomeness=awesome
group=com.baeldung.tutorial 
version=1.0.0
```

With these properties defined, we can remove those variables from our build script.Our build.gradle should look like this now:

```
apply plugin: ‘java’ 
apply plugin: ‘maven’ repositories{ 
    mavenCentral() 
} dependencies{ 
    compile ‘org.springframework:spring-context:4.3.5.RELEASE’ 
} task hello { 
    println “this Baeldung’s tutorial is ${awesomeness}” 
} uploadArchives { 
    repositories { 
        mavenDeployer { 
            repository(url: ‘http://yourmavenrepo/repository') {
                authentication(userName: ‘user’, password: ‘password’); } 
        } 
    } 
}
```

To check if the properties are correctly set, we may run

```
gradle hello
````

and see if the property awesomeness is correctly substituted.

## 7. Conclusion

In this tutorial we have covered some Gradle basics and understood how to build a java project. You can check [this link](https://docs.gradle.org/3.2.1/userguide/userguide.html) to go through the user guide. If you want it, you can check the repo of this code in [this link](https://bitbucket.org/jeyvison_andrade/gradle_tutorial/src/master/).