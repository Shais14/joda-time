# DevOps-HW2
This project shows how to create a testing component and an analysis component that ensures  the correctness of a commit. We are using open source project Joda-Time (https://github.com/JodaOrg/joda-time) to develop the required CI components.

## Capabilities

The build server is created on an Amazon EC2 instance and uses Jenkins as the Continuous Integration tool and testing tools are added to check the coverage report and analysis of the errors (if any).
The testing and analysis component :
- Runs unit tets, measures the coverage and reports the results. Tool used is Cobertura.
- Test cases are generated using Randoop as a measure for advanced testing.
- Checkstyle is used to cover the basic static analysis and report the findings.
- A post commit hook is used as a gate to reject failing builds. Instead a previous build which resulted in a successful build is run so that you always have a asuccessful build.
- For custom metrics, the max number of conditions within an if statement and the maximum length of a method are being handled by features from checktyle. and for checking that no security token is being committed to the giit repo, a pre commit hook is being used.


##Build

The build server is hosted on an Amazon EC2 instance where in Jenkins is installed as a CI tool. To install Jenkins on an EC2 instance, the EC2 instance must have a security group defined which allows inbound and outbound traffic over ssh, smtp, http protocols. For the project, a new git repository is created (this one) which is then cloned locally. In the local directory, a maven project is initialized. This generates a pom.xml file which takes care of the dependencies needed for the project. 

##Installation Instructions 

- Clone the repository in a local folder
```
git clone https://github.ncsu.edu/sshaikh2/joda-time
```

### Configuring Jenkins

To configure Jenkins
- Install Github plugin, Maven Plugin, Cobertura plugin, Checkstyle plugin and Randoop Plugin
- As an SCM, Jenkins is linked with a git repository (this one).
- Maven commands 'clean' and 'install' - which are used for ensuring a clean build each time.
- In execute shell field, configure randoop by the following command
	-java -cp randoop-all-3.0.6.jar:target/classes randoop.main.Main gentests --classlist=Classes.txt --junit-output-dir=src/test/java/org/joda/time/  --timelimit=10
- To invoke top level Maven projects, the goals are:
	-test
	-install
	-cobertura:cobertura -Dcobertura.report.format=xml
	-checkstyle:checkstyle
- In the Post-build Actions, add 
	- Publish Checkstyle analysis results and in the field enter: **/target/checkstyle-result.xml
	- Publish Cobertura Coverage report and in the field enter: **/target/site/cobertura/coverage.xml

### Points to note

- For custom metrics using checkstyle, the checkstyle.xml file (in the root directory of the project) must contain,
```
<module name="BooleanExpressionComplexity">
   	<property name="max" value="5"/>
</module>
<module name="MethodLength">
   <property name="max" value="40"/>
</module>
```
- By defualt, the checkstyle plugin reports the warnings but it doesn't reject the commit. To enforce a hard check on the commits (reject the commits), replace
checkstyle:checkstyle by  checkstyle:checkstyle check in the top level maven goals.

##Demo

Here is a screencast of a successful build:
https://youtu.be/Up5ydmWNS2M

Here is a screencast of an un-successful build:
https://youtu.be/YFuxJjjpxKk


##Contributors:
Shais Shaikh - sshaikh2
Manav Verma - mverma4
Vineet Paul - vpaul



