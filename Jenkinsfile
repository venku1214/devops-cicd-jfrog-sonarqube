pipeline {
    agent any

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
    }
}
