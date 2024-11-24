pipeline {
    agent any

    //tools {
    //    nodejs 'NodeJS_LTS'
    //}
    
    options {
        skipDefaultCheckout(true)
    }
    
    stages {
         stage('Set Git Configurations') {
            steps {
                script {
                    // Set the Git config values
                    sh 'git config http.postBuffer 500M'
                    sh 'git config http.maxRequestBuffer 100M'
                    sh 'git config core.compression 0'
                    
                    // Print the config values to verify
                    sh 'git config --global --list'
                }
            }
        }
        
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'moje-repo', url: 'https://github.com/MikeRossRepo/abcd-student', branch: 'main'
                }
            }
        }

        stage('Check Semgrep is installed') {
            steps {
                script {
                    sh 'semgrep --version && pwd && ls -la ./'
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
                    
                    //   defectDojoPublisher(artifact: 'sast-semgrep-scan.json', 
                 //       productName: 'Juice Shop', 
                 //       scanType: 'Semgrep JSON Report', 
                 //       engagementName: '')
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
                    
                    //   defectDojoPublisher(artifact: 'sast-semgrep-scan.json', 
                 //       productName: 'Juice Shop', 
                 //       scanType: 'Semgrep JSON Report', 
                 //       engagementName: '')
                }
            }

            
        }


        
      //  stage('[OSV] Integrity verification') {
       //     steps {
       //         sh 'npm ci'
       //     }
       // }
        
        stage('[OSV] SCA Scan') {
            steps {
                sh 'osv-scanner scan --lockfile package-lock.json --format json --output ${WORKSPACE}/sca-osv-scanner.json || echo "OSV Scan failed with exit code $?"' 
            }
            
            post {
                always {
                    sh 'cat ${WORKSPACE}/sca-osv-scanner.json'
                }
            }
        }

        
    }
}
