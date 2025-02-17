def app
pipeline {
	agent any
	environment {
		REGISTRY_URL = 'https://docker.io'
		DOCKER_IMAGE = "yashds03/portfolio"
		VERSION = "${env.BUILD_ID}-${env.GIT_COMMIT}"
		PORTAINER_WEBHOOK = 'https://portainer.koltserver.net/api/stacks/webhooks/c157f205-6e4a-488e-a9d9-15c6b921efa9'
	}

	stages {

		stage('Verify dependencies') {
			steps {
				script {
					sh 'curl --version'
					sh 'docker --version'
				}
			}
		}

		stage('Checkout') {
			steps {
				cleanWs()
				git branch: 'main', url: 'https://github.com/YD-S/Portfolio.git'
			}
		}

		stage('Build') {
			steps {
				script {
					app = docker.build("${REGISTRY_URL}/${DOCKER_IMAGE}:latest")
				}
			}
		}

		stage('Push to Registry') {
			steps {
				script {
					docker.withRegistry("${REGISTRY_URL}", 'dockerhub') {
						app.push("latest")
						app.push("${VERSION}")
					}
				}
			}
		}

		stage('Deploy') {
			steps {
				script {
					sh "curl -X POST ${PORTAINER_WEBHOOK}"
				}
			}
		}
	}
}
