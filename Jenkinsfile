pipeline {
    agent any

    parameters {
        string(
            name: 'RemoteHostVm',
            defaultValue: '192.168.31.120',
            description: 'IP или hostname удалённого сервера'
        )
    }

    stages {
        stage('Check Docker') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'ssh-elma1-pass-and-login',
                            usernameVariable: 'SshUser',
                            passwordVariable: 'SshPassword'
                        )
                    ]) {
                        // Объявляем remote через def внутри script
                        def remote = [
                            name: 'elma',
                            host: params.RemoteHostVm,
                            user: SshUser,
                            password: SshPassword,
                            allowAnyHosts: true
                        ]

                        sshCommand remote: remote, command: 'docker ps'
                    }
                }
            }
        }
    }
}
