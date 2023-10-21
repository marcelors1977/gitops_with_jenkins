pipeline {
    agent {
        label 'agent1'
    }
    environment {
        GIT_CREDENTIALS = credentials('git_access')
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        gitrepo = 'github.com/marcelors1977/gitops_with_jenkins.git'
        dockerHubRepo = '19061977/gitops_with_jenkins'
        PATH = "$PATH:/home/marcelo/bin"
        pathMaster = "$PWD"
    }

    stages {
        stage('Get Source Code') {
            steps {
                git url: "https://${gitrepo}", branch: 'master'
            }
        }

        stage('Get Hash of Source Code') {
            steps {
                script {
                    env.GIT_COMMIT_HASH = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                }
            }
        }

        stage('Docker login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Docker Image Build') {
            steps {
                script {
                    dockerImage = docker.build("${dockerHubRepo}", '-f ./Dockerfile .')
                }
            }
        }

        stage('Docker Image Push') {
            steps {
                script {
                    dockerImage.push('latest')
                    dockerImage.push("${GIT_COMMIT_HASH}")
                }
            }
        }

        stage('Update Kubernetes resources') {
            steps {
                script {
                    sh 'echo "Kustomization file updated when gitOps using ArgoCD"'
                    sh "cd ./k8s && kustomize edit set image goserver=${dockerImage.id}:${GIT_COMMIT_HASH}"
                }
            }
        }

        // Stage when deploy using ArgoCD
        stage('Push changes on Github') {
            steps {
                    sh('git add ./k8s/kustomization.yaml')
                    sh("git commit -m 'push new version of kustomization file to git'")
                    sh('git push https://$GIT_CREDENTIALS_PSW@$gitrepo')
            }
        }

        stage('Docker Image Purge from Jenkins Server') {
            steps {
                script {
                    sh "docker rmi ${dockerImage.id}:latest"
                    sh "docker rmi ${dockerImage.id}:${GIT_COMMIT_HASH}"
                }
            }
        }

        // Comment this stage when deploy using ArgoCD
        stage('Deploy to Kubernetes') {
            environment {
                tag_version = "${GIT_COMMIT_HASH}"
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://localhost:6443']) {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml'
                    sh 'echo "GitOps using update a image in deployment file and restart PODS immediately a gitpush"'
                    sh 'kubectl apply -f ./k8s/service.yaml,./k8s/deployment.yaml'
                }
            }
        }
    }
}
