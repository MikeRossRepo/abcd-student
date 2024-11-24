pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }
    
    stages {

        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'github', url: 'https://github.com/MikeRossRepo/abcd-student', branch: 'main'
                }
            }
        }
        
        stage('[Semgrep] SAST Scan') {
            steps {
                sh 'semgrep scan --config auto --json-output=${WORKSPACE}/sast-semgrep-scan.json'
            }
            post {
                always {
                    sh 'cat ${WORKSPACE}/sast-semgrep-scan.json'
                    
                    defectDojoPublisher(artifact: '${WORKSPACE}/sast-semgrep-scan.json', 
                        productName: 'Juice Shop', 
                        scanType: 'Semgrep JSON Report', 
                        engagementName: 'info@prim.net.pl')
                }
            }
        }
        stage('[trufflehog] Secrets Scan') {
            steps {
                sh 'trufflehog git file://. --branch=main --json  > ${WORKSPACE}/secrets-trufflehog-scanner.json'
            }
             post {
                always {
                    sh 'cat ${WORKSPACE}/secrets-trufflehog-scanner.json'
                    
                    defectDojoPublisher(artifact: '${WORKSPACE}/secrets-trufflehog-scanner.json', 
                        productName: 'Juice Shop', 
                        scanType: 'Trufflehog Scan', 
                        engagementName: 'info@prim.net.pl')
                }
            }

            
        }
        
        stage('[OSV] SCA Scan') {
            steps {
                sh 'osv-scanner scan --lockfile package-lock.json --format json --output ${WORKSPACE}/sca-osv-scanner.json || echo "OSV Scan failed with exit code $?"' 
            }
            
            post {
                always {
                    sh 'cat ${WORKSPACE}/sca-osv-scanner.json'

                    defectDojoPublisher(artifact: '${WORKSPACE}/sca-osv-scanner.json', 
                        productName: 'Juice Shop', 
                        scanType: 'OSV Scan', 
                        engagementName: 'info@prim.net.pl')
                    
                }
            }
        }

        
    }
}
