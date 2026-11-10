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
                ./mvnw org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                  -Dsonar.projectKey=devops-cicd-demo \
                  -Dsonar.host.url=http://localhost:9000 \
                  -Dsonar.token=$SONAR_TOKEN
            '''
        }
    }
}
                stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-credentials',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASSWORD'
                )]) {
                    sh '''
                        set +x
                        umask 077
                        trap 'rm -f settings.xml' EXIT

                        python3 - <<'PY'
import os
import xml.etree.ElementTree as ET

settings = ET.Element("settings")
servers = ET.SubElement(settings, "servers")

for server_id in ["nexus-releases", "nexus-snapshots"]:
    server = ET.SubElement(servers, "server")
    ET.SubElement(server, "id").text = server_id
    ET.SubElement(server, "username").text = os.environ["NEXUS_USER"]
    ET.SubElement(server, "password").text = os.environ["NEXUS_PASSWORD"]

ET.ElementTree(settings).write(
    "settings.xml",
    encoding="UTF-8",
    xml_declaration=True
)
PY

                        ./mvnw deploy -s settings.xml
                    '''
                }
            }
        }
}
}
