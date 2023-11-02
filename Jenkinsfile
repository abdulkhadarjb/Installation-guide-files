pipeline {

    agent any
        tools {
              maven 'M2_HOME'
            }
    stages {
         stage('checkout code') {
             steps {
                 git 'https://github.com/abdulkhadarjb/jenkins-task'
                
            }
        }
        stage('validate code') {
            steps {
                sh 'mvn validate'
            }
        }    
        stage('compile code') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Run unit test') {
            steps {
                sh 'mvn test'
            }
        }    
        stage('create build') {
            steps {
                sh 'mvn package'
            }
        }    
        stage('verify code') {
            steps {
                sh 'mvn verify'
            }
        }  
              stage("Maven Build"){
            steps{
                sh "mvn clean sonar:sonar package"
                
            }
        }
        stage('Upload War To Nexus'){
            steps{
                  nexusArtifactUploader artifacts: [
                       [
                            artifactId: 'vprofile', 
                            classifier: '', 
                            file: "target/vprofile-v2.war", 
                            type: 'war'
                       ]
                  ], 
                  credentialsId: 'nexus3', 
                  groupId: 'com.visualpathi', 
                  nexusUrl: '172.31.40.8:8081', 
                  nexusVersion: 'nexus3', 
                  protocol: 'http', 
                  repository: 'vr-releases', 
                  version: 'v2'  
              }
            }
        stage("deploy-dev"){
            steps{
                sshagent(['tomcat-credential']) {
                sh """
                    scp -o StrictHostKeyChecking=no target/vprofile-v2.war  ec2-user@172.31.41.172:/opt/tomcat/webapps/
                    
                    ssh ec2-user@172.31.41.172 /opt/tomcat/bin/shutdown.sh
                    
                    ssh ec2-user@172.31.41.172 /opt/tomcat/bin/startup.sh
                
                """
            }
            
            }
        }

    }
    post {
        always {
            echo 'Always'
        }
        success {
            echo 'Only on SUCCESS'
        }
          failure {
                   emailext body: 'jenkins pipeline failure', subject: 'jenkins pipeline failure', to: 'skabdul.marolix@gmail.com'
                   echo "Email sent for jenkins build fail"
        }
    }  
}
