// ---------- Объявляем remote глобально ----------
def remote  

pipeline {
    agent any

    // ---------- Глобальные параметры ----------
    parameters {
        string(
            name: 'RemoteHostVm', 
            defaultValue: '192.168.31.120', 
            description: 'IP или hostname удалённого сервера, куда будет устанавливаться ELMA365 KinD'
        )
    }

    stages {
        stage('1. Init SSH') {
            steps {
                script {
                    // Подставляем логин/пароль через Credentials
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'ssh-elma1-pass-and-login',
                            usernameVariable: 'SshUser',
                            passwordVariable: 'SshPassword'
                        )
                    ]) {
                        // Инициализируем глобальный remote
                        remote = [
                            name: 'elma',
                            host: params.RemoteHostVm,
                            user: SshUser,
                            password: SshPassword,
                            allowAnyHosts: true
                        ]
                    }
                }
            }
        }

        stage('Check Docker') {
            steps {
                script {
                    // Используем ранее созданный remote
                    sshCommand remote: remote, command: 'docker ps'
                }
            }
        }
    }
}
