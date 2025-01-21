node {
    stage('Build') {
        checkout scm
        // Gunakan Python 3 container
        withDockerContainer(image: 'python:3-alpine', args: '--entrypoint=""') {
            // Compile file Python
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
}

node {
    stage('Test') {
        checkout scm
        // Gunakan pytest container dengan Python 3
        withDockerContainer(image: 'python:3-alpine', args: '--entrypoint=""') {
            // Install pytest terlebih dahulu karena belum tersedia secara default
            sh '''
            pip install pytest
            py.test --junit-xml test-reports/results.xml sources/test_calc.py
            '''
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
        // Gunakan PyInstaller di Python 3
        withDockerContainer(image: 'python:3-alpine', args: '--entrypoint=""') {
            // Install PyInstaller dan buat binary
            sh '''
            pip install --user pytest
            export PATH=$PATH:~/.local/bin
            py.test --junit-xml test-reports/results.xml sources/test_calc.py
            '''
            // Arsipkan binary hasil build
            archiveArtifacts artifacts: 'dist/add2vals', allowEmptyArchive: true
        }
        echo 'Sleep for 1 min'
        sleep time: 60, unit: 'SECONDS'
    }
}
