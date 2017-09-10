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
	   stage('Reconfigure') 
	   {
            steps 
	      {
       	 	configs = readYaml file: "./jenkinsconfig.yaml"
			if (configs.CreateNewApi == true)
			{
				echo 'Do it.'
			}
			else
			{
				echo 'Don't.'
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