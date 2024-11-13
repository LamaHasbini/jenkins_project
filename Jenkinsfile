pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv' 
        }
    stages {
        stage('Setup') {
            steps {
                script {
                    def pythonCmd = "python3"
                    // Check if python3 is available, else fallback to python
                    if (sh(script: "which python3", returnStatus: true) != 0) {
                        pythonCmd = "python"
                    }
                    if (!fileExists("${env.WORKSPACE}/${VIRTUAL_ENV}")) {
                        sh "python3 -m venv ${VIRTUAL_ENV}"
                    }
                    sh "source ${VIRTUAL_ENV}/bin/activate && pip install -r requirements.txt"
                }
            }
        }
        stage('Lint') {
            steps {
                script {
                    sh "source ${VIRTUAL_ENV}/bin/activate && flake8 app.py"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh "source ${VIRTUAL_ENV}/bin/activate && pytest"
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application..."
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}