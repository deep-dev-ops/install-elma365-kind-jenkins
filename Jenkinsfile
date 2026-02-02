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
        RemoteConnectionSsh = ''
    }

    stages {
        stage('1. Init ssh-connection and check Docker') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            // Использование cred Jenkins для подключения к машине
                            credentialsId: 'ssh-elma1-pass-and-login',
                            usernameVariable: 'SshUser',
                            passwordVariable: 'SshPassword'
                        )
                    ]) {
                        // Инициализация remote и сохранение в environment
                        env.RemoteConnectionSsh = [
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
                        sshCommand remote: env.RemoteConnectionSsh, command: 'sudo docker ps'
                    }
                }
            }
        }

        stage('2. Check curl') {
            steps {
                script {
                    sshCommand remote: env.RemoteConnectionSsh, command: 'curl -I ya.ru'
                }
            }
        }
    }
}
