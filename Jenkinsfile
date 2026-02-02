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

        stage('2. Create config file and download install script ELMA365') {
            steps {
                script {
                    // Создание файла config-elma365.txt
                    sshCommand remote: RemoteConnectionSsh, command: '''\
                    cat <<'EOF' > config-elma365.txt
ELMA365_HOST=elma1.work.local
ELMA365_EMAIL=admin@mail.com
ELMA365_PASSWORD=1224
ELMA365_LANGUAGE=ru-RU
ELMA365_EDITION=standard
ELMA365_PORT_FORWARD_PSQL=5432
ELMA365_PORT_FORWARD_MONGO=27017
ELMA365_PORT_FORWARD_AMQP=15672
ELMA365_PORT_FORWARD_REDIS=6379
ELMA365_PORT_FORWARD_S3=9000
ELMA365_DEBUG=true
ELMA365_ENABLED_FEATUREFLAGS="allowPortal","enableModuleServices","allowEditNotManagableExtensions","enableSearchInProcessMonitor","collector_enable_archivingItems"
'''
                    // Проверка сформированного файла
                    sshCommand remote: RemoteConnectionSsh, command: 'cat config-elma365.txt'
                }
            }
        }
    }
}
