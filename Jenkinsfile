pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'Node16'
    }
        environment {
        SCANNER_HOME=tool 'sonarscanner'
        }
       
              stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/SANDHYAMADDERLA/netflix.git'
            }
        }
stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonarserver') {
                    sh ''' $SCANNER_HOME/bin/sonarscanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonartoken'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }

              }
}