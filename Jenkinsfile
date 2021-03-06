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
                    deploy adapters: [tomcat8(credentialsId: 'Tomcat', path: '', url: 'http://192.168.0.247:8080/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
                }
            }
            stage('API Test') {
                steps {
                    dir('api-test') {
                        git 'https://github.com/JuniorArruda/tasks-api-test'
                        bat 'mvn test'
                    }
                }
            }
             stage('Deploy Frontend') {
                steps {
                    dir('frontend') {
                        git 'https://github.com/JuniorArruda/tasks-frontend'
                        bat 'mvn clean package'
                        deploy adapters: [tomcat8(credentialsId: 'Tomcat', path: '', url: 'http://192.168.0.247:8080/')], contextPath: 'tasks', war: 'target/tasks.war'
                    }
                }
            }
            stage('Functional Test') {
                steps {
                    dir('functional-test') {
                        git 'https://github.com/JuniorArruda/tasks-functional-test'
                        bat 'mvn test'
                    }
                }
            }
            stage('Deploy Production') {
                steps {
                    bat "winscp.com betel:user549918@192.168.0.247 /command 'call echo BUILD_NUMBER=${BUILD_NUMBER} > vars'"
                    bat 'winscp.com betel:user549918@192.168.0.247 /script:winscpcmd.txt'
                }
                 steps {
                    
                }
            }
            stage('Health Check') {
                steps {
                    dir('functional-test') {
                        sleep(10)
                        bat 'mvn verify -Dskip.surefire.tests'
                    }
                }
            }

        }
        post {
            always {
                junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml, functional-test/target/surefire-reports/*.xml, functional-test/target/failsafe-reports/*.xml'
                archiveArtifacts artifacts: 'target/tasks-backend.war, frontend/target/tasks.war', onlyIfSuccessful: true
            }
            unsuccessful {
                emailext attachLog: true, body: 'See the attached log below', subject: 'Build $BUILD_NUMBER has failed', to: 'gelberajunior@gmail.com'
            }
            fixed {
                emailext attachLog: true, body: 'See the attached log below', subject: 'Build $BUILD_NUMBER is FINE', to: 'gelberajunior@gmail.com'
            }
        }
}



