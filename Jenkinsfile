pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Detect Changes') {
            steps {
                script {
                    CHANGED_FILES = sh(
                        script: "git diff --name-only HEAD~1 HEAD",
                        returnStdout: true
                    ).trim()

                    echo "Changed files:"
                    echo CHANGED_FILES
                }
            }
        }

        stage('Python CI') {
            when {
                expression {
                    CHANGED_FILES.contains(".py")
                }
            }
            steps {
                echo "Python files changed → running Python pipeline"
                sh '''
                  python3 --version
                  pip3 install -r requirements.txt || true
                  pytest || true
                '''
            }
        }

        stage('C CI') {
            when {
                expression {
                    CHANGED_FILES.contains(".c")
                }
            }
            steps {
                echo "C files changed → running C pipeline"
                sh '''
                  gcc --version
                  gcc -o app src/*.c
                '''
            }
        }

        stage('No Relevant Changes') {
            when {
                expression {
                    !CHANGED_FILES.contains(".py") &&
                    !CHANGED_FILES.contains(".c")
                }
            }
            steps {
                echo "No relevant language changes detected. Skipping build."
            }
        }
    }
}
