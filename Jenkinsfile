pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        echo 'Hello Checkout'
        git(branch: 'main', url: 'https://github.com/Veverita-Engineering/Customer-Portal')
      }
    }

    stage('Build') {
      steps {
        echo 'Hello Build'
        sh 'node --version'
        sh 'npm --version'
        sh 'npm install'
        sh 'npm run build'
      }
    }

    stage('Parallel Stage') {
      failFast true
      parallel {
        stage('Test') {
          steps {
            echo 'Hello Test'
            sh 'npm test -- --reporters=jest-junit'
          }
        }

        stage('SCA') {
          steps {
            echo 'Hello SCA'
            snykSecurity(snykInstallation: 'snyk@latest', snykTokenId: 'snyk-api-token')
          }
        }

      }
    }

    stage('Deploy staging') {
      steps {
        echo 'Hello Deploy staging'
        echo "Deploying to production site ID: ${env.NETLIFY_SITE_ID}"
        sh 'node_modules/.bin/netlify deploy --dir=build'
      }
    }

    stage('Deploy prod') {
      steps {
        echo 'Hello Deploy prod'
        echo "Deploying site: ${NETLIFY_SITE_ID}"
        sh 'node_modules/.bin/netlify deploy --dir=build --prod'
      }
    }

  }
  tools {
    nodejs 'node-lts'
  }
  environment {
    NETLIFY_SITE_ID = "${env.NETLIFY_SITE_ID}"
    NETLIFY_AUTH_TOKEN = 'netlify-personal-access-token'
  }
  post {
    success {
      archiveArtifacts(artifacts: 'build/', fingerprint: true)
    }

    always {
      junit 'reports/junit.xml'
    }

  }
}