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
                        // Правильное создание remote через remotes {}
                        remotes {
                            elmaServer {
                                host = params.RemoteHostVm
                                user = SshUser
                                password = SshPassword
                                allowAnyHosts = true
                            }
                        }

                        // Выполнение команды через sshCommand
                        sshCommand remote: elmaServer, command: 'docker ps'
                    }
                }
            }
        }
    }
}
