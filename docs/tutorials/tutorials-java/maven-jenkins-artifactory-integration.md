---
title: Maven + Jenkins + Artifactory Integration
excerpt: >-
  In this quick tutorial, you will learn how to get started using Artifactory,
  Maven, and Jenkins in sync. Continue reading our simple step-by-step guide
  now.
---
Working at [Sinch](https://www.sinch.com/), I was recently set with the task of separating our application code from our sister company, as well as the test framework we use, so they can run separately. This is great, but for a tech company that has been running for eight years, this is quite an undertaking!

In this quick tutorial, I will show you how to get started using Artifactory, Maven, and Jenkins in sync.

Before this, we had the luxury of needing only one dependency, which was not a big overhead to keep, and exported JAR right in the project folder. This new separation made it obvious that we needed any remote server acting as an artifacts storage to get better transparency in code changes and version control.

This should take about 30 minutes to complete and you will need some familiarity with Maven to get started.

## Setting up Artifactory
![artifactory.png](images\6fc4bd1-artifactory.png)

First, download and install Artifactory from its [official site](http://www.jfrog.com/open-source/).

We are using a Windows server virtual machine as a base for the Artifactory service to run. To install Artifactory, all you need to do is open the downloaded archive, go to `%ARTIFACTORY_HOME%\bin` and execute the file `artifactory.bat`.

This script searches for the Java executable and runs Artifactory’s main class. After installing Artifactory, start the service either via Windows Services menu or using Command Prompt `(sc start|stop Artifactory)`.
![services.png](images\09117c0-services.png)

After that, Artifactory should be available at `http://SERVER_DOMAIN:8081/artifactory`. 

Next, we need to get it integrated into the CI server, which, in this case, is Jenkins. There is a pretty simple and user-friendly plugin for this called [Artifactory plugin](https://wiki.jenkins-ci.org/display/JENKINS/Artifactory+Plugin). After installing it, you should find a new ‘Artifactory’ section in your Jenkins settings, where you need to specify a Artifactory URL and user credentials.
![developer-rights.png](images\2f71604-developer-rights.png)

Now we have a pipeline that can publish artifacts to the remote Artifactory server.

The next step is configuring a job that will build our target project and push the compiled JAR to Artifactory.

Create a new job and, in the settings page, find the Build section and a specify path to the POM file. You will also need to add Maven goals to get your project files built. In this case, we would also like to synchronize the Jenkins build version with the deployed artifact’s version. This is made via passing Jenkins `BUILD_NUMBER` variable to Maven.
![build.png](images\4703352-build.png)

In the post-build actions section, add deploy artifacts to the Artifactory step, specify that you would like to add your artifacts, and check the ‘Deploy Maven Artifacts’ checkbox.
![deploy.png](images\c5a229c-deploy.png)

You will also need to specify a project name and path to your repository.
![source-settings.png](images\a8632d5-source-settings.png)

As a final step, modify the target project’s POM file. Make sure you have `Maven-compiler-plugin` and `Maven-jar-plugin` in the plugins section.

```html
<plugins>
     <plugin>
        <artifactId>Maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
          <source>1.6</source>
          <target>1.6</target>
        </configuration>
     </plugin>
     <plugin>
        <groupId>org.apache.Maven.plugins</groupId>
        <artifactId>Maven-jar-plugin</artifactId>
        <version>2.5</version>
        <configuration>
          <archive>
             <index>true</index>
             <manifest>
                <addClasspath>true</addClasspath>
             </manifest>
             <manifestEntries>
                <Implementation-Version>${build.number}</Implementation-Version>
             </manifestEntries>
          </archive>
        </configuration>
     </plugin>
</plugins>
```

It is also important to add groupId, artifactId, version, and packaging to the top-level element of the POM. Please note that we are using the `${build.number}` property, which we are getting from Jenkins as a parameter when the project is built.

```html
<groupId>CommonKeywords</groupId>
<artifactId>CommonKeywords</artifactId>
<version>0.0.1-SNAPSHOT-${build.number}</version>
<packaging>jar</packaging>
```

Bingo! Everything is set up to publish a compiled JAR file to a remote server. All you need to do is save all your changes, push it to your remote repository, and press the ‘Start building’ button in your Jenkins job that is connected to Artifactory.

After the job has finalized all of its steps in the ‘Build history’ section, you should see a link to Artifactory where all the produced artifacts are stored.
![build-history.png](images\9fb2b47-build-history.png)

From now on, it is possible to use published artifacts in other projects as a Maven dependency.

To set this up, add a dependency with the groupId and artifactId you specified in the previous steps. The version of the added dependency you can specify based on the version number that is deployed to Artifactory, e.g.:

```html
<dependency>
        <groupId>CommonKeywords</groupId>
        <artifactId>CommonKeywords</artifactId>
        <version>0.0.1-SNAPSHOT-54</version>
</dependency>
```

Basically, Maven searches all its dependencies in the central Maven repository. But we have not pushed our project there and that’s why we have to point Maven to our private Artifactory server. You can go to Artifactory and under ‘Artifacts’ in ‘Tree browser,’ it’s easy to find releases or snapshots repository. (We specified it in one of previous steps in job settings.)

On this page, there is a ‘Distribution management’ section, which is exactly where you need to add your POM to get a proper connection to Artifactory. Also, if you want to enable snapshots in addition to release versions, you will need to add the appropriate property to your ‘repository’ section in the POM. The other option is to separately define a snapshot and release repository.

```html
<repositories>
  <repository>
     <id>CLAESTOR</id>
     <name>CLAESTOR-snapshots</name>
     <snapshots>
                <enabled>true</enabled>
     </snapshots>
     <url>http://your_ip_address:8081/artifactory/snapshots</url>
  </repository>
</repositories>
```

And that’s it!

The following scheme shows how the combination of Maven, Jenkins, and Artifactory works in a split team environment to maintain a shared test automation framework.

![process1.png](images\17a04bb-process1.png)


![process2.png](images\0f098d3-process2.png)

A common workflow working with Artifactory is as follows:

> 1.  Changes in target project are pushed to Git repository.
> 2.  Jenkins gets latest code from Git and triggers a job.
> 3.  Jenkins job uses Maven goals to package a Jar (clean install) and to assign a proper version to the Jar `(-Dbuild.number=${BUILD_NUMBER})`.
> 4.  Jar file gets pushed to Artifactory by Jenkins job (as post-build action). Now the artifact is ready to be downloaded and used as a Maven dependency.
> 5.  Maven dependency (POM file) in projects that use target project are updated to the latest version available from Artifactory.
> 6.  Maven downloads dependency from Artifactory and stores it on a local machine `(../.m/repositories)`.

If you have any questions, please leave a comment and I will help you out.

Thanks,

Maryia Tuleika, Test Automation Developer at [Sinch](https://www.sinch.com/)

