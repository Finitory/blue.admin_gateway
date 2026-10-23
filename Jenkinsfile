pipeline {
    agent { label 'built-in' }

    options {
        timestamps()
        disableConcurrentBuilds()
        skipDefaultCheckout(true)
    }


    environment {
        GRADLE_USER_HOME = "${WORKSPACE}/.gradle-ci"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Environment') {
            steps {
                sh '''
                    set -eu

                    java -version
                    ./gradlew --version

                    test -f build.gradle
                    test -f settings.gradle
                '''
            }
        }

        stage('Test') {
            steps {
                sh './gradlew clean test --no-daemon'
            }
        }
    }

    post {
        always {
            junit(
                testResults: 'build/test-results/test/*.xml',
                allowEmptyResults: true
            )

            archiveArtifacts(
                artifacts: 'build/reports/tests/test/**',
                allowEmptyArchive: true
            )
        }

        success {
            echo 'Gateway CI succeeded'
        }

        failure {
            echo 'Gateway CI failed'
        }
    }
}