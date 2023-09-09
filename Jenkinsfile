pipeline{
    agent any
    tools {
        maven 'maven-3.6.3'
        
    }
    stages{
        stage('maven test'){
            steps{
                sh 'mvn clean test'
            }
        }
    
        stage('maven package'){
            steps{
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage("sonarqube build"){
            steps{
                withSonarQubeEnv('kiransonarqube') {
                    sh "mvn clean package sonar:sonar"
                }
            }
        }
        stage("jfrog upload"){
            steps{
                rtUpload(
                    serverId: 'kiranjfrog',
                    spec: '''{
                        "files": [
                            {
                                "pattern": "**/**/*.*ar",
                                "target": "project/"
                            }
                        ]
                    }'''
                )
            }
        }
        stage('dockerbuild'){
            steps{
                dir("backend"){
                    sh 'docker build -t kiran1993.azurecr.io/backend:latest .'
                }
                dir("frontend"){
                    sh 'docker build -t kiran1993.azurecr.io/frontend:latest .'
                }
            }
        }
        stage('docker push'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'ACR', passwordVariable: 'pwd', usernameVariable: 'user')]) {
                    sh 'docker login -u ${user} -p ${pwd} kiran1993.azurecr.io'
                }
            }
            steps{
                sh 'docker push kiran1993.azurecr.io/backend:latest'
            }
            steps{
                sh 'docker push kiran1993.azurecr.io/frontend:latest'
            }
        }
    }
}