# Jenkins CI/CD – Complete Interview Notes (with Maven, Nexus & SonarQube)

This document contains **simple, interview-ready questions and answers** for Jenkins CI/CD, including Maven, Nexus, and SonarQube.  
Suitable for **DevOps technical rounds (0–3 years experience)**.

---

## 1️⃣ What is Jenkins?
**Answer:**  
Jenkins is an open-source CI/CD tool used to automate building, testing, and deploying applications.

---

## 2️⃣ What is CI/CD?
**Answer:**  
CI is the process of continuously building and testing code changes.  
CD is the process of automatically or semi-automatically deploying applications.

---

## 3️⃣ What is a Jenkins job?
**Answer:**  
A Jenkins job defines a set of steps Jenkins executes, such as building code, running tests, or deploying applications.

---

## 4️⃣ Difference between Freestyle Job and Pipeline Job?
**Answer:**  
Freestyle jobs are UI-based and harder to version control.  
Pipeline jobs are code-based (Jenkinsfile), versioned in Git, and better for complex workflows.

---

## 5️⃣ What is a Jenkinsfile?
**Answer:**  
A Jenkinsfile is a file stored in Git that defines the entire CI/CD pipeline as code.

---

## 6️⃣ Declarative vs Scripted Pipeline?
**Answer:**  
Declarative pipelines are structured and easier to maintain.  
Scripted pipelines are flexible and used for complex custom logic.

---

## 7️⃣ What are Jenkins stages?
**Answer:**  
Stages divide the pipeline into logical steps like Checkout, Build, Test, Scan, and Deploy.

---

## 8️⃣ What is a Jenkins agent?
**Answer:**  
A Jenkins agent is a node or container that executes pipeline jobs.

---

## 9️⃣ Why use Docker-based Jenkins agents?
**Answer:**  
They provide isolated and consistent environments, avoid dependency conflicts, scale easily, and reduce infrastructure cost.

---

## 🔟 How does Jenkins integrate with Git?
**Answer:**  
Jenkins pulls code from Git repositories using webhooks or polling and triggers builds on code changes.

---

## 1️⃣1️⃣ What is Maven and why is it used in Jenkins?
**Answer:**  
Maven is a build tool used to compile, test, and package applications.  
Jenkins uses Maven to automate Java application builds.

---

## 1️⃣2️⃣ Common Maven commands used in Jenkins?
**Answer:**  
- `mvn clean` – Cleans old builds  
- `mvn test` – Runs unit tests  
- `mvn package` – Creates JAR/WAR  
- `mvn deploy` – Uploads artifact to Nexus

---

## 1️⃣3️⃣ What is Nexus?
**Answer:**  
Nexus is an artifact repository used to store and manage build artifacts like JARs, WARs, and Docker images.

---

## 1️⃣4️⃣ Why do we use Nexus in CI/CD?
**Answer:**  
Nexus stores versioned build artifacts, avoids rebuilding code, enables rollback, and acts as a single source of truth.

---

## 1️⃣5️⃣ How does Jenkins work with Nexus?
**Answer:**  
Jenkins builds the application using Maven and uploads the artifact to Nexus.  
During deployment, Jenkins pulls the artifact from Nexus instead of rebuilding it.

---

## 1️⃣6️⃣ What happens if Nexus is down?
**Answer:**  
Builds may succeed locally, but deployment fails because artifacts cannot be uploaded or downloaded.

---

## 1️⃣7️⃣ How do you handle credentials in Jenkins?
**Answer:**  
Using Jenkins Credentials Manager to store secrets securely and reference them in pipelines.

---

## 1️⃣8️⃣ How do you troubleshoot Jenkins build failures?
**Answer:**  
Check console logs, identify the failing stage, verify credentials, tools, agent health, and external service availability.

---

## 1️⃣9️⃣ How do you handle approvals in Jenkins?
**Answer:**  
Using `input` steps in pipelines to require manual approval before deployment stages.

---

## 2️⃣0️⃣ How do you rollback a deployment using Jenkins?
**Answer:**  
By redeploying a previous stable artifact from Nexus.

---

# 🔥 ADDITIONAL IMPORTANT JENKINS QUESTIONS

---

## 2️⃣1️⃣ What are Jenkins parameters?
**Answer:**  
Parameters allow users to pass inputs to a Jenkins job at runtime, such as environment name, branch name, or action type.

---

## 2️⃣2️⃣ Types of Jenkins parameters
**Answer:**  
- String parameter  
- Choice parameter  
- Boolean parameter  
- Password parameter  
- File parameter  

---

## 2️⃣3️⃣ What are Jenkins executors?
**Answer:**  
Executors define how many jobs a Jenkins node can run in parallel.

---

## 2️⃣4️⃣ Why are executors important?
**Answer:**  
Executors control parallel execution and prevent overloading Jenkins nodes.

---

## 2️⃣5️⃣ What happens if no executor is available?
**Answer:**  
The job stays in the queue until an executor becomes free.

---

# 🔥 SONARQUBE (CODE QUALITY IN CI/CD)

---

## 2️⃣6️⃣ What is SonarQube?
**Answer:**  
SonarQube is a static code analysis tool used to detect bugs, vulnerabilities, and code smells.

---

## 2️⃣7️⃣ Why do we integrate SonarQube with Jenkins?
**Answer:**  
To enforce code quality checks automatically as part of the CI pipeline.

---

## 2️⃣8️⃣ What is a Quality Gate in SonarQube?
**Answer:**  
A Quality Gate is a set of rules that decide whether the build should pass or fail based on code quality metrics.

---

## 2️⃣9️⃣ What happens if Quality Gate fails?
**Answer:**  
The Jenkins pipeline fails, and developers must fix the issues before proceeding.

---

## 3️⃣0️⃣ SonarQube failure – interview-ready answer
**Answer:**  
If SonarQube analysis fails in Jenkins, I first check the Jenkins console logs to identify whether it’s an authentication, scanner, or Quality Gate issue. I verify the Sonar token and server URL configured in Jenkins and ensure the Sonar scanner and compatible Java version are available on the Jenkins agent. I then try to reproduce the issue locally using the same configuration. If the Quality Gate fails, the pipeline is expected to stop, and I notify the development team with the Sonar report for code fixes.

---

# 🔥 CI/CD DEPENDENCIES (END-TO-END)

---

## 3️⃣1️⃣ Dependencies required for a complete CI/CD pipeline
**Answer:**  
- Git (source code)
- Jenkins (CI/CD engine)
- Maven (build tool)
- SonarQube (code quality)
- Nexus (artifact repository)
- Docker (containerization)
- Cloud/Servers (EC2, Kubernetes, etc.)

---

## 3️⃣2️⃣ End-to-End CI/CD Flow (Java Application)
**Answer:**  
1. Developer pushes code to Git  
2. Jenkins triggers pipeline  
3. Maven builds and tests code  
4. SonarQube performs code analysis  
5. Artifact is uploaded to Nexus  
6. Jenkins pulls artifact from Nexus  
7. Application is deployed to server  
8. Manual approval (if required)

---

## ✅ FINAL NOTES
- All answers are **short, clear, and interview-safe**
- Suitable for **quick revision before technical rounds**
- Can be **extended later with Terraform, Docker, Kubernetes**

---

⭐ **Star this repo and revise before interviews**
