node {
    stage('Build') {
        checkout scm
        withDockerContainer(image: 'python:2-alpine', args: '--entrypoint=""') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
}

node {
    stage('Test') {
        checkout scm
        withDockerContainer(image: 'qnib/pytest', args: '--entrypoint=""') {
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
}

node {
    stage('Manual Approval') {
        checkout scm
        input message: 'Lanjutkan ke tahap Deploy?'
    }
}

node {
    stage('Deploy') {
        checkout scm

        // Use withDockerContainer to specify the pyinstaller container
        // Ensure PyInstaller is available in the container or install it dynamically
        withDockerContainer(image: 'cdrx/pyinstaller-linux:python2', args: '--entrypoint=""') {
            // Install pyinstaller if not present
            sh '''
            pip install pyinstaller
            pyinstaller --onefile sources/add2vals.py
            '''
            archiveArtifacts artifacts: 'dist/add2vals', allowEmptyArchive: true
        }

        echo 'Sleep for 1 min'
        sleep time: 60, unit: 'SECONDS'
    }
}
