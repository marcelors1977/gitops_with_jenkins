pipeline {
    agent any

    def scmVars = checkout([$class: 'GitSCM', branches: [[name: 'master']], 
    userRemoteConfigs: [[url: 'https://github.com/marcelors1977/gitops_with_jenkins.git']]])
    env.GIT_COMMIT = scmVars.GIT_COMMIT
    env.GIT_BRANCH = scmVars.GIT_BRANCH
    env.GIT_URL = scmVars.GIT_URL

    stages {
        stage('Get Source Code and hash generate') {
            steps{
                git url: 'https://github.com/marcelors1977/gitops_with_jenkins.git', branch: 'master'                
            }

            // steps{
            //     dockerapp = docker.build("19061977/gitops_with_jenkins:${env.BUILD_ID}")
            // }
        }
    }
}