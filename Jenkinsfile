pipeline {

    agent any

    tools { 
        maven 'my-maven' 
    }
    environment {
        DOCKERHUB_CREDENTIALS=credentials('dockerhub')
    }
    stages {
        // stage('Scan & Review with Sonar') {
        //     steps {
        //         withSonarQubeEnv(installationName: 'my-sonar') {
        //             sh 'mvn clean package -Dmaven.test.failure.ignore=true sonar:sonar '
        //         }
        //     }
        // }
        stage('Build with Maven') {
            steps {
                sh 'mvn --version'
                sh 'java -version'
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
                stash includes : 'target/*.jar', name: 'app'
            }
        }

        stage('Package with Docker') {

            steps {
                unstash 'app' 
                sh 'ls -la'
                sh 'ls -la target'
                sh 'docker build -t khaliddinh/mysql-spring .'
                sh 'docker network create test || echo "network exists" '
                sh 'docker container stop khalid-java || echo "container does not exist" '
                sh 'echo y | docker container prune '
                sh 'docker container run -d --name khalid-java --network test -p 8081:8080 khaliddinh/mysql-spring'
            }
        }
        stage('Pushing  to DockerHub') {
            steps {
                echo 'Start pushing.. with credential'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push khaliddinh/mysql-spring'
                
            }
        }
        stage('Deploy to QA server') {
            agent{
                docker {
                    image 'khaliddinh/ansible'
                }
            }
            steps {
                echo 'Deploying and cleaning'
                sh 'ls -la '
                sh 'ls -la .ssh'
                sh 'ansible --version '
                sh 'ansible all -m shell -a "echo $HOSTNAME" '
            }
        }
    }
}
