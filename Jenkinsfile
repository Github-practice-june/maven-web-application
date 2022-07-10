pipeline {
    agent any
    tools {
        maven 'Maven 3.8.5'
    }
    triggers {
        pollSCM '* * * * *'
    }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')
        timestamps()
    }


    stages {
        stage('getCodeGit') {
            steps {
                git branch: 'development', credentialsId: 'd43640b0-f4a2-4e1b-a011-4e90de1f9e73', url: 'https://github.com/Github-practice-june/maven-web-application.git'
                notifyBuild('STARTED')
            }
        }
        stage('Build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('sonarQube') {
            steps {
                sh "mvn sonar:sonar"
            }
        }
        stage('Nexus') {
            steps {
                sh "mvn deploy"
            }
        }
        stage('tomcatDeploy') {
            steps {
                 sshagent(['d89efccd-008e-4084-843b-7ff4f80f5dc5']) {
                 sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.10.34:/opt/apache-tomcat-9.0.64/webapps"
                }
            }
            
        }
          
    }
    post {
        success {
            slackNotification('SUCCESS')
        }
        failure {
            slackNotification('FAILURE')
        }
    }
}
