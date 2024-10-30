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


# Folder Structure
A Karate test script has the file extension .feature which is the standard followed by Cucumber. You are free to organize your files using regular Java package conventions.

The Maven tradition is to have non-Java source files in a separate src/test/resources folder structure - but we recommend that you keep them side-by-side with your *.java files. When you have a large and complex project, you will end up with a few data files (e.g. *.js, *.json, *.txt) as well and it is much more convenient to see the *.java and *.feature files and all related artifacts in the same place.

This can be easily achieved with the following tweak to your maven <build> section.

<build>
    <testResources>
        <testResource>
            <directory>src/test/java</directory>
            <excludes>
                <exclude>**/*.java</exclude>
            </excludes>
        </testResource>
    </testResources>        
    <plugins>
    ...
    </plugins>
</build>
This is very common in the world of Maven users and keep in mind that these are tests and not production code.

Alternatively, if using Gradle then add the following sourceSets definition

sourceSets {
    test {
        resources {
            srcDir file('src/test/java')
            exclude '**/*.java'
        }
    }
}
With the above in place, you don't have to keep switching between your src/test/java and src/test/resources folders, you can have all your test-code and artifacts under src/test/java and everything will work as expected.

Once you get used to this, you may even start wondering why projects need a src/test/resources folder at all !

# Naming Conventions
Since these are tests and not production Java code, you don't need to be bound by the com.mycompany.foo.bar convention and the un-necessary explosion of sub-folders that ensues. We suggest that you have a folder hierarchy only one or two levels deep - where the folder names clearly identify which 'resource', 'entity' or API is the web-service under test.

For example:

src/test/java
    |
    +-- karate-config.js
    +-- logback-test.xml
    +-- some-reusable.feature
    +-- some-classpath-function.js
    +-- some-classpath-payload.json
    |
    \-- animals
        |
        +-- AnimalsTest.java
        |
        +-- cats
        |   |
        |   +-- cats-post.feature
        |   +-- cats-get.feature
        |   +-- cat.json
        |   \-- CatsRunner.java
        |
        \-- dogs
            |
            +-- dog-crud.feature
            +-- dog.json
            +-- some-helper-function.js
            \-- DogsRunner.java
Assuming you use JUnit, there are some good reasons for the recommended (best practice) naming convention and choice of file-placement shown above:

Not using the *Test.java convention for the JUnit classes (e.g. CatsRunner.java) in the cats and dogs folder ensures that these tests will not be picked up when invoking mvn test (for the whole project) from the command line. But you can still invoke these tests from the IDE, which is convenient when in development mode.
AnimalsTest.java (the only file that follows the *Test.java naming convention) acts as the 'test suite' for the entire project. By default, Karate will load all *.feature files from sub-directories as well. But since some-reusable.feature is above AnimalsTest.java in the folder hierarchy, it will not be picked-up. Which is exactly what we want, because some-reusable.feature is designed to be called only from one of the other test scripts (perhaps with some parameters being passed). You can also use tags to skip files.
some-classpath-function.js and some-classpath-payload.json are in the 'root' of the Java 'classpath' which means they can be easily read (and re-used) from any test-script by using the classpath: prefix, for e.g: read('classpath:some-classpath-function.js'). Relative paths will also work.
For details on what actually goes into a script or *.feature file, refer to the syntax guide.

# JUnit 5
Karate supports JUnit 5 and the advantage is that you can have multiple methods in a test-class. Only 1 import is needed, and instead of a class-level annotation, you use a nice DRY and fluent-api to express which tests and tags you want to use.

Note that the Java class does not need to be public and even the test methods do not need to be public - so tests end up being very concise.

Karate will traverse sub-directories and look for *.feature files. For example if you have the JUnit class in the com.mycompany package, *.feature files in com.mycompany.foo and com.mycompany.bar will also be run. This is one reason why you may want to prefer a 'flat' directory structure as explained above.

Here is an example:

package karate;

import com.intuit.karate.junit5.Karate;

class SampleTest {

    @Karate.Test
    Karate testSample() {
        return Karate.run("sample").relativeTo(getClass());
    }
    
    @Karate.Test
    Karate testTags() {
        return Karate.run("tags").tags("@second").relativeTo(getClass());
    }

    @Karate.Test
    Karate testSystemProperty() {
        return Karate.run("classpath:karate/tags.feature")
                .tags("@second")
                .karateEnv("e2e")
                .systemProperty("foo", "bar");
    }

}
Note that more "builder" methods are available from the Runner.Builder class such as reportDir() etc.

You should be able to right-click and run a single method using your IDE - which should be sufficient when you are in development mode. But to be able to run JUnit 5 tests from the command-line, you need to ensure that the latest version of the maven-surefire-plugin is present in your project pom.xml (within the <build>/<plugins> section):

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.22.2</version>
</plugin>
To run a single test method, for example the testTags() in the example above, you can do this:

mvn test -Dtest=SampleTest#testTags
Also look at how to run tests via the command-line and the parallel runner.

# JUnit HTML report
When you use the JUnit runner - after the execution of each feature, an HTML report is output to the target/karate-reports folder and the full path will be printed to the console (see video).


html report: (paste into browser to view)
-----------------------------------------
file:///projects/myproject/target/karate-reports/mypackage.myfeature.html

You can easily select (double-click), copy and paste this file: URL into your browser address bar. This report is useful for troubleshooting and debugging a test because all requests and responses are shown in-line with the steps, along with error messages and the output of print statements. Just re-fresh your browser window if you re-run the test.
