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
        stage('Copy Prod Data to Test') 
	   {
            steps 
            {
                echo 'Switch to the prod project.'
			sh 'gcloud config set project leavethehouseoutofit'

			echo 'Copy SQLDump to test storage bucket.'
			sh 'gcloud sql instances export lthoidb gs://lthoi-test.appspot.com/sqldumpfile.gz --database lthoidb'

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