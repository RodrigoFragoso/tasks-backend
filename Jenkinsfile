pipeline {
	agent any
	stages {
		stage ('Build Backend'){
			steps{
				sh 'mvn clean package -DskipTests=true'
			}
		}
		stage ('Unit Tests'){
			steps{
				sh 'mvn test'
			}
		}
		stage ('Sonar Analysis Tests'){
                        environment{
				scannerHome = tool 'SONAR_SCANNER'
			}
			steps{
                                withSonarQubeEnv('SONAR_LOCAL'){
					sh '${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.25.14:9000 -Dsonar.login=dbbd49c50eda140b76aa1a2384760a92234283ea -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java'
				}
                        }
                }
		stage ('Quality Gate'){
			steps{
				sleep(10)
				timeout(time: 1, unit: 'MINUTES'){
					waitForQualityGate abortPipeline: true
				}
			}
		}
		stage('Deploy Backend'){
			steps{
				deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://192.168.25.14:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
			}
		}
                stage('API Test'){
                        steps{
				dir('api-test'){
	                        	git credentialsId: 'github_login', url: 'https://github.com/RodrigoFragoso/tasks-api-test'
					sh 'mvn test'
				}
			}
                }
		stage('Deploy Frontend'){
			steps{
				dir('frontend'){
					git credentialsId: 'github_login', url: 'https://github.com/RodrigoFragoso/tasks-frontend'
					sh 'mvn clean package'
					deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://192.168.25.14:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
					sh 'echo frontend'				
				}
			}
		}

		

	}
}
