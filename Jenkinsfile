pipeline {
    agent {
        node 'slave'
    }
    
    tools {
        maven 'M3'
    }
    
    stages {
        stage('Git Clone') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/saeedhmohd244/sanatagenerator.git'
            }
        }
        
        stage('Create Artifact') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Docker Build and Push') {
            steps {
                withDockerRegistry(credentialsId: 'docker', url: 'https://index.docker.io/v1/') {
                    sh '''
                    docker build -t webapp:latest .
                    docker tag webapp:latest saeedhmohd244/webapp:latest
                    docker push saeedhmohd244/webapp:latest
                    '''
                }
            }
        }
        
        stage('tivy image scan') {
            steps {
                sh 'trivy image saeedhmohd244/webapp:latest'
            }
        }
        
        stage('Docker Run') {
            steps {
                withDockerRegistry(credentialsId: 'docker', url: 'https://index.docker.io/v1/') {
                    sh '''
                    docker stop container
                    docker rm container
                    docker run -d -p 8080:80 --name container webapp:latest
                    '''
                }
            }
        }
    }
}
