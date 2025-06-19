pipeline {
    agent none

    environment {
        // Use escaped backslashes or forward slashes on Windows
        VOLUME = "%CD%\\sources:/src"
        IMAGE = "cdrx/pyinstaller-linux:python2"
    }

    stages {
        stage('Build') {
            agent any
            steps {
                bat '''
                    docker run --rm -v "%CD%":/app -w /app python:2-alpine ^
                    python -m py_compile sources/add2vals.py sources/calc.py
                '''
                stash name: 'compiled-results', includes: 'sources\\*.py*'
            }
        }

        stage('Test') {
            agent any
            steps {
                bat '''
                    docker run --rm -v "%CD%":/app -w /app qnib/pytest ^
                    py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py
                '''
            }
            post {
                always {
                    junit 'test-reports\\results.xml'
                }
            }
        }

        stage('Deliver') {
            agent any
            steps {
                dir("${env.BUILD_ID}") {
                    unstash 'compiled-results'

                    bat """
                        docker run --rm -v "%CD%\\${env.BUILD_ID}\\sources:/src" ${env.IMAGE} ^
                        pyinstaller -F add2vals.py
                    """
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: "${env.BUILD_ID}\\sources\\dist\\add2vals.exe"
                    bat """
                        docker run --rm -v "%CD%\\${env.BUILD_ID}\\sources:/src" ${env.IMAGE} ^
                        rm -rf build dist
                    """
                }
            }
        }
    }
}
