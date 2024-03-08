pipeline {
    agent none
    stages {
        stage ('Testear crudphp') { 
            agent { 
                docker { image 'python:3'
                args '-u root:root'
                }
            }
            stages {
                stage('Clone') {
                    steps {
                        git branch:'main',url:'https://github.com/oscarsanabria80/examenic.git'
                    }
                }
            }
        }
        stage('Upload img') {
            agent any
            stages {
                stage('Build and push') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: 'DOCKER_HUB', url: '']) {
                            def dockerImage = docker.build("oscarsanabria80/php:v8")
                            dockerImage.push()
                            }
                        }
                    }
                }
                stage('Remove image') {
                    steps {
                        script {
                            sh "docker rmi oscarsanabria80/php:v8"
                        }
                    }
                }
            }
        }
        stage('Deploy') {
            agent any
            steps {
                script {
                    sshagent(credentials: ['SSH_VPS']) {
                        sh "ssh -o StrictHostKeyChecking=no oscar@oscarsanabria.blog wget https://raw.githubusercontent.com/oscarsanabria80/examenic/main/docker-compose.yml -O docker-compose.yaml"
                        sh "ssh -o StrictHostKeyChecking=no oscar@oscarsanabria.blog docker-compose up -d --force-recreate"
                    }
                }
            }
        }
    }
    post {
        always {
            mail to: 'oscarponcedeleonsanabria@gmail.com',
            subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
            body: "${env.BUILD_URL} has result ${currentBuild.result}"
        }
    }
}
