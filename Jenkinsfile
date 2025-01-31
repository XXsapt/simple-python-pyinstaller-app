pipeline {
    agent {
        docker {
            image 'python:3.11-slim'
            args '-p 3000:3000 --memory="512m" --cpus="0.5"'
        }
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install pytest
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    . venv/bin/activate
                    python3 -m py_compile sources/add2vals.py sources/calc.py
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    python3 -m pytest --junit-xml test-reports/results.xml sources/test_calc.py
                '''
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        id: 'userInput',
                        message: 'Lanjutkan ke tahap Deploy?',
                        parameters: [
                            choice(
                                choices: ['Proceed', 'Abort'],
                                description: 'Pilih "Proceed" untuk melanjutkan atau "Abort" untuk membatalkan.',
                                name: 'approval'
                            )
                        ]
                    )
                    if (userInput == 'Abort') {
                        error("Pipeline dibatalkan oleh pengguna.")
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                chmod +x deploy.sh
                ./deploy.sh
                sleep 60  // Jeda 1 menit
            }
        }
    }
}