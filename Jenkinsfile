pipeline {
    agent any

    environment {
        // 改成你自己电脑上的 python.exe 绝对路径（注意用双反斜杠）
        PYBIN = 'C:\\Python311\\python.exe'
    }

    stages {
        stage('Verify Python Path') {
            steps {
                bat """
                @echo off
                echo "=== 验证 Python 路径及版本 ==="
                ${PYBIN} --version
                echo "Python 路径验证通过！"
                """
            }
        }

        stage('Checkout') {
            steps {
                git url: 'https://github.com/331512q/myflaskapp.git', branch: 'main'
            }
        }

        stage('Fix pip') {
            steps {
                bat """
                @echo off
                echo "=== 修复 pip 环境 ==="
                ${PYBIN} -m ensurepip --upgrade
                ${PYBIN} -m pip --version
                """
            }
        }

        stage('Install Dependencies') {
            steps {
                bat """
                @echo off
                ${PYBIN} -m pip install --upgrade pip
                ${PYBIN} -m pip install -r requirements.txt
                """
            }
        }

        stage('Lint') {
            steps {
                bat "${PYBIN} -m pip install flake8 && ${PYBIN} -m flake8 app.py tests/"
            }
        }

        stage('Test') {
            steps {
                bat """
                ${PYBIN} -m pip install pytest
                ${PYBIN} -m pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }

        stage('Build') {
            steps {
                bat """
                ${PYBIN} -m pip install pyinstaller
                ${PYBIN} -m PyInstaller --onefile app.py
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                bat "start ${PYBIN} app.py"  // 启动 Flask 应用（按需调整）
            }
        }
    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully!'
        }
        failure {
            echo 'CI/CD pipeline failed!'
        }
    }
}
