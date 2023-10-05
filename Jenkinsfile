pipeline {
    agent any

    environment {
        teste = sh(script: 'git rev-parse --short HEAD', returnStdout: true)
    }

    stages {
        stage('Get Source Code and hash generate') {
            steps{
                git url: 'https://github.com/marcelors1977/gitops_with_jenkins.git', branch: 'master'                
            }

            println "Hash: ${env.teste}"
        }

    }
}