pipeline {
        agent any
        stages {
            stage('build') {
                steps {  
                     sh 'mvn clean install -DskipTests' 
                }
            }
            stage('test') {
                steps {
                    sh 'mvn test' 
                }
            }
            
            stage('run') {
                steps {  
                    sh 'mvn clean spring-boot:run &'
                }
            }
            stage('docker') {
                steps {
                        dir("frontend") {  
                             sh 'docker build -t frontend:latest .'   
                        }         
                        dir("backend") { 
                            sh 'docker build -t backend:latest .' 
                        }
                }
            }
            stage("docker run") {
                steps {
                    sh 'docker run -p 9901:8086 -d backend:latest'
                    sh 'docker run -p 9902:8085 -d frontend:latest'
                }
            }
            stage("docker deploy") {
                steps {
                    sh 'curl http://localhost:9901/docs'
                    sh 'curl http://localhost:9902'
                }
            }
        }
        post{
            always{
                cleanWs()
            }
        }       
}
