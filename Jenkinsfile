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
		stage('Newman API Test'){
                        steps{
                        	sh "newman run https://api.getpostman.com/collections/90f9a0b7-250a-4a67-b3be-1033b4e9c3a5?apikey=PMAK-5ea09512a43dd30042d2ab4c-5954bc31cc8eaafe21ab8c04a97f83e152 -e https://api.getpostman.com/environments/1bad5b00-c6aa-4d1a-97ba-0d325164189e?apikey=PMAK-5ea09512a43dd30042d2ab4c-5954bc31cc8eaafe21ab8c04a97f83e152 -n 1 --delay-request 500 --timeout-script 16000000 --insecure --reporters cli,htmlextra,json,junit --bail newman --timeout-request 18000000 --reporter-htmlextra-darkTheme --reporter-htmlextra-browserTitle 'RCI - Tests API' --reporter-htmlextra-title 'RCI - Tests API' --reporter-htmlextra-titleSize 5 --export-collection ./newman --export-environment ./newman"
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
                stage('Functional Test'){
                        steps{
                                dir('functional-test'){
                                        git credentialsId: 'github_login', url: 'https://github.com/RodrigoFragoso/tasks-functional-tests'
                                        sh 'mvn test'
                                }
                        }
                }
		stage('Deploy Prod'){
			steps{
				sh 'docker-compose build'
				sh 'docker-compose up -d'
			}
		}
	}
}
