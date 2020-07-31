pipeline {
	agent any
	
	stages {
	    

      
      // Build the artefact
       // stage('package') {
          //  steps {
               // bat 'mvn clean compile '
          //  }
       // }
        // Munit testing
        stage('MUnit Testing') {
            steps {
                bat ' mvn clean test'
		     // publish html
        publishHTML target: [
            allowMissing: false,
            alwaysLinkToLastBuild: false,
            keepAll: true,
            reportDir: 'reports',
            reportFiles: 'summary.html',
            reportName: 'Munit Report'
          ]
            }
        }
	}
  
	
	post {
		success {
		  bat "echo 'success'"
		  // Send Success Email 
		}
		failure {
		  bat "echo 'failure'"
		  // Send Failure Email 
		}
	}
}
