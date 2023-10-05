pipeline {
    agent any

    stages {
        stage('Get Source Code and hash generate') {
            steps{
                git url: 'https://github.com/marcelors1977/gitops_with_jenkins.git', branch: 'master'                
            }

            // steps{
            //     dockerapp = docker.build("19061977/gitops_with_jenkins:${env.BUILD_ID}")
            // }
        }
        stage('get sha') {
            steps{
                teste = sh(script: 'ls -la | wc -l', returnStdout: true)
            }
        }
    }
}