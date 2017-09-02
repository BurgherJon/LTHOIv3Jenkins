pipeline {
    agent any

    stages {
        stage('Build') 
	   {
            steps 
	      {
                echo 'Checking Out..'
			git 'https://github.com/BurgherJon/LTHOIv3'
			
			echo 'Building...'
			sh './gradlew build'
            }
        }
	   stage('Reconfigure with new value for version.') 
	   {
            steps 
	      {
                echo 'Check if we need one.'
			def userInput = true
			def didTimeout = false
			try {
    				timeout(time: 10, unit: 'SECONDS') 
				{ 
        				userInput = input
					(
        					id: 'versionCheck', message: 'What is the new version number?', parameters: 
						[
        						[$class: 'TextParameterDefinition', defaultValue: '2017-08-28r0', description: 'Version', name: 'vers']
        					]
					)
    				}
			} catch(err) { // timeout reached or input false
    				def user = err.getCauses()[0].getUser()
    				if('SYSTEM' == user.toString()) { // SYSTEM means timeout.
        				didTimeout = true
    				} 
			}

	     		if (didTimeout) 
			{
                 	echo "no input, proceeding."
    			} 
			else 
			{
        			// do something else
        			echo "this is where we'd do the steps."
    			} 

            }
        }



        stage('Copy Prod Data to Test') 
	   {
            steps 
            {
                echo 'Login the prod service account.'
			sh 'gcloud auth activate-service-account jenkins@leavethehouseoutofit.iam.gserviceaccount.com --key-file=/home/bitnami/downloads/LeaveTheHouseOutOfIt-fac0b08781ca.json'

			echo 'Switch to the prod project.'
			sh 'gcloud config set project leavethehouseoutofit'

			echo 'Copy SQLDump to test storage bucket.'
			sh 'gcloud sql instances export lthoidb gs://lthoi-test.appspot.com/sqldumpfile.gz --database lthoidb'

			echo 'Login the test service account.'
			sh 'gcloud auth activate-service-account jenkins2@lthoi-test.iam.gserviceaccount.com --key-file=/home/bitnami/downloads/lthoi-test-89d068b955b7.json'

			echo 'Switch to the test project.'
			sh 'gcloud config set project lthoi-test'

			echo 'Import the database.'
			sh 'gcloud sql instances import lthoidb gs://lthoi-test.appspot.com/sqldumpfile.gz --quiet'
            }
        }
	   stage('Test Deploy') 
	   {
            steps 
            {
                echo 'Deploying to Test as checked in.'
			sh './gradlew appengineDeploy'
            }
        }
        
    }
}