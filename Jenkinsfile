pipeline {
    agent any
    environment {
        git_url = 'https://github.com/marcelors1977/gitops_with_jenkins.git'
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
                    dockerImage = docker.build("${dockerhub_url}:${env.GIT_COMMIT_HASH}"
                    , '-f ./gowebserver_with_jenkins/Dockerfile .')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("${dockerhub_url}", 'dockerhub') {
                        dockerImage.push('latest')
                        dockerImage.push("${env.GIT_COMMIT_HASH}")
                    }
                }
            }
        }
    }
}
