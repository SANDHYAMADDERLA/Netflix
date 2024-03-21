pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'Node16'
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
    }
}
