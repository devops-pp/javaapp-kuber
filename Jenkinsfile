pipeline {
    
    agent any
    
    
    
    tools {
        maven "MVN3"
        dockerTool "docker"
    }
    
    stages {
        stage('pullscm') {
            steps {
                git credentialsId: 'github', url: 'git@github.com:devops-pp/javaapp-kuber.git'
            }
        }
        
        stage('build') {
            steps {
                sh "mvn -f kubernetes-java clean install"
            }
        }
        
        stage('build docker image') {
            steps {
                script {
                    dockerImage = docker.build("pspal83/javaapp-k8s","kubernetes-java")
                }
            }
        }
        
        stage('Push docker image') {
            steps {
                script {
                    docker.withRegistry( '', 'docker') {
                        dockerImage.push ("$BUILD_NUMBER")
                    }
                }
            }
        }
        
        stage ('Kube Deployment') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'kube']) {
                        sh "sed -i s/latest/$BUILD_NUMBER/g kubernetes-java/deploy.yml"
                        sh "kubectl apply -f kubernetes-java/deploy.yml"
                        sh "sleep 10 && kubectl get svc"
                    }
                }
            }
        }
    }
}
