node {
	def app
	stage('Clone repository') {
		git branch: 'main',
			url: 'https://github.com/jjeongbin0826/Wimb_DevOps'
	}
	stage('Build image') {
		app = docker.build("jjeongbin0826/wimb")
	}
	stage('Push image') {
		docker.withRegistry('https://registry.hub.docker.com', 'jjeong00') {
			app.push("${env.BUILD_NUMBER}")
			app.push("latest")
		}
	}
}
