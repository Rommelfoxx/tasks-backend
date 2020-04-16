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
                withSonarQubeEnv('SONAR_LOCAL')
             //   bat "${scannerHome}/bin/sonar-scanner -e 
                 }
        
            }
    }
}

