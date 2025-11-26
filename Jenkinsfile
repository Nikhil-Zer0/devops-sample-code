pipeline {
    agent any

    stages {
        stage('Build / Setup venv') {
            steps {
                echo 'Creating virtualenv and installing dependencies...'
                sh '''
                    # Create virtual environment in ./venv
                    python3 -m venv venv

                    # Activate venv
                    . venv/bin/activate

                    # Upgrade pip inside venv
                    pip install --upgrade pip

                    # Install project dependencies
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh '''
                    . venv/bin/activate
                    python -m unittest discover -s .
                '''
            }
        }

        stage('Code Quality') {
            steps {
                echo 'Running code quality checks...'
                sh '''
                    . venv/bin/activate
                    flake8 .
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
                echo 'Running application...'
                sh '''
                    cd "${WORKSPACE}/python-app-deploy"
                    # activate venv from parent folder
                    . ../venv/bin/activate

                    nohup python app.py > app.log 2>&1 &
                    echo $! > app.pid
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing running application...'
                sh '''
                    . venv/bin/activate
                    python test_app.py
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
