pipeline {
    agent any

    stages {
        stage('1. Init SSH') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'ssh-elma1-pass-and-login',
                            usernameVariable: 'SSH_USER',
                            passwordVariable: 'SSH_PASS'
                        )
                    ]) {
                        remote = [
                            name: 'elma',
                            host: remoteHost,
                            user: SSH_USER,
                            password: SSH_PASS,
                            allowAnyHosts: true
                        ]
                    }
                }
            }
        }

        stage('Check Docker') {
            steps {
                script {
                    sshCommand remote: remote, command: 'docker ps'
                }
            }
        }
    }
}
