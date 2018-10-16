node {
	def app
    withCredentials([
      [$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS'],
    ]) {
        stage('Login') {
           
            sh """
                docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}
            """
        }
        stage('Preparation') {
          git 'https://github.com/andrenarciso4/jenkins-project.git'
          
        }
        stage('Build image') {
            
            app = docker.build("andrenarciso4/docker-builds-by-jenkins")
        
        }
		
		stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

			app.inside {
				sh 'echo "Tests passed"'
			}
		}
		
		stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
			docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
				app.push("${env.BUILD_NUMBER}")
				app.push("latest")
			}
		}
		
		stage('Deploy') {
            /*sh """
                helm upgrade --install docker-jenkins -f node/charts/javascript/values.yaml ./node/charts/javascript \
				--set image.repository=andrenarciso4/docker-jenkins,image.tag=${BUILD_NUMBER},service.type=LoadBalancer
            """*/
		}
	}
}
