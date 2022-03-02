
pipeline{

	 environment {
    	registry = "siriwut/eks-jenkins-demo" // repo docker hub
    	registryCredential = 'dockerhub-siriwut' // id ของ user name password ที่สร้างขึ้นมา
    	dockerImage = ''
		region = "ap-southeast-1"
		clusterName  = "arm-devops-labs" // ชื่อ cluster ใน aws eks
		eksProfile = "aws-arm" // ใช้ชื่อที่ตั้งใน jenkins
  	}

	agent any
	
	stages {
		stage('Build') {
			steps {
				script {
					dockerImage = docker.build registry + ":$BUILD_NUMBER"
				}   
			}
		}
  

		stage('Push image') {
			steps {
				script {
            		docker.withRegistry( '', registryCredential ) {
						dockerImage.push()
					}
				}
			}
		}
        
		stage('Remove Unused docker image') {
     		steps{
        		sh "docker rmi $registry:$BUILD_NUMBER"
     	 	}
   		}

		stage('Deploy'){
			steps {
				withAWS(credentials: "$eksProfile", region: 'ap-southeast-1') {
					sh "aws iam list-account-aliases"
					sh "aws eks --region $region update-kubeconfig --name $clusterName"
					sh 'kubectl get pods'
					sh 'kubectl get nodes'
					sh 'kubectl apply -f eks-deploy-example.yaml'
				}
			}
		}

		// stage('Deploy') {
		// 	steps {
		// 		sh 'echo Hello World'
		// 		sh 'kubectl get pods'
        //         // sh "sed -i 's/hellonodejs:latest/hellonodejs:eks/g' deploy.yaml"
        //         // sh 'kubectl apply -f deploy.yaml'
        //         // sh 'kubectl rollout restart deployment hello-world-nodejs'
		// 	}
		// }

        // stage('eks deploy') {
		// 	steps {
		// 		sh 'echo Hello World'
		// 		sh 'kubectl get pods'
        //         // sh "sed -i 's/hellonodejs:latest/hellonodejs:eks/g' deploy.yaml"
        //         // sh 'kubectl apply -f deploy.yaml'
        //         // sh 'kubectl rollout restart deployment hello-world-nodejs'
		// 	}
		// }
	}


}

