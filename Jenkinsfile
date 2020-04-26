pipeline  {
        agent any
        stages {
            stage('Build Backend') {
                steps{
                    bat 'mvn clean package -DskipTests=true'
                }
            } 
            
            stage('Unit Tests') {
                steps {
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
            stage('Quality Gate') {
                steps {
                    sleep(5)
                    timeout(time: 1, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
            stage('Deploy Backend') {
                steps {
                    deploy adapters: [tomcat8(path: '', url: 'http://192.168.0.247:8080/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
                }
            }
        }
}



