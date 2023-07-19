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
            stage("docker remove") {
                steps {
                    sh "sudo kill -9 \$(docker top backend | awk 'NR==2 {print \$2}')"
                    sh "sudo kill -9 \$(docker top backend | awk 'NR==2 {print \$2}')"
                }
            }
            stage("docker run") {
                steps {
                    sh 'docker run -p 9991:8086 -d --name backend backend:latest'
                    sh 'docker run -p 9992:8085 -d --name frontend frontend:latest'
                }
            }
            stage("docker deploy") {
                steps {
                    sh 'curl http://localhost:9991/docs'
                    sh 'curl http://localhost:9992'
                }
            }
        }
        post{
            always{
                cleanWs()
            }
        }       
}
