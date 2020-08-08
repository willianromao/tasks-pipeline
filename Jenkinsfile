pipeline {
	agent any
	stages {
		stage ('tasks-backend: Build') {
			steps {
				dir('tasks-backend') {
					git 'https://github.com/willianromao/tasks-backend.git'
					bat 'mvn clean package -DskipTests=true'
				}
			}
		}
		stage ('tasks-backend: Unit Tests') {
			steps {
				dir('tasks-backend') {
					bat 'mvn test'
				}
			}
		}
		stage ('tasks-backend: Sonar Analysis') {
			environment {
				scannerHome = tool 'SONAR_SCANNER'
			}
			steps {
				withSonarQubeEnv('SONAR_REMOTE') {
					dir('tasks-backend') {
						bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=tasks-backend -Dsonar.host.url=http://52.250.57.59:9000 -Dsonar.login=f6ddd287d8e614d2856116cd494bba0ef62dfdaf -Dsonar.java.binaries=target"
					}
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
				dir('tasks-backend') {
					deploy adapters: [tomcat8(credentialsId: 'tomcatadmin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', onFailure: false, war: 'target/tasks-backend.war'
				}
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
		stage ('tasks-frontend: Build') {
			steps {
				dir('tasks-frontend') {
					git 'https://github.com/willianromao/tasks-frontend.git'
					bat 'mvn clean package -DskipTests=true'
				}
			}
		}
		stage ('tasks-frontend: Unit Tests') {
			steps {
				dir('tasks-frontend') {
					bat 'mvn test'
				}
			}
		}
		stage ('tasks-frontend: Sonar Analysis') {
			environment {
				scannerHome = tool 'SONAR_SCANNER'
			}
			steps {
				withSonarQubeEnv('SONAR_REMOTE') {
					dir('tasks-frontend') {
						bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=tasks-frontend -Dsonar.host.url=http://52.250.57.59:9000 -Dsonar.login=96aef74ef8014ee3ba36bc19c28809677f0cd731 -Dsonar.java.binaries=target"
					}
				}
			}
		}
		stage ('tasks-frontend: Quality Gate') {
			steps {
				sleep(5)
				timeout(time: 1, unit: 'MINUTES') {
					waitForQualityGate abortPipeline: true
				}
			}
		}
		stage ('tasks-frontend: Deploy') {
			steps {
				dir('tasks-frontend') {
					deploy adapters: [tomcat8(credentialsId: 'tomcatadmin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', onFailure: false, war: 'target/tasks.war'
				}
			}
		}
		stage ('tasks-frontend: Funcional Tests') {
			steps {
				dir('tasks-funcional-tests') {
					git 'https://github.com/willianromao/tasks-functional-tests.git'
					bat 'mvn test'
				}
			}
		}
		stage ('Production Environment') {
			steps {
				bat 'docker-compose up -d --build'
			}
		}
	}
}