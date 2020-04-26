pipeline {
        agent any
        stages {
            stage('Build') {
                steps{
                    bat 'mvn clean package -DskipTests=true'
                }
            } 
            stage('Unit Tests') {
                steps{
                    bat 'mvn test'
                }
            }
              stage('Sonar analisys') {
                  environment {
                      scannerHome = tool 'SONAR_SCANNER'
                  }
                steps{
                    withSonarQubeEnv('SONAR_LOCAL') {
                        bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.0.247:9000 -Dsonar.login=fd580549047055a8672f25bcb93b4d1df10d87fc -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test**,**/model/**,**/Application.java"
                    }
                }
            }
    }    }
}



//-Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.0.247:9000 -Dsonar.login=fd580549047055a8672f25bcb93b4d1df10d87fc -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test**,**/model/**,**/Application.java 