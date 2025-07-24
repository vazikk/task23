# task23

Поднял Jenkins на отдельном пользователе Jenkins: <br>
<img width="427" height="56" alt="image" src="https://github.com/user-attachments/assets/6e16f525-e701-462b-b061-fd71098af9ed" /> <br>

<img width="928" height="278" alt="image" src="https://github.com/user-attachments/assets/68e4f88a-b57a-4d0b-8920-95ecb3d558aa" /> <br>

______________________________________________________________________________________________________________________________________
Подключил Git, написал там Dockerfile и html код: <br>

<img width="1160" height="390" alt="image" src="https://github.com/user-attachments/assets/54117d6c-6797-4aae-b79a-0ee5a33bd31a" /> <br>
<img width="391" height="148" alt="image" src="https://github.com/user-attachments/assets/023cbf86-db58-4b92-af77-3b8d8140a481" /> <br>

```
FROM ubuntu:20.04

RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean

COPY ./html /var/www/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

```
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Простой Сайт</title>
    <style>
        body {
            text-align: center;
            margin-top: 50px;
        }
    </style>
</head>
<body>
    <h1>Добро пожаловать! Проходите уже ёмае!</h1>
    <p>Это простой сайт.</p>
</body>
</html>
```
_______________________________________________________________________________________________________________________

Подключил Jenkins-agent: <br>

<img width="1688" height="215" alt="image" src="https://github.com/user-attachments/assets/dcf48086-1475-46aa-abcd-4be002980a68" />

_______________________________________________________________________________________________________________________

Подключил Credentials: <br>

<img width="1351" height="281" alt="image" src="https://github.com/user-attachments/assets/3bee3739-e7b0-4723-92d6-9fbce34a3e27" /> <br>

________________________________________________________________________________________________________________________

Добавил WebHook: <br>

<img width="1061" height="142" alt="image" src="https://github.com/user-attachments/assets/94b7a67c-c3ca-4d0e-8afd-192ff2dee7d5" /> <br>

____________________________________________________________________________________________________________________________

Создал Personal Access Token: <br>

<img width="1317" height="415" alt="image" src="https://github.com/user-attachments/assets/07076fce-9fb4-467b-a0a3-43f6a65b0014" /> <br>

________________________________________________________________________________________________________________________________

Включил Trigger: <br>

<img width="1060" height="456" alt="image" src="https://github.com/user-attachments/assets/251f5677-ace0-47b6-8f5a-7ae0593800e8" /> <br>

_________________________________________________________________________________________________________________________________

Создал Pipeline

```
pipeline {
    agent { label 'agent' }

    environment {
        DOCKER_REGISTRY = 'vazik1910/taska'
        GIT_REPO = 'git@github.com:vazikk/fortask23.git'
        GIT_CREDENTIALS_ID = 'git-ssh'
        DOCKER_CREDENTIALS_ID = 'docker-credentials'
        DEPLOY_SERVER = '3.91.217.193' 
        DEPLOY_USER = 'ubuntu' 
        SSH_CREDENTIALS_ID = 'ssh-key' 
        DOCKER_HUB_PASSWORD = credentials('docker-hub-password') 
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: "${GIT_REPO}", credentialsId: "${GIT_CREDENTIALS_ID}"
            }
        }

        stage('Build') {
            steps {
                script {
                    def appImage = docker.build("${DOCKER_REGISTRY}:${env.BUILD_ID}", ".")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        docker.image("${DOCKER_REGISTRY}:${env.BUILD_ID}").push()
                    }
                }
            }
        }

        stage('Prepare Remote Instance') {
            steps {
                script {
                    sshagent(credentials: ["${SSH_CREDENTIALS_ID}"]) {
                        
                        sh """
                            ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_SERVER} '
                               
                                if ! command -v docker &> /dev/null; then
                                    echo "Устанавливаем Docker..."
                                    sudo apt-get update -qq
                                    sudo apt-get install -y -qq apt-transport-https ca-certificates curl software-properties-common
                                    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
                                    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu \$(lsb_release -cs) stable"
                                    sudo apt-get update -qq
                                    sudo apt-get install -y -qq docker-ce docker-ce-cli containerd.io
                                    sudo usermod -aG docker ${DEPLOY_USER}
                                fi

                               
                                docker --version
                            '
                        """
                    }
                }
            }
        }

        stage('Deploy to Instance') {
            steps {
                script {
                    sshagent(credentials: ["${SSH_CREDENTIALS_ID}"]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_SERVER} '
                                
                                echo "${DOCKER_HUB_PASSWORD}" | docker login -u ${DOCKER_REGISTRY.split('/')[0]} --password-stdin

                                
                                docker stop myapp || true
                                docker rm myapp || true

                                
                                docker rmi ${DOCKER_REGISTRY}:latest || true

                                
                                docker pull ${DOCKER_REGISTRY}:${env.BUILD_ID}

                                
                                docker run -d --name myapp -p 80:80 ${DOCKER_REGISTRY}:${env.BUILD_ID}

                                
                                docker ps | grep myapp
                            '
                        """
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    
                    sh "curl -I http://${DEPLOY_SERVER} || true"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo "Приложение успешно развернуто на ${DEPLOY_SERVER}"
        }
        failure {
            echo "Ошибка при развертывании на ${DEPLOY_SERVER}"
        }
    }
}
```

____________________________________________________________________________________________________________________________

Запустил Image и проверил работу: <br>

ДО КООМИТА <br>

<img width="1137" height="257" alt="image" src="https://github.com/user-attachments/assets/9be33ae3-1c8e-45ab-aa6c-39369f6b2f15" /> <br>


______________________________________________________________________________________________________________________________

ПОСЛЕ КОММИТА <br>
<img width="633" height="85" alt="image" src="https://github.com/user-attachments/assets/d009601d-a84c-4450-9839-fcf878003dfc" /> 

<img width="431" height="178" alt="image" src="https://github.com/user-attachments/assets/96a74c92-7550-4718-9728-236698375205" />

<img width="1372" height="266" alt="image" src="https://github.com/user-attachments/assets/740f7171-0869-4e06-ac88-1504d15dc85d" />












