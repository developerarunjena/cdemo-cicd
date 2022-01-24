pipeline {
    agent any
    stages {
        stage("Build") {
            environment {
                DB_HOSTNAME = credentials("localhost")
                DB_DATABASE = credentials("crud")
                DB_USERNAME = credentials("user")
                DB_PASSWORD = credentials("password")
            }
            steps {
                sh 'php --version'
                sh 'composer install'
                sh 'composer --version'
                sh 'cp .env.example .env'
                sh 'echo DB_HOST=${DB_HOSTNAME} >> .env'
                sh 'echo DB_USERNAME=${DB_USERNAME} >> .env'
                sh 'echo DB_DATABASE=${DB_DATABASE} >> .env'
                sh 'echo DB_PASSWORD=${DB_PASSWORD} >> .env'
            }
        }
        stage("Unit test") {
            steps {
                echo "Unit test is skipping...."
            }
        }
        stage("Code coverage") {
            steps {
		echo "Code coverage...."
            }
        }
        stage("Static code analysis phpstan") {
            steps {
                sh "vendor/bin/phpstan analyse --memory-limit=2G"
            }
        }
        stage("Static code analysis phpcs") {
            steps {
                sh "vendor/bin/phpcs"
            }
        }
        stage("Docker build") {
            steps {
                sh "docker build -t devarunjena/codeigniter-demo ."
            }
        }
        stage("Docker push") {
            environment {
                DOCKER_USERNAME = credentials("devarunjena")
                DOCKER_PASSWORD = credentials("Biki@1997")
            }
            steps {
                sh "docker login --username ${DOCKER_USERNAME} --password ${DOCKER_PASSWORD}"
                sh "docker push devarunjena/codeigniter-demo"
            }
        }
        stage("Deploy to staging") {
            steps {
                sh "docker run -d --rm -p 80:80 --name codeigniter-demo devarunjena/codeigniter-demo"
            }
        }
        stage("Acceptance test curl") {
            steps {
                sleep 20
                sh "chmod +x acceptance_test.sh && ./acceptance_test.sh"
            }
        }
        stage("Acceptance test codeception") {
            steps {
                sh "vendor/bin/codecept run"
            }
            post {
                always {
                    sh "docker stop codeigniter-demo"
                }
            }
        }
    }
}
