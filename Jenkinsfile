pipeline {
	agent { label 'master'}
     options { 
    skipDefaultCheckout()
    disableConcurrentBuilds()
	     timestamps()
   }
	   
    stages {
    stage('Clear workspace') {
      steps {
        cleanWs()
      }
    }  
    stage('Source Checkout') {
      steps {
	      checkout([$class: 'GitSCM', branches: [[name: '*/master']], 
        doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], 
        userRemoteConfigs: [[credentialsId: 'jvenkat255', url: 'https://github.com/jvenkat255/sonar-cobertura.git']]])
        }
    }
    
	    stage('Cobertura') {
		    steps {
			    bat "gradlew test cobertura"
			    
			    step([$class: 'JUnitResultArchiver', testResults: '**/build/test-results/test/*.xml'])
				
			    publishHTML ([
			    allowMissing: true,
			    alwaysLinkToLastBuild: false,
			    includes: '**/*.html',
			    keepAll: true,
			    reportDir: '**/build/reports/tests/test',
			    reportFiles: 'index.html',
			    reportName: 'Junit Report',
			    reportTitles: 'junit'
				])
					
			}//End post
		  }//End Jacoco
		    
		    
 stage('Sonarqube') {
    
    steps {
        withSonarQubeEnv('sonarqube_atg') {
            //bat "${scannerHome}/bin/sonar-scanner"
            bat "gradlew sonarqube"
        }
	    sleep(time:1,unit:"MINUTES")
       
    }
 }
 stage('Quality Gate') {
            steps {
              timeout(time: 2, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
             }
            }
          }
     
             
     
   
   
    
  }
 
   
} 
