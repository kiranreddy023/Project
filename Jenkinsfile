pipeline{
    agent any
    tools {
        maven 'maven-3.6.3'
        
    }
    environment{
        imageNameBackend = "kiran023/backend"
        imageNameFrontend = "kiran023/frontend"
        registryUrl = "registry.hub.docker.com"
        registryCreds = 'docker'
        dockerImageBackend = ''
        dockerImageFrontend = ''
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
                script{
                    dir("backend"){
                        dockerImageBackend = docker.build imageNameBackend
                    }
                    dir("frontend"){
                        dockerImageFrontend = docker.build imageNameFrontend
                    }
                }
            }
        }
        stage('docker push'){
            steps{
                script{
                    docker.withRegistry("https://${registryUrl}", registryCreds){
                        dockerImageBackend.push()
                        dockerImageFrontend.push()                        
                    }
                }
            }
        }
        stage('k8s-deploy'){
            steps{
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8sconfig', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                        sh 'kubectl apply -f deploybackend.yml'
                        sh 'kubectl apply -f deployfrontend.yml'
                        sh 'kubectl apply -f svcbe.yml'
                        sh 'kubectl apply -f svcfe.yml'
                }
            }
        }
        stage('k8s-details'){
            steps{
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8sconfig', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                        sh 'kubectl get svc -n kiran -o wide'
                }
            }
        }
        stage('test-deployed'){
            steps{
                
                sh  'curl 20.213.252.174:8085'

                
                sh  'curl 20.213.248.175:8086/docs'
            }
        }
    }
    post {
        always {
            // One or more steps need to be included within each condition's block.
            cleanWs()
        }
    }
}