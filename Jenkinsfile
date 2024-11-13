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
                    if (sh(script: "which python3", returnStatus: true) != 0) {
                        pythonCmd = "python"
                    }
                    if (!fileExists("${env.WORKSPACE}/${VIRTUAL_ENV}")) {
                        sh "${pythonCmd} -m venv ${VIRTUAL_ENV}"
                    }
                }
                sh """
                source \${VIRTUAL_ENV}/bin/activate
                pip install -r requirements.txt
                """
            }
        }
        stage('Lint') {
            steps {
                script {
                    sh "source \${VIRTUAL_ENV}/bin/activate && flake8 app.py"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh "source \${VIRTUAL_ENV}/bin/activate && export PYTHONPATH=$(pwd) && pytest"
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
