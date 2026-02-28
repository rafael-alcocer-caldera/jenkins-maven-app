# simple-java-maven-app

Simplest Java Maven App running on Docker.

To run Jenkins with Maven in the simplest way, it's ideal to use the official Jenkins image and configure Maven from its interface, or use a small Dockerfile if you want it pre-installed.

Here is the recommended minimum configuration using Docker Compose:

```yml
version: '3.8'
services:
  jenkins:
    image: jenkins/jenkins:lts
    privileged: true
    user: root
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    restart: always

volumes:
  jenkins_home:
```

How to have Maven available <br/>
After running docker compose up -d: <br/>
Go to Manage Jenkins > Tools > Maven installations. <br/>
Click "Add Maven", give it a name (e.g. maven-3.9) and check the Install automatically box. <br/>
Jenkins will download it on its own when you need it in a Pipeline.

![Alt text](images/01.png)

To connect Jenkins to a real GitHub project and build it with Maven, follow these structured steps:

1. Prepare Jenkins credentials
   If your repository is private, or to avoid GitHub limits, you need to set up a Personal Access Token (PAT):
   In Jenkins, go to Manage Jenkins > Credentials > System > Global credentials Jenkins.io.
   Click on Add Credentials.
   Kind: Select Username with password Medium.
   Username: Your GitHub username.
   Password: Paste your GitHub Personal Access Token.
   ID: Give it something like github-creds.

2. Create the Pipeline in Jenkins
   Click on New Item, give it a name (e.g. mi-proyecto-maven) and choose Pipeline Contabo.
   In the Pipeline section, change the definition to Pipeline script from SCM Jenkins.io.
   Select Git and enter your GitHub repository URL (e.g. https://github.com).
   In Credentials, select the ones you created (github-creds) Medium.
   Make sure the branch is correct (usually */main or */master).

3. The Jenkinsfile (In your repository)
   For Jenkins to know what to do, create a file called Jenkinsfile in the root of your GitHub repository with this content:

```jenkins
pipeline {
    agent any
    tools {
        // Usa el nombre que configuraste en "Global Tool Configuration"
        maven 'maven-3.9' 
    }
    stages {
        stage('Checkout') {
            steps {
                // Jenkins descarga el código automáticamente desde SCM
                checkout scm
            }
        }
        stage('Build & Test') {
            steps {
                // Comando estándar de Maven para compilar y probar
                sh 'mvn clean install'
            }
        }
    }
}
```
