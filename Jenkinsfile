pipeline {
    agent any
    tools {
	    maven "MAVEN3"
	    jdk "OracleJDK8"
	}
    stages{
        stage('Fetch code') {
          steps{
              git branch: 'main', url:'https://github.com/kishanecosmob/jenkins-test.git'
          }  
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Test'){
            steps {
                sh 'mvn test'
            }

        }

        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'sonar4.7'
            }
            steps {
               withSonarQubeEnv('sonar') {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=jenkins-test \
                   -Dsonar.projectName=jenkins-test \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries= \
                   -Dsonar.junit.reportsPath=ansible \
                   -Dsonar.jacoco.reportsPath=ansible/templates \
                   -Dsonar.java.checkstyle.reportPaths=ansible/templates'''
              }
            }
        }

    }
}

