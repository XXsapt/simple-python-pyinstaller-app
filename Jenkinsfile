node {
    stage('Check Python') {
        sh 'python3 --version'  // Gunakan python3 jika Python 3 terinstal
    }
    stage('Build') {
        sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'  // Gunakan python3 di sini juga
        stash(name: 'compiled-results', includes: 'sources/*.py*')
    }
}
