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

    parameters {
        choice(
            name: 'BRANCH',
            choices: ['main', 'develop', 'feature-ui'],
            description: 'Select the branch to build'
        )
        choice(
            name: 'DEPLOY_MODE',
            choices: ['staging', 'production'],
            description: 'Select deploy environment'
        )
        booleanParam(
            name: 'RUN_VALIDATION',
            defaultValue: true,
            description: 'Run site validation before deploy?'
        )
    }

    stages {
        stage('Init') {
            steps {
                echo "Starting ${env.APP_NAME} build"
                echo "Branch selected: ${params.BRANCH}"
                echo "Deploy mode: ${params.DEPLOY_MODE}"
                echo "Validation enabled? ${params.RUN_VALIDATION}"
            }
        }
    }
}
