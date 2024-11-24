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
                    git credentialsId: 'moje-repo', url: 'https://github.com/MikeRossRepo/abcd-student', branch: 'main'
                }
            }
        }
        stage('Example') {
            steps {
                echo 'Hello!'
                sh 'ls -la'
            }   
        }
        stage('[Semgrep] SAST Scan') {
            steps {
                sh 'semgrep scan --config auto --json-output=${WORKSPACE}/sast-semgrep-scan.json'
            }
            post {
                //always {
                 //   defectDojoPublisher(artifact: 'sast-semgrep-scan.json', 
                 //       productName: 'Juice Shop', 
                 //       scanType: 'Semgrep JSON Report', 
                 //       engagementName: '')
                //}
            }
        }


        
    }
}
