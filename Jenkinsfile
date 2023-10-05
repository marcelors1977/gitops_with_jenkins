pipeline {
    agent any

    stages {
        stage('Get Source Code') {
            steps{
                git url: 'https://github.com/marcelors1977/gitops_with_jenkins.git', branch: 'master'
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
    }
}
