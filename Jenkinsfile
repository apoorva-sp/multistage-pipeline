pipeline {
    agent any

    environment {
        VENV_DIR = "${WORKSPACE}/.venv"
        PY = "${WORKSPACE}/.venv/bin/python"
        PIP = "${WORKSPACE}/.venv/bin/pip"
    }

    stages {
        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies in workspace...'
                sh '''
                set -e
                python3 -m venv "${VENV_DIR}" || true
                "${PY}" -m pip install --upgrade pip
                "${PIP}" install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests with venv python...'
                sh '''
                set -e
                "${PY}" -m unittest discover -s .
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                mkdir -p "${WORKSPACE}/python-app-deploy"
                cp "${WORKSPACE}/app.py" "${WORKSPACE}/python-app-deploy/"
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo 'Starting application with venv python...'
                sh '''
                nohup "${PY}" "${WORKSPACE}/python-app-deploy/app.py" > "${WORKSPACE}/python-app-deploy/app.log" 2>&1 &
                echo $! > "${WORKSPACE}/python-app-deploy/app.pid"
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Running integration tests with venv python...'
                sh '''
                set -e
                "${PY}" "${WORKSPACE}/test_app.py"
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
