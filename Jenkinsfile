pipeline{
    agent any
    stages{
        stage('Build Backend'){
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Test'){
            steps{
                bat 'mvn test'
            }
        }
      
        stage('Sonar'){
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps{

                bat 'echo on'
                withSonarQubeEnv('SONAR_LOCAL'){
                bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=577264ac4836027d3e207e4ef8866d338e997195 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                     }
                }
        
            }

            stage('Quality Gate'){
                steps{
                    sleep(20)
                    timeout(time: 1,unit:'MINUTES'){
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
            stage('Deploy Back'){
                steps{
                deploy adapters: [tomcat8(credentialsId: 'Tomcatlogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target\\tasks-backend.war'
                    }
                
            }
            stage('API Test'){
                steps{
                dir('api-test') {
                git credentialsId: 'github_login', url: 'https://github.com/Rommelfoxx/tasks-api-test'
                bat 'mvn test'
                    }
                }
            }
            stage('Deploy Front'){
                steps{
                dir('frontend') {
                git credentialsId: 'github_login', url: 'https://github.com/Rommelfoxx/tasks-frontend'
                bat 'mvn clean package -DskipTests=true'
                deploy adapters: [tomcat8(credentialsId: 'Tomcatlogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target\\tasks.war'
                    }
                
            }
        }
           stage('Fucional Test Test'){
                steps{
                dir('fucional-test') {
                git credentialsId: 'github_login', url: 'https://github.com/Rommelfoxx/tasks-functional-tests'
                bat 'mvn test'
                    }
                }
            }

            stage('Deploy Prod'){
                steps{
                    bat 'docker-compose build'
                    bat 'docker-compose up -d'
                    }
                
            }
            stage('Health Check'){

                steps{
                    sleep(10)
                    dir('fucional-test'){
                        bat 'mvn verify -Dskip.surefire.tests'
                    }
                }
            }

    }
}

