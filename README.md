# KarateAPIFramework
Sample framework for Karate API automation

# Getting Started
Karate requires at least Java 17 and then either Maven, Gradle, or a Java IDE that embeds either to be installed.
Recommended IDE : Eclipse

# Maven
All you need is available in the karate-core artifact. You can run tests with this directly, but teams can choose the JUnit variant (shown below) that pulls in JUnit 5 and slightly improves the in-IDE experience.

<dependency>
    <groupId>io.karatelabs</groupId>
    <artifactId>karate-junit5</artifactId>
    <version>1.5.0</version>
    <scope>test</scope>
</dependency>


# Quickstart
It may be easier for you to use the Karate Maven archetype to create a skeleton project with one command. You can then skip the next few sections, as the pom.xml, recommended directory structure, sample test and JUnit 5 runners - will be created for you.


You can replace the values of com.mycompany and myproject as per your needs.

mvn archetype:generate \
-DarchetypeGroupId=io.karatelabs \
-DarchetypeArtifactId=karate-archetype \
-DarchetypeVersion=1.5.0 \
-DgroupId=com.mycompany \
-DartifactId=myproject
This will create a folder called myproject (or whatever you set the name to).
