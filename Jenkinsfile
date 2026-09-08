pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven-3.9.12'
    }

    stages {

        stage('Build & Test') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('SonarQube') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'sonar-token',
                        variable: 'SONAR_TOKEN'
                    )
                ]) {
                    sh '''
                        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar \
                          -Dsonar.projectKey=spring-framework-petclinic \
                          -Dsonar.host.url=http://172.20.66.151:9000 \
                          -Dsonar.token=$SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                configFileProvider([
                    configFile(
                        fileId: 'nexus-maven-settings',
                        variable: 'MAVEN_SETTINGS'
                    )
                ]) {
                    sh '''
                        mvn -s "$MAVEN_SETTINGS" deploy -DskipTests
                    '''
                }
            }
        }
    }
}
