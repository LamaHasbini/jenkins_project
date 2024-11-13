// Lama Hasbini - Lab 10 - Jenkinsfile
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
                pip install coverage bandit
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
                    sh """
                        source \${VIRTUAL_ENV}/bin/activate
                        export PYTHONPATH=\$PWD
                        pytest
                    """
                }
            }
        }

        stage('Coverage') {
            steps {
                script {
                    sh """
                        source \${VIRTUAL_ENV}/bin/activate
                        coverage run -m pytest
                        coverage report
                        coverage html -d coverage_html_report 
                    """
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'coverage_html_report/**', allowEmptyArchive: true
                }
            }
        }

        stage('Security Scan') {
            steps {
                script {
                    sh """
                        source \${VIRTUAL_ENV}/bin/activate
                        bandit -r . -o bandit_report.txt -f txt
                    """
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'bandit_report.txt', allowEmptyArchive: true
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application with Docker..."
                    sh """
                        source \${VIRTUAL_ENV}/bin/activate
                        docker build -t myapp:latest .
                        docker run -d -p 8080:8080 myapp:latest
                        echo "Application deployed successfully with Docker."
                    """
                }
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'htmlcov/**', allowEmptyArchive: true
            cleanWs()
        }
    }
}
