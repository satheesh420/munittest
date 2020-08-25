pipeline {
     agent any
     tools {
           maven 'Maven3'
       }
	
	stages {	
	   stage('Compile'){
            steps{
                sh script: 'mvn clean compile'
            }
		}	
        // Munit testing
        stage('MUnit Testing') {
            steps {
               sh 'mvn test-compile test package'
			}
		}
	stage(' publishing Munit Reports'){
	     steps{
			sh '''
			echo " ${WORKSPACE} "
			cd ${WORKSPACE}
			mkdir -p  MunitReports
			echo " ${WORKSPACE} "
			mv "${WORKSPACE}"/target/site/munit/coverage/summary.html "${WORKSPACE}"/MunitReports/MunitReport-$BUILD_ID.html
                           '''				
			}
		}
		
	stage('Artifactory upload'){
			steps{
				def server = Artifactory.server 'Artifactory-server'

                     def uploadSpec = """{

                         "files": [

                             {

                                   "pattern": "target/${mavenPom.artifactId}-${mavenPom.version}-${mavenPom.packaging}.jar",

                                   "target": "lla-esb-snapshot",

                                 "flat": "false",

                                 "excludePatterns": ["target"]

                            }

                          ]

                     }"""

                     def buildInfo = server.upload(uploadSpec)

                     server.publishBuildInfo(buildInfo)

			}
		}
    }	
	
	post {
	  always {
		archiveArtifacts allowEmptyArchive: true, artifacts: 'MunitReports/MunitReport-${BUILD_NUMBER}.html', onlyIfSuccessful: true
		archiveArtifacts allowEmptyArchive: true, artifacts: 'report/${BUILD_NUMBER}/htmlreport.html', onlyIfSuccessful: true
		emailext attachLog: true, attachmentsPattern: 'MunitReports/MunitReport-${BUILD_NUMBER}.html,report/${BUILD_NUMBER}/htmlreport.html', 		
		body: "<h4> ${currentBuild.currentResult}: </h4> Job: <h4> ${env.JOB_NAME}</h4> build: <h4>${env.BUILD_NUMBER}</h4>", compressLog: true, subject: "Jenkins Build ${currentBuild.currentResult}", to: "sandhya.a.n@capgemini.com"
			
	    	   publishHTML target: [
            	   allowMissing: false,
            	   alwaysLinkToLastBuild: false,
            	   keepAll: true,
            	   reportDir: 'MunitReports',
		   reportFiles: 'MunitReport-${BUILD_ID}.html',
            	   reportName: 'Munit Report'
            	   ]
		}
	}
}	
