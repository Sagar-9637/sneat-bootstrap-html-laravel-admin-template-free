pipeline {
    agent any
    environment {
        DEPLOY_PATH = "/var/www/html/sneat-bootstrap-html-laravel-admin-template-free"
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: 'main']], 
                    userRemoteConfigs: [[
                        url: 'https://github.com/Sagar-9637/sneat-bootstrap-html-laravel-admin-template-free.git',
                        credentialsId: 'github-credentials'
                    ]]
                ])
            }
        }
        stage('Fix Git Permissions') {
            steps {
                sh """
                sudo chown -R jenkins:jenkins $DEPLOY_PATH
                sudo chmod -R 775 $DEPLOY_PATH
                sudo chmod -R 775 $DEPLOY_PATH/.git
                """
            }
        }
        stage('Set Git Safe Directory') {
            steps {
                sh "git config --global --add safe.directory $DEPLOY_PATH"
            }
        }
        stage('Install Dependencies') {
            steps {
                sh """
                cd $DEPLOY_PATH
                git pull origin main
                composer install --no-dev --prefer-dist
                """
            }
        }
        stage('Run Migrations') {
            steps {
                sh """
                cd $DEPLOY_PATH
                php artisan migrate --force
                """
            }
        }
        stage('Optimize Application') {
            steps {
                sh """
                cd $DEPLOY_PATH
                php artisan config:cache
                php artisan route:cache
                php artisan storage:link
                """
            }
        }
        stage('Restart Server') {
            steps {
                sh 'sudo systemctl restart apache2'
            }
        }
    }
    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}

