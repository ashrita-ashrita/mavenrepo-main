pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        SONAR_SERVER = 'sonarqube'
        TOMCAT_URL = 'http://13.234.231.55:8080/'
        NEXUS_URL = '13.203.208.175:8081'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ashrita-ashrita/mavenrepo-main.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${sonarqube}") {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Package Artifact') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[
                    artifactId: 'my-webapp',
                    classifier: '',
                    file: 'target/my-webapp.war',
                    type: 'war'
                ]],
                credentialsId: 'Nexus',
                groupId: 'com.example',
                nexusUrl: "${http://13.203.208.175:8081/}",
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: 'my-webapp01',
                version: '1.0-SNAPSHOT'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat9(
                    credentialsId: 'tomcat-creds',
                    url: "${http://13.234.231.55:8080/}"
                )],
                contextPath: 'my-webapp03',
                war: '**/*.war'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully 🚀'
        }
        failure {
            echo 'Pipeline failed ❌'
        }
    }
}
