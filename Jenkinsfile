node {
    def app
    
    stage('Clone') {
        checkout scm
    }
    
    stage('Build') {
        withDockerContainer(image: 'python:2-alpine', args: '--entrypoint=""') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    
    stage('Test') {
        withDockerContainer(image: 'qnib/pytest', args: '--entrypoint=""') {
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
    
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    
    stage('Deploy') {
        withDockerContainer(image: 'python:3.8-slim', args: '--entrypoint=""') {
            sh '''
                pip install pyinstaller
                python -m PyInstaller --onefile sources/add2vals.py
            '''
            archiveArtifacts artifacts: 'dist/add2vals', allowEmptyArchive: true
        }
        echo 'Sleep for 1 min'
        sleep time: 60, unit: 'SECONDS'
    }
}