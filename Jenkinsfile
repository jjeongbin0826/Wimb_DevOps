pipeline {
	agent any
	environment {
		PROJECT_ID = 'opensource-442406'
		CLUSTER_NAME = 'k8s'
		LOCATION = 'asia-northeast3-a'
		CREDENTIALS_ID = '91e1030d-caa1-4185-a8e3-ba91cbe448e4'
	}
	stages {
		stage("Checkout code") {
			steps {
				checkout scm
			}
		}
		stage("Build image") {
			steps {
				script {
					myapp = docker.build("jjeongbin0826/wimb:${env.BUILD_ID}")
				}
			}
		}
		stage("Push image") {
			steps {
				script {
					docker.withRegistry('https://registry.hub.docker.com', 'jjeong00') {
						myapp.push("latest")
						myapp.push("${env.BUILD_ID}")
					}
				}
			}
 		}
		stage('Deploy to GKE') {
			when {
				branch 'main'
			}
			steps{
				sh "sed -i's/wimb:latest/wimb:${env.BUILD_ID}/g' deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, 
verifyDeployments: true])
			}
		}
	}
}
