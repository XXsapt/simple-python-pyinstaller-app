pipeline {
    agent any

    stages {
        // Tahap Setup Virtual Environment
        stage('Setup Virtual Environment') {
            steps {
                sh '''#!/bin/bash
                    echo "Setting up virtual environment..."
                    python3 -m venv myenv
                    . myenv/bin/activate
                    pip install pytest
                '''
            }
        }

        // Tahap Build
        stage('Build') {
            steps {
                sh '''#!/bin/bash
                    echo "Building application..."
                    . myenv/bin/activate
                    python3 -m py_compile sources/add2vals.py sources/calc.py
                '''
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }

        // Tahap Test
        stage('Test') {
            steps {
                sh '''#!/bin/bash
                    echo "Running tests..."
                    . myenv/bin/activate
                    python3 -m pytest --junit-xml test-reports/results.xml sources/test_calc.py
                '''
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        // Tahap Konfirmasi Manual Sebelum Deploy
        stage('Confirm Deploy') {
            steps {
                script {
                    // Menampilkan pesan dan menunggu konfirmasi manual
                    def userInput = input(
                        id: 'userInput', 
                        message: 'Lanjutkan ke tahap deploy?', 
                        parameters: [
                            choice(
                                choices: ['Ya', 'Tidak'],
                                description: 'Pilih "Ya" untuk melanjutkan deploy atau "Tidak" untuk membatalkan.',
                                name: 'DeployConfirmation'
                            )
                        ]
                    )

                    // Jika pengguna memilih "Tidak", batalkan pipeline
                    if (userInput == 'Tidak') {
                        error("Deploy dibatalkan oleh pengguna.")
                    }
                }
            }
        }

        // Tahap Deploy
        stage('Deploy') {
            steps {
                sh '''#!/bin/bash
                    echo "Deploying application..."
                    . myenv/bin/activate
                    # Tambahkan perintah deploy Anda di sini
                    # Contoh: Menjalankan skrip deploy.sh
                    ./deploy.sh
                '''
            }
        }
    }
}