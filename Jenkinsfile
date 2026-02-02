pipeline {
    agent any

    // Объявление параметров job
    parameters {
        string(
            name: 'RemoteHostVm',
            defaultValue: '192.168.31.120',
            description: 'IP или hostname удалённого сервера'
        )
    }

    // Объявление переменных окружения
    environment {
    }

    stages {
        stage('1. Init ssh-connection and check Docker') {
            steps {
                script {
                    // создаём глобальную переменную remoteConnection для использования в других stage
                    RemoteConnectionSsh = null
                    
                    withCredentials([
                        usernamePassword(
                            // Использование cred Jenkins для подключения к машине
                            credentialsId: 'ssh-elma1-pass-and-login',
                            usernameVariable: 'SshUser',
                            passwordVariable: 'SshPassword'
                        )
                    ]) {
                        // Инициализация remote и сохранение в environment
                        RemoteConnectionSsh = [
                            name: 'elma',
                            host: params.RemoteHostVm,
                            user: SshUser,
                            password: SshPassword,
                            allowAnyHosts: true
                        ]
                        
                        // Проверка подключения через команды
                        echo "----------Вывод подключения----------"
                        echo "Соединение успешно к ${params.RemoteHostVm} с пользователем ${SshUser}"
                        echo "----------Вывод docker контейнеров----------"
                        sshCommand remote: RemoteConnectionSsh, command: 'sudo docker ps'
                    }
                }
            }
        }

        stage('2. Check curl') {
            steps {
                script {
                    sshCommand remote: RemoteConnectionSsh, command: 'curl -I ya.ru'
                }
            }
        }
    }
}
