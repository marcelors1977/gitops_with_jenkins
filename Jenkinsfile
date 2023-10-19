pipeline {
    agent {
        label 'agent1'
    }
    environment {
        GIT_CREDENTIALS = credentials('git_access')
        gitrepo = 'github.com/marcelors1977/gitops_with_jenkins.git'
        dockerhubRegistry = 'https://registry.hub.docker.com'
        dockerhub_url = '19061977/gitops_with_jenkins'
        PATH = "$PATH:/home/marcelo/bin"
    }

    stages {
        stage('Get Source Code') {
            steps{
                git url: "https://${gitrepo}", branch: 'master', credentialsId: 'git_access'
            }
        }
        
        stage('Get Hash of Source Code') {
            steps {
                script {
                    env.GIT_COMMIT_HASH = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry("${dockerhubRegistry}", 'dockerhub') {
                        dockerImage = docker.build("${dockerhub_url}"
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
                        dockerImage.push("${GIT_COMMIT_HASH}")
                    }
                }
            }
        }
        
        stage('Update Kubernetes resources') {
            steps {
                script {
                    sh "cd ./k8s && kustomize edit set image goserver=${dockerImage.id}:${GIT_COMMIT_HASH}"
                }
            }
        }

        stage('Push changes on Github') {
            steps {
                    sh('git add ./k8s/kustomization.yaml')
                    sh("git commit -m 'push new version of kustomization file to git'")
                    sh('git push https://$GIT_CREDENTIALS_PSW@${gitrepo}')
            }
        }

        stage('Purge image created') {
            steps {
                script {
                    // docker.withRegistry("${dockerhubRegistry}", 'dockerhub') {
                        sh "docker rmi ${dockerImage.id}:latest"
                        sh "docker rmi ${dockerImage.id}:${GIT_COMMIT_HASH}"
                    // }
                }
            }
        }
    }
}