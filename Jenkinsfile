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

    triggers {
        pollSCM('H/30 * * * *')
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

        stage('Build') {
            steps {
                echo "Building static site..."
                sh '''
                    mkdir -p build
                    cp -r site/* build/
                    echo "Build folder created successfully!"
                '''
            }
        } 

        stage('Parallel Validation') {
            when {
                equals expected: true, actual: params.RUN_VALIDATION
            }
            parallel {
                stage('HTML Check') {
                    steps {
                        sh '''
                            echo "🔍 Checking HTML structure..."
                            sleep 1
                            if [ ! -f build/index.html ]; then
                                echo "Missing index.html"
                                exit 1
                            fi
                            echo "HTML check passed!"
                        '''
                    }
                }
                stage('CSS Check') {
                    steps {
                        sh '''
                            echo "Checking CSS files..."
                            sleep 2
                            if [ ! -f build/style.css ]; then
                                echo "Missing style.css (non-blocking warning)"
                            fi
                            echo "CSS check completed!"
                        '''
                    }
                }
                stage('JS Check') {
                    steps {
                        sh '''
                            echo "Checking JS files..."
                            sleep 3
                            if [ ! -f build/script.js ]; then
                                echo "Missing script.js (optional)"
                            fi
                            echo "JS check finished!"
                        '''
                    }
                }
            }
        }

        stage('Approval') {
            steps {
                script {
                    def userInput = input(
                        id: 'DeployApproval',
                        message: 'Do you want to deploy this build?',
                        parameters: [
                            choice(name: 'CONFIRM_DEPLOY', choices: ['No', 'Yes'], description: 'Approve deployment?')
                        ]
                    )
                    echo "User selected: ${userInput}"

                    if (userInput == 'No') {
                        error('Deployment was cancelled by the user.')
                    }
                }
            }
        }

        stage('Deploy') {
            when {
                allOf {
                    equals expected: 'production', actual: params.DEPLOY_MODE
                }
            }
            steps {
                echo "Starting deployment to ${params.DEPLOY_MODE} environment..."

                withCredentials([string(credentialsId: 'fake-deploy-token', variable: 'DEPLOY_KEY')]) {
                    sh '''
                        echo "Uploading build folder to simulated server..."
                        echo "Using deploy key: $DEPLOY_KEY"
                        sleep 2
                        echo "Deployment to ${DEPLOY_MODE} completed successfully!"
                    '''
                }
            }
        }

        
    }

    post {
        success {
            echo "${env.APP_NAME} built and validated successfully!"
        }
        failure {
            echo "Build failed. Check logs for details."
        }
    }    

}
