pipeline {
    agent any

    stages {
        stage('Get Source Code and hash generate') {
            steps{
                git url: 'https://github.com/marcelors1977/gitops_with_jenkins.git', branch: 'master'
                environment {
                    teste = sh(script: 'git rev-parse HEAD', returnStdout: true)
                }
                sh('echo $teste')
            }
        }
    }
}
