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
        '''
        stage('sonarbuild'){
            steps{
                withSonarQubeEnv(credentialsId: 'kiransonarqube') {
                     sh 'mvn clean package sonar:sonar'
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
        '''
    }
}