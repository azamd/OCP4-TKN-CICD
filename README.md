# OCP4-TEKTON-CICD
This project is an implementation of a Tekton CI/CD pipeline on Red Hat OpenShift 4, the main goal of this pipeline is to automate a series of static and dynamic tests over an e-learning web application made using Java 8 and Spring Boot.

The following screenshot shows our web application's home page.

Ez-Learning Git repo: https://github.com/donnatto/ez-learning

![image](https://github.com/azamd/Automated-Testing-OCP4-CICD/assets/47691398/1a47bd13-45d1-4823-a59d-cb255373157a)

I've forked the previous Git repo to be able to add the necessary updates like maven dependencies/plugins, unit tests, and selenium E2E tests.

Each task is a well-defined type of test, either Static or Dynamic, customized tasks and cluster tasks were used in the making of this CI/CD pipeline.
the main cluster task implemented in this pipeline is Maven Cluster Task provided by Red Hat OpenShift 4 Pipeline Builder UI, regarding that the addressed application is developed using Java 8 and Spring Boot as well as Maven as the project management tool.

# Used technologies and tools:

![Screenshot from 2023-11-10 15-59-28-1](https://github.com/azamd/OCP4-TKN-CICD/assets/47691398/2fecd096-a1b8-4083-9d8e-d44d54c3e26f)

- Red Hat OpenShift 4: the main platform where I've implemented the CI/CD pipeline and deployed the application.
- Tekton: a powerful yet flexible Kubernetes-native open-source framework for creating continuous integration and delivery (CI/CD) systems, OCP4 uses Tekton to build CI/CD pipelines.
- JUnit: an open-source testing framework for Java programming language that provides a standardized way to write and execute unit tests.
- Mockito: an open-source Java framework used for creating and working with mock objects in unit testing.
- Gitleaks: a SAST tool for scanning Git repositories, it detects hardcoded secrets like passwords, API keys, and tokens.
- OWASP-Dependency-Check: an SCA tool that detects vulnerabilities within a project's dependencies.
- Checkstyle: an open-source static code analysis tool that ensures that Java code adheres to a set of coding standards and guidelines.
- PMD/CPD: detect duplicated code blocks.
- SonarQube: an open-source platform for continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs and code smells.
- Checkov: a static code analysis tool for infrastructure as code (IaC) and also a software composition analysis (SCA) tool for images and open source packages.
- Buildah: an open-source tool developed by Google that enables users to build container images from a Dockerfile, inside a container or Kubernetes cluster, without requiring a Docker daemon.
- Grype: an open-source vulnerability scanner for container images and filesystems, it identifies known vulnerabilities and potential security issues in container images and their components.
- Apache JMeter: an open-source performance testing tool, primarily used for load testing, performance testing, and functional testing of web apps, web services, databases, and other software systems.
- Taurus: combined with Apache JMeter, Taurus outputs a fully detailed report on the application's endpoints and their respective status.
- OWASP ZAP: a DAST tool that simulates different types of attacks and outputs full reports on the application's security vulnerabilities by scanning its API endpoints and identifying weaknesses like SQL Injection, XSS, and CSRF.    
# Pipeline Description:
- Pipeline overview:

![Screenshot from 2023-08-15 12-31-13](https://github.com/azamd/Automated-Testing-OCP4-CICD/assets/47691398/605d58f9-2ce2-4fd1-a538-ed9317d8f990)
![Screenshot from 2023-08-15 12-31-40](https://github.com/azamd/Automated-Testing-OCP4-CICD/assets/47691398/880c29a7-c1ae-4195-b6f7-c45e0a1e9412)

- In the following section, I elaborate with a comprehensive explanation of each task:

1- git-clone: the starting task that links the application source code with the pipeline by cloning its Git remote repository.

2- git-leaks: this task uses Gitleaks which is a SAST tool for detecting hard-coded secrets like passwords, API keys, and tokens in git repos.

3- owasp-dependency-check: the main goal of this task is to use OWASP Dependency Check which is a Software Composition Analysis (SCA) tool that attempts to detect publicly disclosed vulnerabilities contained within a project’s dependencies.

4- app-build: this task builds the application.

5- checkstyle: this task detects code-formatting violations based on pre-specified code-formatting rules and standards. 

6- unit-testing: this task launches unit tests, using JUnit 4 and Mockito.

7- pmd/cpd-reporting: these tasks are linked together and their common goal is to detect duplicated code blocks.

8- sonarqube-analysis: this task launches a SonarQube testing process, SonarQube is an open-source platform for continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs and code smells.

9- checkov-scanner: this task uses Checkov which is a static code analysis tool for infrastructure as code (IaC) and also a software composition analysis (SCA) tool for images and open source packages.

->Notice: Checkov in this case was used to detect Dockerfile misconfigurations.

10- image-build-and-push: this task aims to build the application container image from a Dockerfile and push it to DockerHub.

->Notice: This Task builds source into a container image using Google's Kaniko tool, kaniko doesn't depend on a Docker daemon and executes each command within a Dockerfile completely in userspace. This enables building container images in environments that can't easily or securely run a Docker daemon, such as a standard Kubernetes cluster.

Hint: The configuration of DockerHub credentials is possible through a secret YAML file that you can link with the image-build-and-push task as a workspace in order to be able to push the application image to DockerHub, you can simply type the following oc command on the OpenShift command line terminal: oc create secret docker-registry docker-config --docker-server=https://index.docker.io/v1/ --docker-username=<your-username> --docker-password=<your-password> --docker-email=<your-email> 

PS: "docker-config" is the name of the secret that I've chosen you can pick your own secret name.

11- grype-image-scan: this task aims to launch an automated container image scan to detect possible vulnerabilities, it uses Grype which is a vulnerability scanner for container images and filesystems.

12- deploy: this cluster task uses OpenShift CLI to update the application's deployment by replacing the current application container image with the latest image pulled from DockerHub.

->Notice: there is a pre-deployed version of the application on the Red Hat Openshift Platform already (applicable manually using YAML files or with the assistance of OpenShift UI).

13- jmeter-load-testing: this task launches a series of pre-defined JMeter tests (HTTP requests) for analyzing and measuring the overall performance of the pre-deployed application.

14- Taurus performance testing: this task alongside the previously executed JMeter task will report the respective status of our application API endpoints (this report is the visual interpretation of our JMeter JMX testing scenario).

15- OWASP-ZAP: this task will launch a simulation of passive attacks through OWASP ZAP to help identify the application's security weaknesses, such as SQL Injection, XSS, and CSRF with full reports.
IMPORTANT NOTE: the maven cluster tasks used in this pipeline are: 3, 4, 5, 6, 7(both), 8, 13, and 14. each maven cluster task is an execution of a maven command with specific goals (example: "mvn clean package -DskipTests" for task n°4), it is also required to specify the project source directory and maven-settings as workspaces, and its context (default: "."). 

For a better understanding here's a detailed screenshot of task n°4:

![Screenshot from 2023-08-15 14-27-29](https://github.com/azamd/Automated-Testing-OCP4-CICD/assets/47691398/dfde3525-a850-4ce9-9f47-37fbe5d65971)

-> Notice the usage of maven goals: "clean" and "package" / -DskipTests: for skipping tests.

![Screenshot from 2023-08-15 14-30-36](https://github.com/azamd/Automated-Testing-OCP4-CICD/assets/47691398/48121ede-8963-4c5d-81e9-7833e76994ea)

-> Notice "." as the default value of The context directory within the repository for sources on which we want to execute maven goals.

-> Notice the addition of the required workspaces: source and maven-settings (PVCs [PersistentVolumeClaim] are needed for your workspaces).
