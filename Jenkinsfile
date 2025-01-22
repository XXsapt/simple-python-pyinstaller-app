pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                    args '--entrypoint=""'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                    args '--entrypoint=""'
                }
            }
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
                junit 'test-reports/results.xml'
            }
        }

        stage('Manual Approval') {
            steps {
                input message: 'Lanjutkan ke tahap Deploy?'
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'python:3.8'
                    args '--entrypoint=""'
                }
            }
            steps {
                sh '''
                    pip install pyinstaller
                    python -m PyInstaller --onefile sources/add2vals.py
                '''
                archiveArtifacts artifacts: 'dist/add2vals', allowEmptyArchive: true
                sleep time: 60, unit: 'SECONDS'
            }
        }
    }
}