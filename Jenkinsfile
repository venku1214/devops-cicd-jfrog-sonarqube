pipeline {
    agent {
    label 'ubuntu-wsl-agent'
}
    tools {
        jdk 'JDK17'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/venku1214/devops-cicd-jfrog-sonarqube.git',
                    credentialsId: 'github-credentials'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package'
            }
        }
       stage('SonarQube Analysis') {
            steps {
                 withCredentials([string(
                 credentialsId: 'sonarqube-token',
                 variable: 'SONAR_TOKEN'
        )]) {
            sh '''
                ./mvnw sonar:sonar \
                  -Dsonar.projectKey=devops-cicd-demo \
                  -Dsonar.host.url=http://localhost:9000 \
                  -Dsonar.token=$SONAR_TOKEN
            '''
        }
    }
}
    }
}
