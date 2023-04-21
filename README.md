# Домашнее задание к занятию "`CI/CD`" - `Горбунов Владимир`

---

### Задание 1

Jenkins запущен на вм c Ubuntu в яндекс.клауд. 
Установлены golang, docker, jenkins добавлен в группу docker. 

jenkins go docker versions

![jenkins go docker versions](https://github.com/Night-N/ci-cd/blob/main/img/jenkins1.jpg)

Build success

![Build success](https://github.com/Night-N/ci-cd/blob/main/img/jenkins2.jpg)

Shell commands

![Shell commands](https://github.com/Night-N/ci-cd/blob/main/img/jenkins3.jpg)

Github source

![Github source](https://github.com/Night-N/ci-cd/blob/main/img/jenkins4.jpg)

---

### Задание 2

```
pipeline {
    agent any
    stages {
        stage('Git') {
            steps {
                git branch: 'main', url: 'https://github.com/Night-N/ci-cd-materials/'
            }
            }
        stage('Go test') {
            steps {
                sh "go test ."
            }
            }
        stage('Docker test') {
            steps {
                sh "docker build ."
            }
            }
        }
    }
```

![Pipeline success](https://github.com/Night-N/ci-cd/blob/main/img/pipeline.jpg)


---

### Задание 3 - 4

Версионирование сразу сделал.

На второй машине в яндекс клауде установлен репозиторий Nexus.

Загрузка скомпилированного go - с помощью плагина nexusArtifactUploader


Пайплайн:
```
pipeline {
    agent any
    environment {
    projectName = 'goapp-project'
    version = "1.0.${BUILD_NUMBER}"
    }
    stages {
        stage('Git') {
            steps {
                git branch: 'main', url: 'https://github.com/Night-N/ci-cd-materials/'
            }
            }
        stage('Go test') {
            steps {
                sh "go test ."
            }
            }
        stage('Go build') {
            steps {
                sh "go build -a -installsuffix nocgo -o goapp${version} ."
            }
            }
        stage('upload artifact') {
            steps {
                nexusArtifactUploader(
                nexusVersion: 'nexus3',
                protocol: 'http',
                nexusUrl: '192.168.10.32:8081',
                groupId: 'jenkins-server',
                version: version,
                repository: 'jenkins',
                credentialsId: 'nexus',
                artifacts: [
                        [artifactId: projectName,
                         classifier: '',
                         file: 'goapp' + version ,]
                        ]
                    )
                }

            }
        }
    }
```

креды для доступа к nexus указал в Global credentials 

![](https://github.com/Night-N/7-ci-cd/blob/main/img/artifactupload2.jpg)

сборка:

![](https://github.com/Night-N/7-ci-cd/blob/main/img/artifactupload1.jpg)

в репозитории:

![](https://github.com/Night-N/7-ci-cd/blob/main/img/artifactupload3.jpg)
