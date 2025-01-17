pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Install Dependencies') {
            steps {
                // Buat virtual environment
                sh 'python3 -m venv venv'
                // Aktifkan virtual environment dan instal pytest
                sh 'source venv/bin/activate && pip install pytest'
            }
        }
        stage('Test') {
            steps {
                sh 'source venv/bin/activate && pytest --junit-xml test-reports/results.xml sources/test_calc.py'
            }
        }
    }
    post {
        always {
            junit 'test-reports/results.xml'
        }
    }
}
