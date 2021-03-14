pipeline {
	agent {
		label "default"
	}
	stages {
		stage('Checkout') {
			steps {
				script {
					git url: 'https://github.com/piomin/sample-spring-boot-on-kubernetes.git', credentialsId: 'github_credentials'
					sh 'ls -la'
				}
			}
		}
		stage('Deploy on test') {
			steps {
				script {
					env.PIPELINE_NAMESPACE = "test"
					kubernetesDeploy configs: 'k8s/deployment-template.yaml'
				}
			}
		}		
		stage('Build') {
			agent {
				label "maven"
			}
			steps {
				sh 'ls -la'
				sh 'mvn -version'
				sh 'mvn clean compile'
			}
		}
		stage('Test') {
			agent {
				label "maven"
			}
			steps {
				sh 'mvn test'
			}
		}
		stage('Image') {
			agent {
				label "maven"
			}
			steps {
				sh 'mvn -P jib -Djib.to.auth.username=piomin -Djib.to.auth.password=Piot_123 compile jib:build'
			}
		}
		stage('Deploy on test') {
			steps {
				script {
					env.PIPELINE_NAMESPACE = "test"
					kubernetesDeploy kubeconfigId: 'kubernetes', configs: 'k8s/deployment-template.yaml'
				}
			}
		}
		stage('Deploy on prod') {
			steps {
				script {
					env.PIPELINE_NAMESPACE = "prod"
					kubernetesDeploy kubeconfigId: 'kubernetes', configs: 'k8s/deployment-template.yaml'
				}
			}
		}
	}
}
