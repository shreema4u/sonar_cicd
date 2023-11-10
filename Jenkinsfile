pipeline {
    agent any
    tools{
        maven '3.9.5'
    }
    stages {
        stage('build maven'){
            steps{
                sh """echo ${Branch}"""
                checkout scmGit(branches: [[name: '*/${Branch}']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/SmrutiChandan/devops-automation']])
                sh 'mvn clean install'
              
            }
        }
        stage('static code analysis'){
            environment{
                SONAR_URL = "http://35.154.6.92:9000"
            }
            steps{
                withCredentials([string(credentialsId: 'SonarQube', variable: 'sonar')]) {
                sh 'mvn sonar:sonar -Dsonar.login=$sonar -Dsonar.host.url=${SONAR_URL}'
}
            }
        }
        stage('build docker image'){
            steps{
                script{
                    sh 'docker build -t smrutichandan20/devops-integration .'
                }
            }
            
        }
        stage('Deploy to Production') {
            steps {
                sh 'docker stop devops-integration || true'
            
                
                sh 'docker rm devops-integration || true'
            
               
                sh 'docker run -d --name devops-integration devops-integration'
            }
        }
        stage('push to docker hub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                        sh 'docker login -u smrutichandan20 -p ${dockerhub}'
                        
}
                        sh 'docker push smrutichandan20/devops-integration'
                }
                    
                }
            }
        }
    }