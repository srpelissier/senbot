Senbot
======

Java based Cucumber and Selenium framework.

goals
======
Our goal is to make it easy to create, run and report on automated tests for webbased applications (by using behavior driven development).

features
======
* Cucumber based tests
* Selenium based tests using different browsers
* Run tests in parallel (Has some issue's)
* Generate readable reports (Work in progress)

prerequisites
======
To run the framework tests you minimal need:
* Java JDK v1.6+ (http://www.java.com)
* Maven (http://maven.apache.org/, install latest version in your local path)
* Google Chrome (www.google.com/chrome, for the default configured example tests)
* Chrome WebDriver (http://code.google.com/p/chromedriver/downloads/list, put the executable in your local path)
* Firefox (www.firefox.com, for the default configured example tests)

initial test run
=======
After the prerequisites are installed clone the repository<br>
Run a ```mvn clean install``` in the cloned directory<br>
This will build the senbot and fire the example tests

new project
=======
If you want to get started with SenBot use our Demo Archetype to create a new SenBot project showcasing the SenBot possibilities. First create your project using:
```
mvn archetype:generate -DarchetypeGroupId=com.gfk.senbot -DarchetypeArtifactId=SenBotDemo-archetype -DgroupId=com.yourdomain.namespace -DartifactId=YourProjectName
```

This will result in a new folder in your current directory called 'YourProjectName', ```cd``` into it and call ```mvn clean install```. This will start the maven build cycle which will also run all included cucumber tests utilizing Selenium. 

Runtime configuration
=======
You can configure by means of properties files and runtime variables. The properties files can be contributed as part of your project but can also be configured outside of the project on your local system to 
allow for flexible development setups.

* Provide a properties file directly on the on the classpath named ```senbot-runner.properties```.
* Add a properties file in your user root folder by the name senbot.properties. So ```~/senbot.properties```.
* Run you commands with a runtime variable ```-Dsenbot.properties``` pointing to your properties file. eg.  ```mvn clean install -Dsenbot.properties=/Users/me/Desktop/my-overwrite.properties```
* Last, you can provide a ```senbot-dev.properties``` file directly on your classpath which would generally be excluded form your version control to allow every dev to manage their different senbot projects with a user specific configuration.

It is also possible to overwrite reference data values by providing property values in your properties file. So let's say you have used an implementation of [ReferenceServicePopulator](https://github.com/gfk-ba/senbot/blob/master/SenBotRunner/src/main/java/com/gfk/senbot/framework/data/ReferenceServicePopulator.java)
to register a [GenericUser](https://github.com/gfk-ba/senbot/blob/master/SenBotRunner/src/main/java/com/gfk/senbot/framework/data/GenericUser.java) to the [SenBotReferenceService](https://github.com/gfk-ba/senbot/blob/master/SenBotRunner/src/main/java/com/gfk/senbot/framework/data/SenBotReferenceService.java).addUser("loginUser1", new GenericUser(...values...)) method.
By providing the property ```GenericUser.loginUser1.userName=anotherUserName```, you will overwrite the default value during execution allowing for execution specific data configurations. This setup applies to all POJO's contributed to the reference service.

Running cucumber selenium tests in parallel
=======
SenBot supports running cucumber selenium tests in parallel. If you use the [ParameterizedCucumber](https://github.com/gfk-ba/senbot/blob/master/SenBotRunner/src/main/java/com/gfk/senbot/framework/cucumber/ParameterizedCucumber.java) class as shown in the example test 
[ParameterizedCucumberTestBaseTest](https://github.com/gfk-ba/senbot/blob/master/SenBotRunner/src/test/java/com/gfk/senbot/framework/cucumber/tests/ParameterizedCucumberTestBaseTest.java) the feature files will be devided over multiple threads. By default 5 threads 
will be used but this can be overwritten by using the ```-Dthreads=n``` commandline argument. 

Some usefull runtime tips
=======
When running your SenBot through maven you can provide custom cucumber runtime options to append to the variables defined in your JUnit tests like so
```
mvn test -Dfeatures="--tags @myOtherTag"
```
or run a single feature file on a single runner like so
```
mvn test -Dtest=com.domain.NameOfYourJUnitTest -Dfeatures="path/to/your/file.feature"
```
Changing the default domain or test environment can be done with
```
mvn test -Durl=mydomain.com -Denv=FF
```
Changing target browser to run your tests against using the -Denv variable as shown below. Available options: FF, CH, IE, SF and phantomjs (for a headless browser). It is also possible to run your tests to multiple environments by providing the a variable like -Denv=FF,CH,phantomjs
```
mvn test -Denv=FF
```
If you add this option, the cucumber test results report is opened automatically for you at the end of your build.
```
mvn test -DopenReport
```

Logging options
=======
SenBot uses [slf4j](http://www.slf4j.org/) configured though [log4j](http://logging.apache.org/log4j/) for its logging. This allows you to overwrite how senbot logs its messages in your projects using SenBot.
The default SenBot logger is configured to log to PROJECT_ROOT/target/logs/senbot.log as specified in the [log4j.xml](https://github.com/gfk-ba/senbot/blob/master/SenBotRunner/src/main/resources/log4j.xml) file. If you need to add
log messages to your own StepDefinitions you can do so by adding a logger like so (example taken from the [SenBotContext](https://github.com/gfk-ba/senbot/blob/master/SenBotRunner/src/main/java/com/gfk/senbot/framework/context/SenBotContext.java)):
```
private static Logger log = LoggerFactory.getLogger(SenBotContext.class);
```
In your code you can then log your messages by calling:
```
log.trace("Trace message");
log.debug("Debug message");
log.info("Info message");
log.warn("Warn message");
log.error("Error message");
```