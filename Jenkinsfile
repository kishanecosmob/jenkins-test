pipeline {
    agent any
    
    environment {
        SONAR_TOKEN = credentials('sonartoken')
        NEXUS_CREDENTIALS = credentials('nexuslogin')
        MAVEN_HOME = tool 'MAVEN3'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Static Code Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQubeServer') {
                        sh 'mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN'
                    }
                }
            }
        }
        
        stage('Deploy to Nexus') {
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: 'http://172.16.18.116:8081',
                        groupId: 'ECOSMOB',
                        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                        repository: 'jenkins-test',
                        credentialsId: NEXUS_CREDENTIALS,
                        artifacts: [
                        [artifactId: 'jenkinstest',
                            classifier: '',
                            file: 'target/vprofile-v2.war',
                            type: 'war']
                        ]
                    )
                }
            }
        }
    }
    
    post {
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build or deployment failed!'
        }
    }
}

