pipeline {
    agent any 
    environment {
        DB_LOC = credentials("CB_DB_LOC")
        UTOPIA_DB_UNAME = credentials("CB-DB-USER")
        UTOPIA_DB_PASS = credentials("CB_DB_PASS")
        AWS_CLI = '/usr/local/bin/aws'
    }
    stages {
        stage('init') {
            steps {
                sh 'rm -r CICD-practice'
                sh 'git clone https://github.com/Colin-Bradshaw/CICD-practice.git'
            }
        }
        stage('build and scan') { 
            steps {
                // booking
                dir ('CICD-practice'){
                    withSonarQubeEnv('SonarQubeScanner'){
                        sh 'mvn verify sonar:sonar -Dsonar.host.url=http://jenkins.hitec.link:9000/ -Dsonar.login=dbe54ee91fab45705e2a7ccbdb2a3ef0587cad48'
                    }
                    sh 'mvn clean install > maven-build.txt'
                }
            }
        }
        stage('dockerize') { 
            steps {
                // booking
                dir ('CICD-practice'){
                    sh 'docker build -t "jenkins-auto-build-booking:latest" .'
                }
                
            }
        }
        stage('push to ecr') { 
            steps {
                withAWS(credentials: 'Jenkins', region: 'us-east-2') {
                    sh '$AWS_CLI ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 902316339693.dkr.ecr.us-east-2.amazonaws.com'
                    sh 'docker tag jenkins-auto-build-booking:latest 902316339693.dkr.ecr.us-east-2.amazonaws.com/cb-booking:jenkins-auto-build-booking'
                    sh 'docker push 902316339693.dkr.ecr.us-east-2.amazonaws.com/cb-booking:jenkins-auto-build-booking'
                }
            }
        }
    }
}






