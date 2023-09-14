/*
Project :  https://github.com/DevOpsWorld-9959/nodeProject_for_jenkins.git

Required plugins:  

-Nodejs -  
-Docker plugin -
-OWSAP dependency-check plugin-
-sonarQube scanner for jenkins

-Eclips Temurin installer (to get more option on tool configuration drop down)

-Install Trivy inside jenkins server 

Note: as per requirements we have to install required plugins.

Trouble Shoot I got and resolved  while running this script :  

     PermissionError: [Errno 13] Permission denied: 

     Solution: 

           # chmod 777 /var/run/docker.sock

*/

pipeline {
    agent any
    tools{
        nodejs "nodejs"
    }
    environment{
        SONAR_HOME = tool "sonar-scanner"
    }
    stages {
        stage('Git SCM CheckOut') {
            steps {
                git branch: 'main', url: 'https://github.com/DevOpsWorld-9959/nodeProject_for_jenkins.git' 
            }
        }
        
        stage("OWASP Scan(Dependency check scan)"){
            steps{
              dependencyCheck additionalArguments: '--scan ./',odcInstallation:'DC'
              dependencyCheckPublisher pattern:'**/dependency-check-report.xml'    
            }
        }
        
        stage("Trivy Security Scan"){
            steps{
               sh "trivy fs ."
            }
        }
        
        stage("SonarQube Code Analysis"){
            steps{
              withSonarQubeEnv('sonar') {
                sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=bank -Dsonar.projectKey=bank" 
              }  
            }
        }
        
        stage("Install NPM Dependencies"){
            steps{
                sh "npm install"
            }
        }
        
        stage("Install NPM Dependencies Backend"){
            steps{
               dir("/var/lib/jenkins/workspace/Bank/app/backend"){
                   sh "npm install"
               }
            }
        } 
        stage("Install NPM Dependencies Fronend"){
            steps{
               dir("/var/lib/jenkins/workspace/Bank/app/frontend"){
                   sh "npm install"
               }
            }
        }
        stage("Deploy to Containers"){
            steps{
                sh "npm run compose:up -d"
            }
        }
        
    }
}
