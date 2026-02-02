pipeline {
    agent any

    // ---------- Глобальные параметры----------
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
                    // Подключаем credentials
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'ssh-elma1-pass-and-login',
                            usernameVariable: 'SshUser',
                            passwordVariable: 'SshPassword'
                        )
                    ]) {
                        // Объявляем remote внутри script через def
                        def remote = [
                            name: 'elma',
                            host: params.RemoteHostVm,
                            user: SshUser,
                            password: SshPassword,
                            allowAnyHosts: true
                        ]

                        echo "Connecting to ${remote.host} as ${remote.user}"

                        // Выполняем команду на удалённом сервере
                        sshCommand remote: remote, command: 'docker ps'
                    }
                }
            }
        }
    }
}
