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

    }
}

