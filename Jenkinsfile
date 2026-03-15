// Корневой блок декларативного пайплайна, внутри него описывается весь сценарий CI/CD
pipeline {
    // Директива agent описывает, на каком агенте Jenkins будет выполняться пайплайн
    //  any означает: можно запустить на любом доступном агенте
    agent any
    triggers {
        githubPush()
    }
    //  Блок options задаёт глобальные настройки поведения сборки
    options {
        //  Ограничивает максимальное время выполнения пайплайна до 1 часа, после чего сборка будет прервана
        timeout(time: 1, unit: 'HOURS')
        //  Запрещает одновременный запуск нескольких сборок этой job, новые запуски будут ждать окончания предыдущего
        disableConcurrentBuilds()
        //  Настраивает политику хранения логов и артефактов: оставить только последние 10 сборок
        buildDiscarder(logRotator(numToKeepStr: '10'))
        //  Включает вывод временных меток в логи, чтобы проще анализировать, когда что выполнялось
        timestamps()
        //  Отключает автоматическую проверку исходников из SCM в начале пайплайна
        //  Полезно, если вы сами явно вызываете checkout или в данном примере не используете репозиторий
        //skipDefaultCheckout()
    }

    //  Блок parameters объявляет параметры, которые пользователь может задавать перед запуском пайплайна
    parameters {
        //  Строковый параметр BRANCH_NAME — имя ветки Git, по умолчанию main
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Git branch name')
        //  Параметр-выбор ENVIRONMENT — окружение для деплоя (dev/test/prod)
        choice(name: 'ENVIRONMENT', choices: ['dev', 'test', 'prod'], description: 'Deployment environment')
        //  Логический параметр RUN_TESTS — запускать ли тесты (true/false)
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
    }

    //  Блок stages содержит список всех этапов пайплайна (Build, Testing, Deploy и т.д.)
    stages {
        //  Описание стадии Build — типичный этап сборки артефакта
        stage('Build') {
            //  Блок steps описывает конкретные шаги, которые нужно выполнить в этой стадии
            steps {
                //  Блок script позволяет использовать произвольный Groovy-код внутри декларативного пайплайна
                script {
                    //  Выводит в лог информацию, из какой ветки идёт «сборка»
                    //  params.BRANCH_NAME — значение параметра, выбранное пользователем
                    echo "Building project from branch ${params.BRANCH_NAME}..."
                    //  Выполнение shell-скрипта внутри агента Jenkins
                    //  Тройные кавычки ''' позволяют писать многострочный скрипт
                    sh '''
                        #  Создаём директорию target, если её нет
                        mkdir -p target
                        #  Создаём фейковый jar-файл как пример артефакта сборки
                        echo "fake jar content" > target/app.jar
                    '''
                }
            }
            //  Блок post внутри stage описывает действия после выполнения этой стадии
            post {
                //  Секция success выполняется только если стадия завершилась успешно
                success {
                    //  Сохраняет артефакты сборки (jar-файлы из target) в Jenkins с включением fingerprint
                    //  fingerprint позволяет отслеживать, какой артефакт использовался в каких сборках/деплоях
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    //  Пишет в лог сообщение об успешной сборке артефакта
                    echo "Artifact build completed successfully"
                }
                //  Секция failure выполняется, если стадия завершилась с ошибкой
                failure {
                    //  Пишет в лог сообщение о том, что сборка не удалась
                    echo "Build failed"
                }
            }
        }

        //  Стадия Testing — отвечает за запуск тестов и анализа качества кода
        stage('Testing') {
            //  Блок when задаёт условие, при котором стадия будет выполняться
            when {
                //  expression позволяет написать условие на Groovy
                //  В данном случае стадия Testing выполняется только если параметр RUN_TESTS == true
                expression { return params.RUN_TESTS }
            }
            //  Блок parallel позволяет выполнять несколько вложенных stage параллельно
            parallel {
                //  Вложенная стадия Unit Tests — имитация запуска модульных тестов
                stage('Unit Tests') {
                    steps {
                        //  Сообщение в лог о запуске модульных тестов
                        echo "Running unit tests"
                        //  Shell-скрипт, который создаёт структуру каталогов и пустой файл отчёта
                        sh '''
                        echo "Running unit tests"
                        #  Создаём каталог для отчётов surefire
                        mkdir -p target/surefire-reports
                        #  Создаём пустой файл отчёта тестов как заглушку
                        touch target/surefire-reports/test.xml
                        echo "Test results saved to target/surefire-reports/test.xml"
                        '''
                    }
                    //  post внутри вложенной стадии описывает действия после Unit Tests
                    post {
                        //  always означает, что блок выполнится независимо от результата стадии (успех/ошибка)
                        always {
                            //  Логируем, что модульные тесты завершены
                            echo "Unit tests completed - results saved locally"
                            //  Логируем путь к файлу с результатами тестов
                            echo "Test report: target/surefire-reports/test.xml"
                        }
                    }
                }
                //  Вложенная стадия Integration Tests — имитация запуска интеграционных тестов
                stage('Integration Tests') {
                    steps {
                        //  Сообщение в лог о запуске интеграционных тестов
                        echo "Running integration tests"
                        //  Shell-скрипт, который создаёт каталог и файл отчёта для интеграционных тестов
                        //test123
                        sh '''
                        echo "Running integration tests"
                        #  Создаём каталог для отчётов failsafe
                        mkdir -p target/failsafe-reports
                        #  Создаём пустой файл отчёта интеграционных тестов как заглушку
                        touch target/failsafe-reports/it.xml
                        echo "Integration test results saved to target/failsafe-reports/it.xml"
                        '''
                    }
                    //  post для интеграционных тестов
                    post {
                        //  always — выполнить блок всегда, независимо от результата стадии
                        always {
                            //  Логируем завершение интеграционных тестов
                            echo "Integration tests completed - results saved locally"
                            //  Логируем путь к файлу с результатами интеграционных тестов
                            echo "Test report: target/failsafe-reports/it.xml"
                        }
                    }
                }
                //  Вложенная стадия Code Quality — имитация анализа качества кода
                stage('Code Quality') {
                    steps {
                        //  Сообщение о запуске анализа качества кода
                        echo "Analyzing code quality"
                        //  Простой shell-скрипт-заглушка вместо реального инструмента анализа
                        sh 'echo "Code analysis done"'
                    }
                    //  post для анализа качества кода
                    post {
                        //  always — выполнить этот блок всегда
                        always {
                            //  Лог о завершении анализа качества кода
                            echo "Code quality analysis completed"
                        }
                    }
                }
            }
        }

        //  Стадия Deploy to Test — деплой в dev или test окружение
        stage('Deploy to Test') {
            //  Условие выполнения стадии деплоя в тест/дев окружение
            when {
                //  anyOf означает, что достаточно выполнения любого из вложенных условий
                anyOf {
                    //  Выполнять стадию, если параметр ENVIRONMENT == 'dev'
                    expression { return params.ENVIRONMENT == 'dev' }
                    //  Или если параметр ENVIRONMENT == 'test'
                    expression { return params.ENVIRONMENT == 'test' }
                }
            }
            steps {
                //  Логируем в какое окружение выполняется «деплой»
                echo "Deploying to ${params.ENVIRONMENT} environment..."
                //  Shell-команда-заглушка, имитирующая деплой на dev/test сервер
                sh 'echo "Deploying to dev/test server..."'
            }
        }

        //  Стадия Deploy to Prod — деплой в production
        stage('Deploy to Prod') {
            //  Условие: стадия выполняется только если выбранное окружение — prod
            when {
                expression { return params.ENVIRONMENT == 'prod' }
            }
            steps {
                //  Шаг input делает паузу пайплайна и требует ручного подтверждения
                //  Это типичный паттерн для деплоя в production: человек должен явно нажать «OK»
                input message: 'Confirm deployment to production', ok: 'Deploy'
                //  Логируем старт деплоя в production
                echo "Deploying to production..."
                //  Команда-заглушка для деплоя на prod сервер
                sh 'echo "Deploying to prod server..."'
            }
        }
    }

    //  Глобальный блок post для всего пайплайна — выполняется после завершения всех стадий
    post {
        //  Блок success выполняется, если весь пайплайн завершился успешно
        success {
            //  Печатает в лог сообщение об успешном завершении пайплайна
            echo '✅ Pipeline completed successfully!'
        }
        //  Блок failure выполняется, если любая стадия завершилась с ошибкой и пайплайн помечен как failed
        failure {
            //  Печатает в лог сообщение о неуспешном завершении пайплайна
            echo '❌ Pipeline failed.'
        }
        //  Блок always выполняется всегда, независимо от результата (успех/ошибка)
        always {
            //  Пишет в лог, что выполнение пайплайна завершено и все результаты сохранены локально
            echo "Pipeline execution completed - all results saved locally"
            //  Очищает рабочую директорию агента после завершения пайплайна (удаляет файлы сборки)
            deleteDir()
        }
    }
}
