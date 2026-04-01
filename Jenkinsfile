pipeline {
  agent any

  // Run the pipeline every 5 minutes
  triggers {
    cron('H/5 * * * *')
  }

  parameters {
    string(name: 'BRANCH', defaultValue: 'main', description: 'Git branch to build')
    booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
    choice(name: 'ENV', choices: ['dev', 'staging', 'prod'], description: 'Deploy environment')
  }

  stages {

    stage('Checkout Code') {
      steps {
        echo "Cloning branch: ${params.BRANCH}"
        // Actual Git checkout step pointing to your GitHub repository
        git branch: "${params.BRANCH}", url: 'https://github.com/JakeConal/demo_test_ci-main.git'
      }
    }

    stage('Build') {
      steps {
        echo "Building application..."
        sh 'echo "Build successful!"'
      }
    }

    stage('Test') {
      when {
        expression { params.RUN_TESTS }
      }
      parallel {
        stage('Unit Test') {
          steps {
            echo "Running unit tests..."
            script {
                if (isUnix()) {
                    sh 'python3 -m unittest test_main.py || python -m unittest test_main.py'
                } else {
                    bat 'python -m unittest test_main.py'
                }
            }
          }
        }
        stage('Integration Test') {
          steps {
            echo "Running integration tests..."
            sh 'echo "Integration tests passed!"'
          }
        }
      }
    }

    stage('Deploy') {
      steps {
        echo "Deploying to ${params.ENV} environment..."

        script {
          if (params.ENV == 'dev') {
            sh 'echo "Deploy to DEV server"'
          } else if (params.ENV == 'staging') {
            sh 'echo "Deploy to STAGING server"'
          } else {
            sh 'echo "Deploy to PRODUCTION server"'
          }
        }
      }
    }
  }

  post {
    success {
      echo "Pipeline completed successfully!"
    }
    failure {
      echo "Pipeline failed!"
    }
    always {
      echo "Cleaning workspace..."
      cleanWs()
    }
  }
}
