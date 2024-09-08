Задание 1
Что нужно сделать:

Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно.
Установите на машину с jenkins golang.
Используя свой аккаунт на GitHub, сделайте себе форк репозитория. В этом же репозитории находится дополнительный материал для выполнения ДЗ.
Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта go test . и docker build ..
В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

Решение 1


![img](https://github.com/Furious992/hw8-02/blob/main/screen1.png)
![img](https://github.com/Furious992/hw8-02/blob/main/screen2.png)
![img](https://github.com/Furious992/hw8-02/blob/main/screen3.png)
![img](https://github.com/Furious992/hw8-02/blob/main/screen4.png)


Задание 2
Что нужно сделать:

Создайте новый проект pipeline.
Перепишите сборку из задания 1 на declarative в виде кода.
В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

Решение 2

Пайплайн:

```groovy
pipeline {
    agent any
    stages {
        stage('Git clone') {
            steps {
                git 'https://github.com/netology-code/sdvps-materials.git'
            }
        }
        stage('Run tests') {
            steps {
                sh '/usr/local/go/bin/go test .'
            }
        }
        stage('Build go app in docker image') {
            steps {
                sh 'docker build . -t cicd-host:8082/hello-world:decl_v$BUILD_NUMBER'
            }
        }
    }
}
```


![img](https://github.com/Furious992/hw8-02/blob/main/screen5.png)
![img](https://github.com/Furious992/hw8-02/blob/main/screen8.png)



Задание 3
Что нужно сделать:

Установите на машину Nexus.
Создайте raw-hosted репозиторий.
Измените pipeline так, чтобы вместо Docker-образа собирался бинарный go-файл. Команду можно скопировать из Dockerfile.
Загрузите файл в репозиторий с помощью jenkins.
В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.


Решение 3

![img](https://github.com/Furious992/hw8-02/blob/main/screen6.png)

Пайплайн:

```groovy
pipeline {
    agent any
    stages {
        stage('Git clone') {
            steps {
                git 'https://github.com/netology-code/sdvps-materials.git'
            }
        }
        stage('Run tests') {
            steps {
                sh '/usr/local/go/bin/go test .'
            }
        }
        stage('Build go app and push to Nexus repo') {
            steps {
                sh 'CGO_ENABLED=0 GOOS=linux /usr/local/go/bin/go build -a -installsuffix nocgo -o ./go-app .'
                sh 'curl -u admin:admin http://cicd-host:8081/repository/raw-hosted-repo/ --upload-file ./go-app -v'
            }
        }
    }
}
```

![img](https://github.com/Furious992/hw8-02/blob/main/screen7.png)





