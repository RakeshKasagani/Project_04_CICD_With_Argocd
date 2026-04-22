pipeline {
    agent any


       tools {
        maven 'maven'
    }

    stages {
      stage('checkout') {
            steps {
                echo 'Cloning GIT HUB Repo '
				git branch: 'main', url: 'https://github.com/RakeshKasagani/Project_04_CICD_With_Argocd.git'
            }  
        }
		
		
		
	 stage('sonar') {
            steps {
                echo 'scanning project'
                sh 'ls -ltr'
                
                sh ''' mvn sonar:sonar \\
                      -Dsonar.host.url=http://34.229.207.236:9000/ \\
                      -Dsonar.login=sqa_2f8d960b9ee91907615ad5d4620fdb15c29f1958'''
            }
    	}
		
		
		
        stage('Build Artifact') {
            steps {
                echo 'Build Artifact'
				sh 'mvn clean package'
            }
        }
		
		
		
        stage('Docker Image') {
            steps {
                echo 'Docker Image building'
				sh 'docker build -t rakesh268/mc:${BUILD_NUMBER} .'
            }
        }
		
		
       stage('Push to Dockerhub') {
            steps {
			 script {
			withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) 
			{
            sh 'docker login -u rakesh268 -p ${dockerhub}'
			
			 }
			   sh 'docker push rakesh268/mc:${BUILD_NUMBER}'
			   
           
				}
				
            }
        }
		
		
    stage('Update Deployment File') {
		
		 environment {
            GIT_REPO_NAME = "Project_4"
            GIT_USER_NAME = "RakeshKasagani"
        }
		
            steps {
                echo 'Update Deployment File'
				withCredentials([string(credentialsId: 'githubtoken', variable: 'github-token')]) 
				{
                  sh '''
                    git config user.email "rakesh@gmail.com"
                    git config user.name "rakesh"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/mc:.*/mc:${BUILD_NUMBER}/g" deploymentfiles/deployment.yml
                    git add .
                    
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"

                    git push https://${githubtoken}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
				  
                 }
				
            }
        }
		
		
			
    }

}
