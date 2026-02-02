pipeline {
    agent any

    // Объявление параметров job
    parameters {
        string(
            name: 'RemoteHostVm',
            defaultValue: '192.168.31.120',
            description: 'IP или hostname удалённого сервера'
        )
        string(
            name: 'YearVersion',
            defaultValue: '2025',
            description: 'Год выпуска версии ELMA365'
        )
        string(
            name: 'MajorVersion',
            defaultValue: '10',
            description: 'Мажор выпуска версии ELMA365'
        )
        string(
            name: 'MinorVersion',
            defaultValue: '10',
            description: 'Минор выпуска версии ELMA365'
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
                        sshCommand remote: RemoteConnectionSsh, command: "sudo docker ps"
                    }
                }
            }
        }

        stage('2. Create config file and download install script ELMA365') {
            steps {
                script {
                    // Объявление переменной с названием директории
                    env.RemoteDirName = "install-job-elma365-${new Date().format('dd-MM-yyyy--HH-mm')}"
                
                    // Создание локальной директории для файлов
                    sshCommand remote: RemoteConnectionSsh, command: "mkdir -p ${env.RemoteDirName}"
                    
                    // Создание файла config-elma365.txt
                    sshCommand remote: RemoteConnectionSsh, command: """\
                    cat <<'EOF' > ${env.RemoteDirName}/config-elma365.txt
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
EOF
"""
                    echo "----------Загрузка файла скрипта установки----------"
                    sshCommand remote: RemoteConnectionSsh, command: "sudo curl -fsSL -o ${env.RemoteDirName}/elma365-docker.sh https://dl.elma365.com/onPremise/${params.YearVersion}/${params.MajorVersion}/${params.MinorVersion}/elma365-docker-${params.YearVersion}.${params.MajorVersion}.${params.MinorVersion}"
                    
                    echo "----------Добавление прав x для возможности запуска скрипта----------"
                    sshCommand remote: RemoteConnectionSsh, command: "sudo chmod +x ${env.RemoteDirName}/elma365-docker.sh"
                    
                    echo "----------Проверка временного каталога и созданных файлов----------"
                    sshCommand remote: RemoteConnectionSsh, command: "cat ${env.RemoteDirName}/config-elma365.txt"
                    sshCommand remote: RemoteConnectionSsh, command: "ls -li ${env.RemoteDirName}"
                }
            }
        }
        
        stage('3. Search and uninstall current ELMA365') {
            steps {
                script {
                    echo "----------Проверка наличия контейнера ELMA365----------"
                    def checkContainer = sshCommand(
                        remote: RemoteConnectionSsh,
                        returnStdout: true,
                        command: "docker ps --filter name=elma365 --format '{{.Names}}'"
                    ).trim()
                    
                    echo "----------Вывод docker контейнеров----------"
                    sshCommand remote: RemoteConnectionSsh, command: "sudo docker ps"
                    
                    if (checkContainer) {
                        echo "----------Контейнер elma365 найден: ${checkContainer}----------"
                        echo "----------Выполняем удаление----------"
                        sshCommand(
                            remote: RemoteConnectionSsh, command: "echo 'y' | sudo ${env.RemoteDirName}/elma365-docker.sh --delete"
                        )
                        echo "----------Удаление контейнера завершено----------"
                        echo "----------Вывод docker контейнеров----------"
                        sshCommand remote: RemoteConnectionSsh, command: "sudo docker ps"
                    } else {
                        echo "----------Контейнер elma365 не найден. Пропускаем удаление----------"
                    }
                }
            }
        }
        
    }
}
