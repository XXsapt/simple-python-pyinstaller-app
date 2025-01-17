node {
    stage('Build') {
        // Menjalankan kompilasi Python menggunakan perintah python3
        sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        
        // Stash hasil kompilasi
        stash(name: 'compiled-results', includes: 'sources/*.py*')
    }
}