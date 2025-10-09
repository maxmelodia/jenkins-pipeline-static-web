pipeline {
    agent any  // executa em qualquer nó Jenkins disponível

    environment {
        APP_NAME = 'jenkins-static-experience'
        BUILD_ENV = 'development'
        AUTHOR = 'Jenkins'
    }

    options {
        // mantém apenas os 5 últimos builds
        buildDiscarder(logRotator(numToKeepStr: '5'))

        // impede dois builds simultâneos da mesma branch
        disableConcurrentBuilds()

        // mostra timestamp em cada log
        timestamps()
    }

    stages {
        stage('Init') {
            steps {
                echo "Starting ${env.APP_NAME} build in ${env.BUILD_ENV} mode"
                echo "Author: ${env.AUTHOR}"
            }
        }
    }
}
