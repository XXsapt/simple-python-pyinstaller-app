node {
    try {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash name: 'compiled-results', includes: 'sources/*.py*'
        }

        stage('Test') {
            try {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            } finally {
                junit 'test-reports/results.xml'
            }
        }

        stage('Deliver') {
            sh 'pyinstaller --onefile sources/add2vals.py'
            archiveArtifacts 'dist/add2vals'
        }
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        if (currentBuild.result == 'FAILURE' || currentBuild.result == 'UNSTABLE') {
            echo 'Pipeline failed or unstable.'
        } else {
            echo 'Pipeline completed successfully.'
        }
    }
}