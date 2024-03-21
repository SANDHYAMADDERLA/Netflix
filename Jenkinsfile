pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'Node16'
    }
        environment {
        SCANNER_HOME=tool 'sonar-scanner'
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
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=netflix \
                    -Dsonar.projectKey=netflix '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
                    }
                    stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'dp-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh "docker build --build-arg TMDB_V3_API_KEY=136212add169a2f0ec505aad8dbe4975 -t netflix ."
                       sh "docker tag netflix madderlasandhya/netflix:latest "
                       sh "docker push madderlasandhya/netflix:latest "
                    }
                }
            }
                      }
                     stage("TRIVY"){
            steps{
                sh "trivy image madderlasandhya/netflix:latest > trivyimage.txt"
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d --name netflix -p 8081:80 madderlasandhya/netflix:latest'
            }
        }
              }

          post{
                always {
                 emailext attachLog: true,
                 subject: "'${currentBuild.result}'",
                body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
                to: 'madderla.sandhya@gmail.com',
                attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
                    }
            }
                 
}