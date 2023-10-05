pipeline {
    agent any

    stages {
        stage('Get Source Code') {
            steps{
                git url: 'https://github.com/marcelors1977/gitops_with_jenkins.git', branch: 'master'
                GIT_COMMIT_HASH = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
            }

            // steps{
            //     dockerapp = docker.build("19061977/gitops_with_jenkins:${env.BUILD_ID}")
            // }
        }
    }
}