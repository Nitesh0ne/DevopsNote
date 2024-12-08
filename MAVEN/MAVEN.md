MAVEN: 
Maven is a build/project management tool.
Build: adding dependencies to code.
dependencies: packages req to run the code.
maven is used to manage dependencies for the code.

HISTORY:
company: apache software Foundations.
year: 2004
language: java-1.8.0
kind: free and opensource

ARTIFACTS:
it is the final product of a source code.
code -- > build & test -- > artifact

.jar = compile java -- > .class -- > .jar = backend
.war = frontend + backend code
.ear = 

POM.XML: contains info about the project and dependencies of code.
target: artifacts will store on this folder. 

INSTALLATION:
yum install java-1.8.0-openjdk maven git -y
git --version 
mvn --version

git clone https://github.com/devopsbyraham/jenkins-java-project.git
cd jenkins-java-project.git



MAVEN GOALS:
it is a command used to execute tasks.
1. mvn compile: to compile the code
2. mvn test: to test the code
3. mvn package: to create war/jar file
4. mvn install: to copy the war file to local repo (.m2 folder)
5. mvn deploy: to generate the war file
6. mvn clean: to delte the artifacts

mvn clean package: used to generate the war file.


BUILD TOOLS:
c: make file
.net: visual code
java: maven, ant, gradle
python: gradle

MAVEN VS ANT:

maven is procedural, ant is declarative.
maven: pom.xml, ant: build.xml
maven: lifecycle, ant: not have a lifecycle
maven: plugins (reusable), ant: scripts (not reusable)


plugins: is a small software we can use without installing tool.


