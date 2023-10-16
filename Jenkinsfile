pipeline {
    agent {
        label 'agent1'
    }
    environment {
        git_url = 'https://github.com/marcelors1977/gitops_with_jenkins.git'
        dockerhubRegistry = 'https://index.docker.io/v1'
        dockerhub_url = '19061977/gitops_with_jenkins'
    }

    stages {
        stage('Get Source Code') {
            steps{
                git url: "${git_url}", branch: 'master'
            }
        }
        
        stage('Get Hash of Source Code') {
            steps {
                script {
                    env.GIT_COMMIT_HASH = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                    sh('echo $GIT_COMMIT_HASH')
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry("${dockerhubRegistry}", 'dockerhub') {
                        dockerImage = docker.build("${dockerhub_url}:${env.GIT_COMMIT_HASH}"
                    , '-f ./Dockerfile .')
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("${dockerhubRegistry}", 'dockerhub') {
                        dockerImage.push('latest')
                        dockerImage.push("${env.GIT_COMMIT_HASH}")
                    }
                }
            }
        }
        
        stage('Update Kubernetes resources') {
            steps {
                script {
                    sh "cd ./k8s && /home/marcelo/bin/kustomize edit set image goserver=${dockerImage.id}"
                }
            }
        }

        stage('Purge image created') {
            steps {
                script {
                    docker.withRegistry("${dockerhubRegistry}", 'dockerhub') {
                      sh "docker rmi ${dockerImage.id}"
                    }
                }
            }
        }
    }
}