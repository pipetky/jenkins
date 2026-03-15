pipeline {
    agent any
    
    parameters {
        string(name: 'APP_VERSION', defaultValue: 'latest', description: 'Version of the application')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
        choice(name: 'DEPLOY_ENV', choices: ['dev', 'staging', 'prod'], description: 'Deployment environment')
        password(name: 'MY_SECRET_TOKEN', defaultValue: 'SECRET', description: 'Secret token from credentials store')
    }
    
    environment {
        // Устанавливаем переменные окружения на основе параметров
        APP_VERSION = "${params.APP_VERSION}"
        RUN_TESTS = "${params.RUN_TESTS}"
        DEPLOY_ENV = "${params.DEPLOY_ENV}"
        // Для секрета используем credentials
        SECRET_TOKEN = credentials('495341a1-8fec-4e2c-b222-7a9ed52353b6') // ID вашего credentials в Jenkins
    }
    
    stages {
        stage('Setup Environment') {
            steps {
                script {
                    echo "========================================"
                    echo "Setting up environment..."
                    echo "========================================"
                    echo "Application Version: ${env.APP_VERSION}"
                    echo "Run Tests: ${env.RUN_TESTS}"
                    echo "Deployment Environment: ${env.DEPLOY_ENV}"
                    echo "Secret token is configured and available"
                    
                    // Выводим информацию о том, что переменные окружения установлены
                    echo "\nEnvironment variables set:"
                    echo "  APP_VERSION=${env.APP_VERSION}"
                    echo "  RUN_TESTS=${env.RUN_TESTS}"
                    echo "  DEPLOY_ENV=${env.DEPLOY_ENV}"
                    echo "  SECRET_TOKEN is available (value hidden for security)"
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    echo "========================================"
                    echo "Building application version: ${env.APP_VERSION}"
                    echo "========================================"
                    
                    // Симуляция процесса сборки
                    sh """
                        echo "Building application version ${APP_VERSION}..."
                        echo "Build completed for version ${APP_VERSION}"
                        mkdir -p build
                        echo "Application ${APP_VERSION}" > build/app.txt
                    """
                    
                    echo "Build artifacts created successfully"
                }
            }
        }
        
        stage('Test') {
            when {
                expression { params.RUN_TESTS == true }
            }
            steps {
                script {
                    echo "========================================"
                    echo "Running tests for environment: ${env.DEPLOY_ENV}"
                    echo "========================================"
                    
                    // Симуляция тестов для разных окружений
                    sh """
                        echo "Running tests for ${DEPLOY_ENV} environment..."
                        echo "Testing application version ${APP_VERSION}"
                        
                        # Симуляция разных типов тестов
                        echo "Running unit tests..."
                        sleep 2
                        echo "✓ Unit tests passed"
                        
                        echo "Running integration tests..."
                        sleep 2
                        echo "✓ Integration tests passed"
                        
                        echo "Running ${DEPLOY_ENV}-specific tests..."
                        sleep 1
                        echo "✓ All tests completed successfully"
                    """
                    
                    echo "All tests passed for ${env.DEPLOY_ENV} environment!"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    echo "========================================"
                    echo "Deploying to ${env.DEPLOY_ENV} environment"
                    echo "========================================"
                    
                    // Демонстрация использования секретного токена
                    echo "Using secret token for authentication to ${env.DEPLOY_ENV} server..."
                    
                    // В реальном сценарии здесь был бы curl или другой запрос с токеном
                    // Но мы просто показываем, что токен доступен
                    sh """
                        echo "Deploying application version ${APP_VERSION} to ${DEPLOY_ENV}..."

                        
                        if [ "${DEPLOY_ENV}" = "prod" ]; then
                            echo "⚠️  PRODUCTION DEPLOYMENT - EXTRA CAUTION REQUIRED"
                            sleep 3
                        else
                            echo "Deploying to ${DEPLOY_ENV} environment..."
                            sleep 2
                        fi
                        
                        echo "✅ Application ${APP_VERSION} successfully deployed to ${DEPLOY_ENV}"
                    """
                    echo "Secret token length: ${SECRET_TOKEN.length()} characters"
                    echo "Deployment to ${env.DEPLOY_ENV} completed!"
                    
                    // Показываем, что токен был использован (без раскрытия значения)
                    echo "Secret token was used for authentication to ${env.DEPLOY_ENV}"
                }
            }
        }
    }
    
    post {
        success {
            echo "========================================"
            echo "✅ Pipeline completed successfully!"
            echo "Application: ${env.APP_VERSION}"
            echo "Environment: ${env.DEPLOY_ENV}"
            echo "Tests run: ${params.RUN_TESTS}"
            echo "========================================"
        }
        failure {
            echo "========================================"
            echo "❌ Pipeline failed!"
            echo "========================================"
        }
        always {
            echo "Pipeline execution finished. Cleaning up..."
            deleteDir()
        }
    }
}