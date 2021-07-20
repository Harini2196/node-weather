pipeline{
    agent any
    tools{
        nodejs 'nodebuild'
    }
    stages{
        stage('Git Clone'){
            steps{
                git 'https://github.com/Harini2196/node-weather.git'
            }
        }
         stage('build'){
            steps{
                sh 'npm --v'
               // sh 'node --v'
                sh 'npm install'
            }
        }
        stage('quality gates'){
              environment {
                scannerHome = tool 'sonarScanner'
             }
            steps{
                withSonarQubeEnv(installationName:'sonar-server', credentialsId: 'sonar') {
                       sh "${scannerHome}/bin/sonar-scanner -Dsonar.sources=./ -Dsonar.language=js -Dsonar.projectKey=node-nagp -Dsonar.projectName=NodeApplication -Dsonar.projectVersion=1.0.0"
                }
            }
        }
        stage ('Login to deployment server')
        {
            steps{
                sshagent(['centos-slave']) {
                    script {
                    sh 'ssh -o StrictHostKeyChecking=no centos@52.87.210.104'
                    sh 'rm -rf Nodejs-proj'
                    }
                }
           }
        }
        stage('deploy to server'){
            steps{
                sshagent(['centos-slave']) {
                    script {
                        sh 'rsync -av -e ssh  --exclude=.git/ $JENKINS_HOME/workspace/nodejs-proj centos@52.87.210.104:~/Nodejs-proj'
                       // sh 'scp -ro StrictHostKeyChecking=no $JENKINS_HOME/workspace/nodejs-proj centos@52.87.210.104:~/Nodejs-proj'
                        sh 'ssh -o StrictHostKeyChecking=no centos@52.87.210.104 cd Nodejs-proj/ &&  node ./bin/www'
                       
                    }
                }
            }
        }
    }
}
