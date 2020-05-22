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

	}
}
