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
                    sh 'docker build -t kiran023/backend:latest .'
                }
                dir("frontend"){
                    sh 'docker build -t kiran023/frontend:latest .'
                }
            }
        }
        stage('docker push'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'pwd', usernameVariable: 'user')]) {
                    sh 'docker login -u kiran023 -p kiraN023@'
                    sh 'docker push kiran023/backend:latest'
                    sh 'docker push kiran023/frontend:latest'
                }
            }
        }
        stage('k8s-deploy'){
            steps{
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8sconfig', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                        sh 'kubectl apply -f ns.yml'
                }
            }
        }
    }
}