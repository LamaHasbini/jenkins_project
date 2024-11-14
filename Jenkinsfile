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
                        source ${VIRTUAL_ENV}/bin/activate
                        coverage run -m pytest
                        coverage report -m
                        coverage html -d coverage_html_report
                    """
                }
            }
        }

        stage('Security Scan') {
            steps {
                script {
                    withEnv(["PYTHONIOENCODING=utf-8"]) {
                        sh """
                            source ${VIRTUAL_ENV}/bin/activate
                            bandit -r . -f txt -o bandit_report.txt || exit 0
                        """
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application..."
                    sh """
                        source ${VIRTUAL_ENV}/bin/activate
                        python app.py
                    """
                }
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'coverage_html_report/**', allowEmptyArchive: true            
            cleanWs()
        }
    }
}
