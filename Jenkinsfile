apiVersion: apps/v1
kind: Deployment
metadata:
	name: wimb
 	labels:
 		app: wimb
spec:
	replicas:3
	strategy:
		type: Recreate
	selector:
		matchLabels:
			app: wimb
	template:
		metadata:
			labels:
				app: wimb
				tier: wimb
		spec:
			containers:
			- name: hello
			image: pjbear/hello:latest
			imagePullPolicy: Always
			ports:
			-containerPort: 5000
 			name: wimb

----

apiVersion: v1
kind: Service
metadata:
	name: wimb
	labels:
		app: wimb
spec:
	ports:
	- port: 80
	targetPort: 5000
	selector:
 		app: wimb
 		tier: wimb
 type: LoadBalancer





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
				sh "sed -i's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, 
verifyDeployments: true])
				}
			}
		}
	} 
}
