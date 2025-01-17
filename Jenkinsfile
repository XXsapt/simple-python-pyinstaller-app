node {
    try {
        stage('Build') {
            // Compile the Python files
            sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'

            // Stash the compiled results
            stash name: 'compiled-results', includes: 'sources/*.py*'
        }
        stage('Test') {
            // Activate the virtual environment and run tests
            sh '. venv/bin/activate && pytest --junit-xml=test-reports/results.xml sources/test_calc.py'
        }
    } catch (Exception e) {
        // Handle exceptions and mark the build as failed
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        // Always publish the JUnit report
        stage('Publish Test Results') {
            junit 'test-reports/results.xml'
        }
    }
}
