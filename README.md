maven-manifest-and-assembly-bug
===============================

Sample project to demonstrate a possible problem with maven jar plugin and snapshot versions

To reproduce the use case :
* clone this repo : git clone git://github.com/anthonydahanne/maven-manifest-and-assembly-bug.git
* cd maven-manifest-and-assembly-bug/
* add to your ~/.m2/settings.xml :

```
    <server>
        <id>snapshots</id>
        <username>deployment</username>
        <password>maven</password>
    </server>
```

in the servers section
* mvn clean deploy (that will install and deploy the cli module and its dependency, dependencyA to a nexus repo)
* you can check that the cli-1.1.0-SNAPSHOT.jar contains a manifest with :

```
Class-Path: dependencyA-1.1.0-SNAPSHOT.jar slf4j-jdk14-1.6.6.jar slf4j
 -api-1.6.6.jar
```

* clean your local repo : $ rm -rf ~/.m2/repository/com/terracotta/
* cd assembly && mvn clean install
* cd target 
* untar the assembly archive tar xvzf assembly-1.1.0-SNAPSHOT-bundle.tar.gz 


* you  will see :

```
x cli-1.1.0-20130110.023006-1.jar
x dependencyA-1.1.0-20130110.023002-1.jar
x slf4j-jdk14-1.6.6.jar
x slf4j-api-1.6.6.jar
```

* you should have seen :

```
x cli-1.1.0-20130110.023006-1.jar
x dependencyA-1.1.0-SNAPSHOT.jar
x slf4j-jdk14-1.6.6.jar
x slf4j-api-1.6.6.jar
```

The cli actually declares a dependency on dependencyA-1.1.0-SNAPSHOT.jar in its manifest, but the assembly got dependencyA-1.1.0-20130110.023002-1.jar


