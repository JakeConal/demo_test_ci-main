pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    if (isUnix()) {
                        sh '''
                            python3 --version || python --version
                            python3 -m pip install --upgrade pip || python -m pip install --upgrade pip
                            python3 -m pip install ruff pytest coverage || python -m pip install ruff pytest coverage
                            if [ -f requirements.txt ]; then
                                python3 -m pip install -r requirements.txt || python -m pip install -r requirements.txt
                            fi
                        '''
                    } else {
                        bat '''
                            python --version
                            python -m pip install --upgrade pip
                            python -m pip install ruff pytest coverage
                            if exist requirements.txt python -m pip install -r requirements.txt
                        '''
                    }
                }
            }
        }

        stage('Lint') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'python3 -m ruff check . || python -m ruff check .'
                    } else {
                        bat 'python -m ruff check .'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'python3 -m coverage run -m pytest -v -s --junitxml=pytest-results.xml || python -m coverage run -m pytest -v -s --junitxml=pytest-results.xml'
                    } else {
                        bat 'python -m coverage run -m pytest -v -s --junitxml=pytest-results.xml'
                    }
                }
            }
        }

        stage('Coverage Report') {
            steps {
                script {
                    if (isUnix()) {
                        sh '''
                            python3 -m coverage report -m || python -m coverage report -m
                            python3 -m coverage xml || python -m coverage xml
                        '''
                    } else {
                        bat '''
                            python -m coverage report -m
                            python -m coverage xml
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'pytest-results.xml'
            archiveArtifacts allowEmptyArchive: true, artifacts: 'coverage.xml,.coverage'
        }
    }
}
