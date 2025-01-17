pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Install Dependencies') { // Tambahkan tahap ini
            steps {
                sh 'pip3 install pytest'
            }
        }
        stage('Test') {
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
        }
    }
    post {
        always {
            junit 'test-reports/results.xml'
        }
    }
}
