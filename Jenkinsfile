pipeline {
    agent any

    stages {
        stage('Setup Virtual Environment') {
            steps {
                sh '''
                    python3 -m venv myenv
                    source myenv/bin/activate
                    pip install pytest
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    source myenv/bin/activate
                    python3 -m py_compile sources/add2vals.py sources/calc.py
                '''
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }

        stage('Test') {
            steps {
                sh '''
                    source myenv/bin/activate
                    python3 -m pytest --junit-xml test-reports/results.xml sources/test_calc.py
                '''
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
    }
}