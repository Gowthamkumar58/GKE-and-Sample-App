currentBuild.displayName="samplegk-app-#"+currentBuild.number
pipeline {
    agent any
     stages{
        stage('Build Docker Image'){
            steps{
                sh "sudo docker build . -t gowthamgk/samplegk-app:${BUILD_ID} "
            }
        }
        stage('DockerHub Push'){
            steps{
               withCredentials([string(credentialsId: 'gkdockerpwd', variable: 'gksample')]) {
                    sh "sudo docker login -u gowthamgk -p ${gksample}"
                    sh "sudo docker push gowthamgk/samplegk:${BUILD_ID}"
                }
            }
        }
        stage("Deploy To Kuberates Cluster"){
		steps{	
	 withCredentials([file(credentialsId: 'demo-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
         sh "gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}"
         sh "gcloud config set project mssdevops-284216"
         sh "gcloud config set compute/zone us-central1-c"
         sh "gcloud config set compute/region us-central1"
         sh "gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project mssdevops-284216"			
          /**frontend **/			
	  sh "sed -i -e 's,image_to_be_deployed,'gowthamgk/samplegk:${BUILD_ID}',g' mysql-deployment.yaml"
	  sh " kubectl apply -f mysql-deployment.yaml -n gksample"
          
          sh "sed -i -e 's,image_to_be_deployed,'gowthamgk/samplegk:${BUILD_ID}',g' mysql-service.yaml"
	  sh " kubectl apply -f mysql-service.yaml -n gksample"
		 
	  sh "sed -i -e 's,image_to_be_deployed,'gowthamgk/samplegk:${BUILD_ID}',g' testapp-deployment.yaml"
	  sh " kubectl apply -f testapp-deployment.yaml -n gksample"
		 
          sh "sed -i -e 's,image_to_be_deployed,'gowthamgk/samplegk:${BUILD_ID}',g' testapp-service.yaml"
	  sh " kubectl apply -f testapp-service.yaml -n gksample"		 
         
	
      }

    }
  } 
 }	
}
