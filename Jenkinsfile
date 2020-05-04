node {
	def mvnHome
	
	stage('Poll') {
	    checkout scm
	mvnHome = tool 'M3'
	}

	stage('Build & Unit test'){
    sh "'${mvnHome}/bin/mvn' clean verify -DskipITs=true"
    junit '**/target/surefire-reports/TEST-*.xml'
    archive 'target/*.jar'
	}

	stage('Static Code Analysis'){
    sh "'${mvnHome}/bin/mvn' clean verify sonar:sonar -Dsonar.projectName=myproject -Dsonar.projectKey=myproject -Dsonar.projectVersion='${BUILD_NUMBER}'"
	}

	stage ('Integration Test'){
    sh "'${mvnHome}/bin/mvn' clean verify -Dsurefire.skip=true"
    junit '**/target/failsafe-reports/TEST-*.xml'
    archive 'target/*.jar'
	}


	stage ('Publish'){
	    def server = Artifactory.server 'Default_Artifactory_Server'
	    def uploadSpec = """{
	      "files": [
	        {
	          "pattern": "target/hello-0.0.1.war",
	          "target": "example_local_repo/${BUILD_NUMBER}/",
	          "props": "Integration-Tested=Yes;Performance-Tested=No"
	        }
	      ]
	    }"""
	  server.upload(uploadSpec)
	}

}
