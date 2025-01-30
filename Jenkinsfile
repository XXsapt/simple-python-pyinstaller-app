pipeline {
    agent any

    stages {
        // 1️⃣ Instal dependensi sebelum membuat virtual environment
        stage('Install Dependencies') {
            steps {
                sh '''#!/bin/bash
                    echo "Installing dependencies..."
                    apt-get update && apt-get install -y python3 python3-venv
                '''
            }
        }

        // 2️⃣ Setup Virtual Environment
        stage('Setup Virtual Environment') {
            steps {
                sh '''#!/bin/bash
                    echo "Setting up virtual environment..."
                    python3 -m venv myenv || { echo "Failed to create virtual environment"; exit 1; }
                    source myenv/bin/activate || . myenv/bin/activate
                    python3 -m ensurepip --default-pip
                    pip install --upgrade pip
                    pip install pytest
                '''
            }
        }

        // 3️⃣ Build Aplikasi
        stage('Build') {
            steps {
                sh '''#!/bin/bash
                    echo "Building application..."
                    source myenv/bin/activate || . myenv/bin/activate
                    python3 -m py_compile sources/add2vals.py sources/calc.py
                '''
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }

        // 4️⃣ Test Aplikasi
        stage('Test') {
            steps {
                sh '''#!/bin/bash
                    echo "Running tests..."
                    source myenv/bin/activate || . myenv/bin/activate
                    python3 -m pytest --junit-xml test-reports/results.xml sources/test_calc.py
                '''
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        // 5️⃣ Manual Approval sebelum Deploy
        stage('Manual Approval') {
            steps {
                script {
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

                    if (userInput == 'Abort') {
                        error("Deploy dibatalkan oleh pengguna.")
                    }
                }
            }
        }

        // 6️⃣ Deploy Aplikasi
        stage('Deploy') {
            steps {
                sh '''#!/bin/bash
                    echo "Deploying application..."
                    source myenv/bin/activate || . myenv/bin/activate
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

        // 7️⃣ Jeda 1 Menit setelah Deploy
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
