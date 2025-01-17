node {
    stage('Check Python') {
        sh 'python3 --version'  // Gunakan python3 jika sudah terinstal
    }
    stage('Build') {
        sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
        stash(name: 'compiled-results', includes: 'sources/*.py*')
    }
}
