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
        stage('1. Init ssh-connection and check Docker') {
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
                        def remote = [
                            name: 'elma',
                            host: params.RemoteHostVm,
                            user: SshUser,
                            password: SshPassword,
                            allowAnyHosts: true
                        ]
                        echo "----------Вывод docker контейнеров----------"
                        echo "Соединение успешно к ${remote.host} с пользователем ${remote.user}"
                        echo "----------Вывод docker контейнеров----------"
                        sshCommand remote: remote, command: 'sudo docker ps'
                    }
                }
            }
        }
    }
}
