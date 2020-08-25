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
				script{
			def mavenPom = readMavenPom file:'pom.xml'
			def server = Artifactory.server 'Artifactory-server'
                     def uploadSpec = """{

                         "files": [

                             {  "pattern": "./${mavenPom.artifactId}-${mavenPom.version}-${mavenPom.packaging}.jar",

                                 "target": "lla-esb-snapshot/${mavenPom.groupID}/${mavenPom.artifactId}/${mavenPom.version}",

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
    }	
	
	
}	
