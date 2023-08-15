# Automated-Testing-OCP4-CICD
This project is an implementation of a Tekton CI/CD pipeline on Red Hat OpenShift 4, the main goal of this pipeline is to automate a series of static and dynamic tests over an e-learning web application made using Java 8 and Spring Boot.

Each task is a well-defined type of test, either Static or Dynamic, customized tasks and cluster tasks were used in the making of this CI/CD pipeline.
the main cluster task implemented in this pipeline is Maven Cluster Task provided by Red Hat OpenShift 4 Pipeline Builder UI, regarding that the addressed application is developed using Java 8 and Spring Boot as well as Maven as the project management tool.

Here is the pipeline's full view in the following screenshots:

![Screenshot from 2023-08-15 12-31-13](https://github.com/azamd/Automated-Testing-OCP4-CICD/assets/47691398/605d58f9-2ce2-4fd1-a538-ed9317d8f990)
![Screenshot from 2023-08-15 12-31-40](https://github.com/azamd/Automated-Testing-OCP4-CICD/assets/47691398/880c29a7-c1ae-4195-b6f7-c45e0a1e9412)

=> Tasks Description:
1- git-clone: the starting task that links the application source code with the pipeline by cloning its Git remote repository.

2- git-leaks: this task uses Gitleaks which is a SAST tool for detecting hard-coded secrets like passwords, API keys, and tokens in git repos.

3- owasp-dependency-check: the main goal of this task is to use OWASP Dependency Check which is a Software Composition Analysis (SCA) tool that attempts to detect publicly disclosed vulnerabilities contained within a project’s dependencies.
4- app-build: this task builds the application.

5- checkstyle: this task detects code-formatting violations based on pre-specified code-formatting rules and standards. 

6- unit-testing: this task launches unit tests, using JUnit 4 and Mockito.

7- pmd/cpd-reporting: these tasks are linked together and their common goal is to detect duplicated code blocks.

8- sonarqube-analysis: this task launches a SonarQube testing process, SonarQube is an open-source platform for continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs and code smells.

9- checkov-scanner: this task uses Checkov which is a static code analysis tool for infrastructure as code (IaC) and also a software composition analysis (SCA) tool for images and open source packages.
Notice: Checkov in this case was used to detect Dockerfile misconfigurations.

10- image-build-and-push: this task aims to build the application container image from a Dockerfile and push it to DockerHub.
Notice: This Task builds source into a container image using Google's Kaniko tool, kaniko doesn't depend on a Docker daemon and executes each command within a Dockerfile completely in userspace. This enables building container images in environments that can't easily or securely run a Docker daemon, such as a standard Kubernetes cluster.

11- grype-image-scan: this task aims to launch an automated container image scan to detect possible vulnerabilities, it uses Grype which is a vulnerability scanner for container images and filesystems.

12- deploy: this cluster task uses OpenShift CLI to update the application's deployment by replacing the current application container image with the latest image pulled from DockerHub.
Notice: there is a pre-deployed version of the application on the Red Hat Openshift Platform already (applicable manually using YAML files or with the assistance of OpenShift UI).

13- jmeter-load-testing: this task launches a series of pre-defined JMeter tests (HTTP requests) for analyzing and measuring the overall performance of the pre-deployed application.

14-selenium-e2e-testing: this task uses Selenium to launch automated end-to-end tests on Google Chrome browser to evaluate the execution/user experience delivered by the application.
