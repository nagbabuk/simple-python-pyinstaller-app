pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                bat '''
                    docker run --rm -v "%CD%":/app -w /app python:2-alpine \
                    python -m py_compile sources/add2vals.py sources/calc.py
                '''
                stash(name: 'compiled-results', includes: 'sources\\*.py*')
            }
        }

        stage('Test') {
            steps {
                bat '''
                    docker run --rm -v "%CD%":/app -w /app qnib/pytest \
                    py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py
                '''
            }
            post {
                always {
                    junit 'test-reports\\results.xml'
                }
            }
        }
    }
}
