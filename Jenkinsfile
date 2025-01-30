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

        // Tahap Manual Approval (Kriteria 4)
        stage('Manual Approval') {
            steps {
                script {
                    // Menampilkan pesan dan menunggu konfirmasi manual
                    def userInput = input(
                        id: 'userInput', 
                        message: 'Lanjutkan ke tahap Deploy?', 
                        parameters: [
                            choice(
                                choices: ['Proceed', 'Abort'],
                                description: 'Pilih "Proceed" untuk melanjutkan deploy atau "Abort" untuk membatalkan.',
                                name: 'DeployConfirmation'
                            )
                        ]
                    )

                    // Jika pengguna memilih "Abort", batalkan pipeline
                    if (userInput == 'Abort') {
                        error("Deploy dibatalkan oleh pengguna.")
                    }
                }
            }
        }

        // Tahap Deploy (Kriteria 2)
        stage('Deploy') {
            steps {
                sh '''#!/bin/bash
                    echo "Deploying application..."
                    . myenv/bin/activate
                    # Pastikan file deploy.sh ada dan memiliki izin eksekusi
                    if [ -f ./deploy.sh ]; then
                        chmod +x ./deploy.sh
                        ./deploy.sh
                    else
                        echo "Error: deploy.sh not found!"
                        exit 1
                    fi
                '''
            }
        }

        // Jeda 1 Menit Setelah Deploy (Kriteria 3)
        stage('Wait for 1 Minute') {
            steps {
                sh '''#!/bin/bash
                    echo "Menunggu 1 menit sebelum mengakhiri pipeline..."
                    sleep 60
                    echo "Pipeline akan segera berakhir."
                '''
            }
        }
    }
}