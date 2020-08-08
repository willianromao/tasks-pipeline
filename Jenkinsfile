pipeline {
	agent any
	stages {
		stage ('tasks-backend: Build') {
			steps {
				bat 'mvn clean package -DskipTests=true'
			}
		}
		stage ('tasks-backend: Unit Tests') {
			steps {
				bat 'mvn test'
			}
		}
		stage ('tasks-backend: Sonar Analysis') {
			environment {
				scannerHome = tool 'SONAR_SCANNER'
			}
			steps {
				withSonarQubeEnv('SONAR_REMOTE') {
					bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=tasks-backend -Dsonar.host.url=http://52.250.57.59:9000 -Dsonar.login=f6ddd287d8e614d2856116cd494bba0ef62dfdaf -Dsonar.java.binaries=target"
				}
			}
		}
		stage ('tasks-backend: Quality Gate') {
			steps {
				sleep(5)
				timeout(time: 1, unit: 'MINUTES') {
					waitForQualityGate abortPipeline: true
				}
			}
		}
		stage ('tasks-backend: Deploy') {
			steps {
				deploy adapters: [tomcat8(credentialsId: 'tomcatadmin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', onFailure: false, war: 'target/tasks-backend.war'
			}
		}
		stage ('tasks-backend: API Test') {
			steps {
				dir('tasks-api-test') {
					git 'https://github.com/willianromao/tasks-api-test.git'
					bat 'mvn test'
				}
			}
		}
	}
}