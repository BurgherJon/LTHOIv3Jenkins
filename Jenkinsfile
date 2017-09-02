pipeline {
    agent any

    stages {
        stage('Build') 
	   {
            steps 
	      {
                echo 'Building..'
			git 'https://github.com/BurgherJon/LTHOIv3'
			sh './gradlew build'
            }
        }
        stage('Test') 
	   {
            steps 
            {
                echo 'Testing..'
            }
        }
        stage('Deploy') 
	   {
            steps 
            {
                echo 'Deploying....'
            }
        }
    }
}